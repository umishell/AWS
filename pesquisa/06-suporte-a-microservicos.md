# 6. Tecnologias da AWS que dão suporte a microsserviços

Documento de pesquisa para a pergunta “quais dessas tecnologias dão suporte para microsserviços? Cite as tecnologias, para que servem e como suportam microsserviços.” Não é roteiro de slides.

Os serviços estão espalhados em `servicos-aws.md` (seções 2, 3, 6, 9 e 10). O que faltava é a ligação: o que é o estilo arquitetural, qual problema cada tecnologia resolve dentro dele, e como resolve. Uma lista de nomes com “serve para microsserviços” não responde a pergunta.

## O que é um microsserviço

Uma aplicação monolítica põe catálogo, pedido, pagamento e notificação no mesmo processo, no mesmo deploy e, em geral, no mesmo banco. Microsserviço parte isso em processos separados. Cada um:

- implementa uma capacidade de negócio (pedido, estoque, notificação);
- é implantado sozinho, sem republicar os outros;
- escala sozinho;
- é dono dos seus dados;
- fala com os outros pela rede (HTTP ou mensagem).

O ganho é independência: o time de pagamento publica sem esperar o time de catálogo, e um pico de notificação não exige crescer o catálogo. O custo é operacional. Passam a existir descoberta de endereço, falha parcial, mensagem duplicada, rastreio de um pedido que atravessa cinco serviços e uma conta de nuvem com dezenas de pedaços. A AWS entra nesse custo. Ela não “transforma o monólito em microsserviço”. Ela oferece as peças para quem já decidiu partir o sistema.

Mercado Livre (milhares de serviços em EKS) e o middleware financeiro do iFood (EventBridge, SQS, EKS) são esse estilo em produção. Os cases estão em `05-exemplos-de-aplicacoes.md`.

## O que a plataforma precisa sustentar

| Necessidade | Por que aparece quando o sistema é partido |
| --- | --- |
| Computação por serviço | Cada serviço tem ciclo de vida e tamanho próprios |
| Imagem e implantar | A versão sobe sem mexer na versão do vizinho |
| Porta de entrada | O cliente fala com um endereço só, não com vinte serviços internos |
| Comunicação solta | Um serviço lento não segura a thread do outro |
| Orquestração | Alguns fluxos têm ordem, espera e compensação |
| Descoberta e rede | O endereço de uma tarefa muda quando ela é recriada |
| Dado por serviço | Compartilhar a mesma tabela acopla o deploy de novo |
| Identidade | Cada serviço chama a AWS e os outros com permissão mínima |
| Observabilidade | O sintoma aparece num serviço e a causa em outro |
| Escala | O serviço quente cresce sem replicar o sistema inteiro |

As tecnologias abaixo estão agrupadas por essa necessidade. “Para que serve” é o serviço em geral. “Como suporta” é o papel dentro do estilo.

## Computação: onde cada serviço roda

### AWS Lambda

**Para que serve.** Executar código em resposta a evento, sem provisionar servidor. A explicação geral está na seção 2 de `servicos-aws.md`.

**Como suporta microsserviços.** Cada função pode ser um serviço, ou uma ação de um serviço, com deploy próprio. A escala é por invocação: o serviço de nota fiscal cresce no fechamento do mês e o de cadastro não cresce junto. Integrada à fila, a função é o consumidor. O teto de 15 minutos e a ausência de disco permanente limitam o tipo de serviço: vale para API curta e para worker. Não vale para um processo que precisa ficar residente com estado em memória o dia inteiro.

### Amazon ECS e AWS Fargate

**Para que serve.** ECS agenda contêineres. Uma *task* descreve a imagem, a CPU e a memória. Um *service* mantém N cópias no ar, atrás de um balanceador. Fargate é o modo em que a pessoa não administra as instâncias do cluster.

**Como suporta microsserviços.** O contêiner é a unidade de deploy. O serviço “pedido” e o serviço “estoque” são dois services, com imagem, escala e pipeline próprios, podendo dividir o mesmo cluster. Se o pedido precisa de duas tarefas a mais no almoço, o Auto Scaling do service mexe só nele. Fargate tira o trabalho de operar a máquina, que em sistema partido se multiplica. ECS em EC2 continua válido quando a equipe quer controlar o nó (GPU, disco local, reserva de capacidade).

### Amazon EKS

**Para que serve.** Kubernetes gerenciado. A AWS opera o control plane. A pessoa opera os nós, ou usa Fargate, ou o EKS Auto Mode.

**Como suporta microsserviços.** Kubernetes já é um orquestrador de vários serviços: Deployment por serviço, Service para o endereço estável, Horizontal Pod Autoscaler para a escala, namespace para isolar time. EKS coloca esse modelo na AWS, com IAM, balanceador e VPC. Mercado Livre usa EKS na plataforma Fury por isso. O custo é a curva do Kubernetes. Para um sistema de quatro serviços e um time pequeno, ECS/Fargate ou Lambda sustentam o mesmo estilo com menos plataforma.

### AWS App Runner e Amazon ECR

**App Runner** publica um contêiner ou um repositório como serviço web com HTTPS e escala, com menos controle que o ECS. Sustenta o caso simples: cada microsserviço HTTP vira um serviço App Runner.

**ECR** guarda a imagem. Sem um registro privado, não há versão estável para o ECS ou o EKS puxar. A varredura de vulnerabilidade da imagem é parte de publicar serviço com segurança. O ECR não executa nada. Ele torna o deploy reproduzível.

### EC2, balanceador e Auto Scaling

Ainda sustentam microsserviço. Cada serviço é um grupo de instâncias atrás de um Application Load Balancer, com Auto Scaling próprio. É o desenho anterior ao contêiner, e continua correto para sistema que já é uma máquina virtual por serviço. O que ele exige a mais é a pessoa operar sistema operacional, patch e AMI. ECS e Lambda existem para reduzir essa operação, não porque EC2 “não sirva”.

## Porta de entrada

### Amazon API Gateway

**Para que serve.** Receber HTTP ou WebSocket, autenticar (IAM, Cognito ou Lambda authorizer), aplicar cota e encaminhar.

**Como suporta microsserviços.** O aplicativo do cliente conhece um hostname e um contrato. Por trás, `/pedidos` vai para um serviço e `/cardapio` para outro. Trocar o serviço de Lambda para ECS não muda o cliente, se o contrato permanecer. Throttle protege um serviço pequeno de um pico que o derrubaria e, por tabela, derrubaria os vizinhos se eles compartilhassem processo.

### Application Load Balancer

**Para que serve.** Repartir HTTP/HTTPS por caminho e por host entre contêineres, instâncias ou Lambda.

**Como suporta microsserviços.** É a porta de entrada quando os serviços são contêineres na VPC. Regras de path mandam `/pagamentos` para o target group daquele service e o resto para outro. Health check tira do rodízio só a tarefa doente. O API Gateway faz o mesmo papel com mais recurso de “produto de API”. Os dois podem coexistir: CloudFront na borda, API Gateway para a API pública, balanceador para o tráfego interno.

### Amazon Route 53 e Amazon CloudFront

Route 53 dá um nome estável por serviço ou para a borda única. CloudFront coloca cache, TLS e WAF na frente. Sustentam o estilo ao esconder quantos serviços existem lá atrás e ao absorver tráfego de leitura (catálogo, imagem) antes que ele chegue no serviço.

## Comunicação entre serviços

Chamada HTTP síncrona acopla disponibilidade: se estoque está lento, pedido está lento. Fila e evento existem para cortar esse acoplamento. A tabela de escolha já está na seção 10 de `servicos-aws.md`. Aqui o foco é o suporte ao estilo.

### Amazon SQS

**Para que serve.** Fila. Um produtor envia, um consumidor processa, a mensagem fica até 14 dias se o consumidor cair. Fila padrão privilegia vazão. Fila FIFO privilegia ordem e deduplicação.

**Como suporta microsserviços.** O serviço de pedido grava o pedido e publica “pedido criado” na fila. O serviço de nota consome quando puder. Se a nota estiver fora, o pedido do cliente não falha: a mensagem espera. O *visibility timeout* impede que dois consumidores processem a mesma mensagem ao mesmo tempo. A *dead-letter queue* tira da frente a mensagem que já falhou N vezes, para um erro de dado não travar a fila inteira. Cada serviço pode ter a sua fila. Escalar consumidores esvazia a fila mais rápido, e essa escala é independente do produtor.

### Amazon SNS

**Para que serve.** Um tópico, vários assinantes.

**Como suporta microsserviços.** Um fato interessa a mais de um serviço. “Pedido pago” precisa acordar estoque, nota e e-mail. SNS entrega a mesma notícia aos três. O desenho que segura isso em produção é SNS para as filas SQS, uma fila por assinante: se o e-mail estiver fora, estoque continua consumindo a fila dele. Sem a fila no meio, um assinante HTTP lento segura o fan-out.

### Amazon EventBridge

**Para que serve.** Barramento. Produtores publicam eventos; regras filtram pelo conteúdo e encaminham para Lambda, SQS, Step Functions ou API. Também agenda tarefa (Scheduler) e liga origem, filtro e destino (Pipes).

**Como suporta microsserviços.** O produtor não conhece a lista de consumidores. Um serviço novo passa a reagir a “pedido criado” com uma regra nova, sem novo deploy de quem criou o pedido. Isso é o que o iFood descreveu ao adotar o barramento no middleware financeiro: a falha de um serviço deixa de se propagar pela cadeia de chamadas síncronas. O schema registry guarda o formato do evento para um time não quebrar o outro ao mudar um campo.

### AWS Step Functions

**Para que serve.** Máquina de estado. Passos chamam Lambda, ECS, Batch ou quase qualquer API da AWS, com retry, ramo e paralelismo. Standard para fluxo longo. Express para volume alto e curta duração.

**Como suporta microsserviços.** Nem todo fluxo deve ser coreografia solta (cada serviço reage a um evento e ninguém vê o processo inteiro). Cobrar, emitir nota e estornar se a nota falhar é um processo com dono. Step Functions é esse dono: orquestra serviços que continuam separados. A diferença prática para o EventBridge: EventBridge anuncia fatos e quem quiser escuta; Step Functions conduz uma instância de processo do começo ao fim.

### Amazon MQ, MSK e Kinesis

**Amazon MQ** (ActiveMQ ou RabbitMQ gerenciado) suporta o serviço que já fala AMQP ou JMS e não vai ser reescrito para SQS.

**Amazon MSK** é Kafka gerenciado. Sustenta o mesmo papel da fila e do log de eventos quando a empresa já padronizou Kafka. Nubank e o case do iFood usam Kafka nesse lugar. Kinesis faz streaming nativo da AWS (clique, telemetria) com vários consumidores do mesmo fluxo.

Para sistema novo desenhado na AWS, SQS, SNS e EventBridge são o caminho mais curto. MQ e MSK entram por compatibilidade com o que já existe.

## Descoberta e rede entre serviços

### AWS Cloud Map

**Para que serve.** Registro de serviços: nome para o endereço atual de uma task ou instância.

**Como suporta microsserviços.** Tarefa de contêiner ganha IP novo quando morre e outra nasce. O chamador não pode ter esse IP escrito em arquivo. Cloud Map, ou o DNS do Kubernetes no EKS, devolve o endereço atual. Sem descoberta, o deploy independente quebra a malha de chamadas.

### Amazon VPC Lattice

**Para que serve.** Conectar serviços em VPCs e contas diferentes, com autenticação IAM e política, sem um peering artesanal por par de serviços.

**Como suporta microsserviços.** Quando cada time tem conta própria (o caso de empresa grande, e o que o Nubank descreve como várias contas), o serviço A precisa chamar o serviço B sem abrir a rede inteira. Lattice é a proposta atual da AWS para esse tráfego leste-oeste.

### AWS App Mesh

Malha de serviços baseada em Envoy: retry, timeout, mTLS e canário fora do código de cada serviço. O catálogo já registra que o App Mesh está em modo de manutenção. Para apresentação, citar como peça histórica de service mesh e não como escolha nova. Em Kubernetes, o ecossistema usa outras malhas, e na AWS o VPC Lattice cobre parte do problema de conectividade.

### Security groups, PrivateLink e IAM

O security group libera só a porta do serviço chamador para o serviço chamado. PrivateLink expõe um serviço a outra VPC sem tornar as redes roteáveis uma para a outra. A *role* IAM de cada task ou função carrega só as ações daquele serviço (`sqs:SendMessage` nesta fila, `dynamodb:PutItem` naquela tabela). Isso é least privilege aplicado ao estilo: partir a aplicação sem partir a permissão recria um monólito de credencial.

## Dado

Microsserviço que lê e grava na mesma tabela do vizinho volta a ser um monólito na hora do schema: uma migração derruba os dois.

| Tecnologia | Para que serve | Como suporta |
| --- | --- | --- |
| **DynamoDB** | Chave-valor gerenciado | Cada serviço tem as suas tabelas. Escala sem um servidor de banco compartilhado. Streams avisam outro serviço de que o item mudou, o que substitui um trigger olhando tabela alheia |
| **RDS ou Aurora** | Relacional gerenciado | O padrão compatível com o estilo é um banco por serviço, ou ao menos um schema isolado com dono único. Um RDS compartilhado com dezenas de serviços é possível e é um atalho que reacopla o deploy |
| **ElastiCache** | Cache | Cache do serviço, não cache global sem dono. Sessão e resposta quente ficam perto do serviço que a produz |
| **S3** | Objeto | Arquivo daquele serviço (nota em PDF, imagem). Outro serviço recebe o id ou o evento, não vasculha o bucket alheio |
| **Secrets Manager** | Segredo | Cada serviço lê a própria senha em execução |

## Identidade do usuário e da plataforma

**Amazon Cognito** autentica o usuário final e entrega token. O API Gateway valida o token. Isso é a borda.

**IAM roles** autenticam o serviço na plataforma. A função do serviço de pedido não usa a chave de acesso de uma pessoa. Ela assume uma role. Conta cruzada usa a mesma ideia. Organizações com várias contas (Control Tower, Organizations) isolam um domínio de negócio inteiro: um time não altera o serviço do outro porque nem está na mesma conta.

## Observabilidade e entrega

### Amazon CloudWatch e AWS X-Ray

CloudWatch recebe métrica e log **por serviço**. Alarme de erro do pagamento não se mistura com CPU do catálogo. X-Ray (e o Application Signals, no mesmo espírito de traço) coloca um id na requisição e mostra a cascata: o tempo foi no serviço de estoque, não no de pedido. Sem isso, microsserviço em produção vira caixa-preta. A explicação dos dois está nas seções 8 e 9 do catálogo.

### ECR, CodePipeline, CodeDeploy, CloudFormation e CDK

Cada serviço tem pipeline: a imagem sobe ao ECR, o pipeline publica só aquele service (rolling, canário ou blue/green no CodeDeploy). CloudFormation ou CDK descreve a fila, a tabela e a função como stack daquele serviço. O deploy independente, que é a definição operacional de microsserviço, depende dessas peças. Sem pipeline por serviço, a equipe volta a publicar “o sistema” por medo de quebrar o vizinho.

### Auto Scaling por serviço

EC2 Auto Scaling, service auto scaling do ECS (CPU ou profundidade da fila SQS) e a escala automática do Lambda fazem a mesma coisa em runtimes diferentes: a unidade que cresce é o serviço. Profundidade da fila é o sinal certo para worker. CPU é o sinal certo para API. Escalar os dois pelo mesmo alarme reacopla o que foi partido.

## Um fluxo, para amarrar

Pedido num aplicativo, com os serviços partidos:

1. O celular chama **API Gateway**, com token do **Cognito**.
2. **Lambda** (ou uma task **ECS/Fargate**) do serviço de pedido grava em **DynamoDB** e publica um evento no **EventBridge**.
3. Uma regra entrega o evento a uma fila **SQS** do serviço de cozinha e a outra fila do serviço de notificação.
4. Cada consumidor escala com a própria fila. Se a notificação cair, a mensagem fica, e a cozinha segue.
5. **Step Functions** entra só se houver um processo com compensação, por exemplo pagamento que precisa estornar.
6. **X-Ray** mostra a cadeia. **IAM** de cada função só alcança a própria tabela e a própria fila. **Cloud Map** ou o balanceador resolve o endereço se um serviço chamar o outro por HTTP.

Esse fluxo usa quase todas as tecnologias da resposta. Tirar uma delas não impede o estilo, desde que o papel continue existindo (outro runtime, outra fila, outro registro).

## O que citar na fala, e o que deixar na reserva

Para 3 ou 4 minutos, cinco nomes fecham a pergunta:

| Tecnologia | Para que serve | Como suporta o microsserviço |
| --- | --- | --- |
| **Lambda ou ECS/Fargate** | Rodar o código | Um deploy e uma escala por serviço |
| **API Gateway ou ALB** | Receber o cliente | Um contrato na frente, vários serviços atrás |
| **SQS e EventBridge** | Passar trabalho e fato | O serviço não cai junto com o vizinho |
| **DynamoDB ou um banco por serviço** | Guardar estado | O schema de um não trava o deploy do outro |
| **CloudWatch e X-Ray** | Ver a cadeia | A falha parcial tem dono |

EKS, SNS, Step Functions, ECR e IAM entram se alguém perguntar “e Kubernetes?”, “e se vários precisarem do mesmo fato?”, “e um fluxo com estorno?”. App Mesh fica de fora da recomendação.

## Fontes

- `servicos-aws.md`, seções 2, 3, 6, 9 e 10.
- AWS Prescriptive Guidance, integração de microsserviços com serviços serverless (Lambda, API Gateway, SQS, SNS, EventBridge): https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/welcome.html
- Documentação do Lambda sobre arquitetura orientada a eventos: https://docs.aws.amazon.com/lambda/latest/dg/concepts-event-driven-architectures.html
- Cases que ilustram o estilo em produção: Mercado Livre e iFood, com as URLs em `05-exemplos-de-aplicacoes.md`.

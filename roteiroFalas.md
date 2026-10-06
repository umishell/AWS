# Roteiro de Falas — AWS
### Leia em voz alta durante a apresentação · ~20 minutos

> Cada seção corresponde a um slide. O número do slide está no título.
> Fale no ritmo que o slide pede — os blocos mais curtos são de transição, os mais longos têm mais conteúdo visual para apontar enquanto fala.

---

## Slide 0 — Capa

Bom dia. Hoje a apresentação é sobre a Amazon Web Services, a AWS. Vou responder seis perguntas em ordem: o que ela é, quais são os principais serviços, como a IA da plataforma funciona, como a cobrança é feita, exemplos de aplicações reais hospedadas lá e, por último, quais tecnologias dão suporte a microsserviços e como fazem isso. Algumas perguntas rendem uma tabela porque o catálogo é grande — nesses momentos vou comentar os itens mais importantes em vez de ler tudo.

---

## Slide 1 — O que é a AWS

A Amazon Web Services é a plataforma de computação em nuvem da Amazon. Ela começou em 2006 com dois serviços: o S3, para guardar arquivo, e o EC2, para ligar uma máquina virtual na internet. A origem foi prática — a própria Amazon precisava provisionar infraestrutura rápido para crescer a loja, transformou esse processo em produto e passou a vender para qualquer pessoa ou empresa, no tamanho que a carga precisar.

Hoje o catálogo passa de 200 serviços e a AWS opera em 39 regiões geográficas com 124 zonas de disponibilidade ao redor do mundo. A receita anual em 2025 ficou em torno de 129 bilhões de dólares. Em participação de mercado de infraestrutura de nuvem, a AWS tem cerca de 28%, a Microsoft Azure 20% e o Google Cloud 15% — segundo levantamento do segundo trimestre de 2026.

Os serviços se dividem em três modelos. IaaS é a camada mais baixa: você aluga máquina, rede e disco, e instala tudo em cima — EC2, VPC e EBS ficam aqui. PaaS é o próximo nível: o runtime, o banco ou o orquestrador já vêm operados pela AWS, e você entrega código ou configuração — Lambda, RDS e Fargate são exemplos. Há ainda alguns produtos SaaS — contact center, e-mail corporativo — mas são minoria. A maior parte do que se chama "AWS" no dia a dia é IaaS e PaaS.

---

## Slide 2 — Por que usar a AWS

O argumento econômico é direto: em vez de comprar servidor, montar sala, pagar energia e manter equipe de data center o ano inteiro, você paga a hora da máquina e o gigabyte guardado. Quando a demanda cai, você desliga e para de pagar. O argumento operacional é tempo: provisionar um servidor em 2005 levava dias ou semanas. Na AWS leva minutos.

A escala acompanha automaticamente. Você define um mínimo e um máximo de instâncias, coloca uma regra — se a CPU passar de 70%, sobe mais uma máquina —, e a plataforma cuida do resto.

Um ponto importante que a AWS chama de responsabilidade compartilhada: a AWS cuida da segurança *da* nuvem — o prédio, o hardware, o hypervisor. Você cuida da segurança *na* nuvem — sistema operacional da instância, configuração do banco, políticas de acesso, dados, patches da aplicação. A fatura do recurso esquecido ligado também é sua.

Para quem está no Brasil, a região de São Paulo tem o código sa-east-1. Usar essa região mantém o dado no país e reduz a latência. O preço costuma ser um pouco maior que o da Virgínia, mas a latência e a residência do dado justificam para a maioria dos sistemas brasileiros.

---

## Slide 3 — Como organizar os serviços

Em vez de listar duzentos nomes, vou agrupar os serviços por pergunta de arquitetura. Cada grupo responde uma necessidade concreta: onde o código roda, onde o arquivo e o dado ficam, como o usuário chega lá, quem pode fazer o quê. Integração e IA aparecem aqui só para situar — cada um tem bloco próprio depois.

---

## Slide 4 — Computação

O EC2, Elastic Compute Cloud, é o servidor virtual clássico. Você escolhe o sistema operacional — Linux ou Windows —, o tamanho da máquina, o disco e a rede, e paga enquanto ela estiver ligada. É a base histórica da AWS. Os tipos de instância são famílias com perfis diferentes: T é barato e serve para uso variável; M é uso geral equilibrado; C é otimizado para processamento; R tem muita memória; P e G têm GPU para IA e renderização.

Com o Auto Scaling, você configura uma quantidade mínima e máxima de instâncias e define uma métrica de disparo — CPU, número de requisições no balanceador. Quando o tráfego sobe, novas instâncias nascem. Quando cai, elas são encerradas. O Application Load Balancer distribui o tráfego entre as máquinas e tira do rodízio qualquer instância que não responda ao health check.

O Lambda é o extremo oposto do EC2. Você sobe uma função em Python, Node, Java ou outra linguagem suportada, e a AWS a dispara em resposta a um evento: uma requisição HTTP, um arquivo que chegou no S3, uma mensagem numa fila, um horário agendado. Não existe servidor para gerenciar. Quando ninguém chama a função, não há cobrança. A escala acontece por invocação — cada chamada pode rodar num ambiente separado.

ECS, Fargate e EKS são para contêineres. Vou detalhar esses três no bloco de microsserviços, porque é lá que o papel deles fica mais claro.

---

## Slide 5 — Armazenamento e Banco de Dados

O S3, Simple Storage Service, é o serviço de armazenamento mais famoso da AWS. A ideia é simples: você manda um arquivo, ele ganha um nome — chamado de chave — dentro de um bucket, e fica armazenado com durabilidade altíssima, porque o objeto é gravado em múltiplas zonas de disponibilidade. É importante entender que S3 não é um disco que você monta no sistema operacional. É uma API: você faz uma chamada para guardar e outra para recuperar. Serve para backup, site estático, data lake, origem de CDN, artefato de build. As classes de armazenamento existem para pagar menos por dado frio: o Glacier Deep Archive, por exemplo, custa menos de um centavo por gigabyte-mês, mas a recuperação pode levar horas. O Standard é para dado que precisa de acesso imediato.

O EBS, Elastic Block Store, é o disco da instância EC2. Simples assim: o sistema operacional da máquina virtual mora aqui, e só uma instância usa aquele disco por vez.

No banco de dados, o RDS é o relacional gerenciado. Você escolhe o motor — MySQL, PostgreSQL, Oracle, SQL Server — e a AWS cuida da instalação, do patch e do backup automático. Se você habilitar o Multi-AZ, existe uma réplica síncrona em outra zona de disponibilidade e o failover é automático. O Aurora é a variante da AWS, compatível com MySQL e PostgreSQL, mas com armazenamento distribuído próprio e desempenho maior.

O DynamoDB é o NoSQL da casa: chave de partição, latência de poucos milissegundos em qualquer escala, sem servidor para você dimensionar. Ele serve para sessão de usuário, carrinho de compras, perfil, telemetria — qualquer coisa em que o acesso é sempre pela mesma chave conhecida. Ele não substitui o relacional quando a pergunta envolve muitos joins.

---

## Slide 6 — Rede e Entrega

A VPC, Virtual Private Cloud, é a rede privada da sua conta. Você define o bloco de endereços IP e cria as sub-redes. O padrão que aparece em toda arquitetura web é: sub-rede pública recebe o balanceador, que é alcançável da internet; sub-rede privada recebe a aplicação; outra sub-rede privada recebe o banco. O banco nunca tem rota direta para a internet.

O Route 53 é o serviço de DNS. Ele traduz um domínio — api.minhaempresa.com — no endereço IP do balanceador ou da distribuição CloudFront. Também faz failover automático: se o health check de uma região falhar, o tráfego vai para a outra.

O CloudFront é a CDN, Content Delivery Network. Ele copia o conteúdo para mais de 750 pontos de presença ao redor do mundo. O usuário de São Paulo busca o arquivo num ponto próximo, não num data center distante. Além de acelerar o site e terminar o TLS perto do usuário, o CloudFront se integra ao WAF para filtrar ataques antes de chegarem à origem.

O API Gateway é a porta de entrada das APIs. Ele recebe a requisição HTTPS, autentica usando IAM, Cognito ou um autorizador Lambda, aplica limite de taxa para proteger o backend e encaminha para Lambda ou para outro serviço. Existe um modelo HTTP API mais simples e barato, e um modelo REST API com mais recursos como cache, chaves de API e planos de uso.

---

## Slide 7 — Segurança, Operação e Integração

O IAM, Identity and Access Management, é a identidade da infraestrutura da conta. Ele define quem — pessoa ou serviço — pode chamar qual API em qual recurso. É importante não confundir com o login do usuário do aplicativo, que fica no Cognito ou em outro provedor. O mecanismo preferido no IAM é a role: uma credencial temporária que o Lambda, a tarefa do ECS ou a instância EC2 assume para ganhar só as permissões que precisa, sem chave de acesso fixa. O erro clássico que gera incidente de segurança é criar uma política com Action estrela em Resource estrela — permissão total em tudo — ou colocar uma chave de acesso no código que vai pro repositório.

O CloudWatch coleta métricas de todos os serviços — CPU da instância, erros do balanceador, invocações do Lambda — e também os logs da aplicação. Você configura alarmes que disparam notificação ou até uma ação automática quando um valor cruza um limiar. Ele responde à pergunta "a aplicação está saudável?".

O CloudTrail tem um papel diferente: ele registra cada chamada de API feita na conta — quem fez, o quê, quando, de qual endereço IP. É a trilha de auditoria. Ele responde à pergunta "quem mudou essa configuração?".

O CloudFormation descreve a infraestrutura inteira em um arquivo YAML ou JSON. Com ele, você recria o ambiente de forma repetível em qualquer conta, revisa mudança em pull request antes de aplicar e evita o problema de "cliquei no console e não lembro o que fiz".

Por último neste slide, SQS e SNS são os dois serviços de integração fundamentais. SQS é fila: um serviço produz o trabalho e coloca na fila, o outro consome quando puder. SNS é pub/sub: um fato publicado num tópico chega a vários assinantes. Vou detalhar os dois no bloco de microsserviços, onde o papel deles fica mais concreto.

---

## Slide 8 — Catálogo completo

O catálogo da AWS vai muito além do núcleo que acabei de apresentar. Esta tabela organiza os serviços mais relevantes por categoria. Não vou abrir cada um aqui, mas quero que fique registrado o escopo. Armazenamento extra cobre EFS para disco NFS compartilhado entre várias máquinas e FSx para file server Windows. Banco extra tem Aurora, ElastiCache para cache em memória, Neptune para banco de grafos, Redshift para data warehouse e DocumentDB para documentos no estilo MongoDB. A categoria de análise tem Athena para consultar arquivos no S3 com SQL, Glue para ETL, Kinesis para streaming e EMR para processamento em cluster Spark ou Hadoop. Segurança extra tem Cognito para login de usuário final, KMS para gerenciar chaves de criptografia, WAF, GuardDuty e Inspector para detecção de ameaças e vulnerabilidades. Migração tem DMS para mover bancos e MGN para mover servidores. Mídia tem a família Elemental para vídeo ao vivo e sob demanda. IoT tem IoT Core como broker MQTT para dispositivos e Greengrass para processamento na borda.

---

## Slide 9 — Três caminhos para IA na AWS

A AWS não tem "um serviço de IA". Tem três formas de usar inteligência artificial, e elas resolvem problemas muito diferentes.

O primeiro caminho é a API pronta: o modelo já está treinado pela AWS ou por parceiros, você manda o dado e recebe uma estrutura de resposta — imagem, texto, áudio. Não há treino, não há GPU para gerenciar. Serve quando o problema é uma modalidade fechada e o modelo genérico resolve.

O segundo caminho é a IA generativa pelo Bedrock: você acessa um modelo fundacional por API e monta o prompt, os documentos e as ferramentas em cima dele. É o caminho para chat, resumo, resposta baseada em documentos internos, assistente de código.

O terceiro caminho é construir o próprio modelo com o SageMaker: você traz seus dados, define o algoritmo ou faz fine-tuning, treina, avalia, registra e publica o endpoint. Serve quando o dado é exclusivo da organização e o modelo precisa de controle total do ciclo.

---

## Slide 10 — APIs prontas e IA generativa

Nas APIs prontas, cada serviço cobre uma modalidade. Rekognition faz visão computacional: detecta objetos, rostos, texto em imagem e modera conteúdo — útil para bloquear upload impróprio ou ler uma placa. Textract vai além do OCR comum: ele entende formulários e tabelas dentro de PDFs e extrai os campos com estrutura, não só o texto corrido. Isso é o que faz diferença para ler um boleto ou uma nota fiscal escaneada. Transcribe converte fala em texto, com uma variante médica que entende terminologia clínica. Polly faz o caminho inverso: texto vira fala com vozes neurais — útil para URA, avisos e acessibilidade. Translate é tradução de texto. Comprehend analisa linguagem: detecta sentimento, extrai entidades, classifica — para marcar automaticamente se um comentário é reclamação, por exemplo. Lex é o motor de chatbot: você define intenções e os dados que o bot precisa coletar, chamados de slots. É a mesma família de tecnologia da Alexa.

Vale destacar que Transcribe, Lex e Polly juntos formam a base de um atendimento por voz: a fala do cliente vai para o Transcribe, que transcreve; o Lex entende a intenção; e o Polly fala a resposta de volta.

O Amazon Bedrock é o caminho generativo. Por trás da mesma API podem estar Claude, Llama, Titan, Mistral ou outros modelos, conforme a região e o contrato. Para fazer um chatbot responder sobre os documentos da empresa, você usa as Knowledge Bases: os PDFs ficam no S3, o serviço fatia o conteúdo, gera embeddings e, na hora da pergunta, recupera os trechos mais relevantes para embasar a resposta — isso se chama RAG, retrieval augmented generation. Os Agents permitem que o modelo decida chamar APIs da aplicação para cumprir uma tarefa, como consultar o status de um pedido. Os Guardrails adicionam filtro de conteúdo e mascaram dado pessoal na entrada e na saída.

O Amazon Q Developer é o assistente de código no editor: sugere, explica e transforma código. O Amazon Q Business é o assistente corporativo, conectado ao SharePoint, wikis e S3 da empresa, respeitando o que cada funcionário pode ver.

O SageMaker é para quem precisa controlar o ciclo de treino. Você prepara o dado, treina em instâncias com GPU que desligam ao terminar — para não pagar a máquina a noite inteira —, registra a versão aprovada e publica um endpoint de inferência. Para casos simples como ler um PDF ou traduzir uma frase, Textract e Translate são o caminho curto. SageMaker é o caminho longo e o certo quando o modelo é exclusivo da organização.

---

## Slide 11 — Como a fatura é formada

Não existe uma assinatura mensal da AWS. A conta do mês é a soma de tudo que foi usado, em cada serviço, em cada região. Parar um recurso encerra a cobrança daquele recurso. Não há multa de cancelamento da conta.

O whitepaper oficial de precificação da AWS resume o custo em três vetores principais. O primeiro é computação: você paga a hora ou o segundo da instância EC2, ou o GB-segundo da função Lambda. O segundo é armazenamento: gigabyte-mês no disco ou no objeto. O terceiro é a saída de dados: o gigabyte que sai da AWS para a internet. Existe ainda um quarto vetor que o whitepaper trata dentro dos outros: o request, ou seja, a chamada em si — importante em Lambda, S3, API Gateway e DynamoDB.

Há quatro modelos de compra. On-Demand é o preço cheio, sem contrato, pago por hora ou segundo conforme o uso — ideal para experimento, pico ou carga nova. Savings Plans é um compromisso de gastar um determinado valor por hora, por um ou três anos, em troca de desconto — o plano Compute cobre EC2, Lambda e Fargate juntos. Instâncias Reservadas funcionam de forma parecida mas prendem a um tipo específico de instância ou banco — RDS, Redshift, ElastiCache. Spot usa capacidade ociosa com desconto grande, mas a AWS pode retomar com aviso curto, então só serve para processamento em lote, CI e outras cargas que tolerem interrupção.

Um ponto importante sobre compromisso: Savings Plan ou reserva cobram mesmo sem uso — você paga o que comprometeu, e não o que efetivamente rodou. Compromisso além do necessário é mais caro do que On-Demand.

---

## Slide 12 — Free Tier, preços de referência e o que gera susto

O Free Tier mudou em julho de 2025. Para contas criadas a partir dessa data, são 100 dólares em crédito ao criar a conta, e até mais 100 ao completar atividades básicas como subir uma instância, um banco e uma função — chegando a 200 dólares no total. No plano gratuito, a conta não gera cobrança até os créditos acabarem ou até seis meses, o que vier primeiro. Além dos créditos, existe um conjunto de ofertas always free — mensais, sem prazo de expiração. Lambda está nesse conjunto: 1 milhão de chamadas por mês e 400 mil GB-segundos de computação, para sempre. DynamoDB tem 25 GB de armazenamento e uma franquia de capacidade de leitura e escrita por mês, também permanente.

Para ter ordem de grandeza de preço: um t3.micro ligado 24 horas por dia o mês inteiro custa cerca de 7 dólares e 60 centavos em Virgínia. Em São Paulo, perto de 12 dólares. O S3 Standard custa 2,3 centavos por gigabyte-mês — 100 GB ficam em torno de 2 dólares e 30 centavos por mês só de armazenamento. A transferência para a internet nos primeiros 100 gigabytes por mês é gratuita para a conta inteira; acima disso, são 9 centavos por gigabyte até 10 terabytes. Esses são valores de lista de us-east-1, sem imposto. Para qualquer estimativa real, use a calculadora em calculator.aws.

O que costuma gerar susto na primeira fatura: instância EC2 que ninguém desligou depois do laboratório; endereço IPv4 público, que desde 2024 custa 0,005 dólar por hora mesmo parado — cerca de 3,50 dólares no mês; NAT Gateway, que cobra por hora e por gigabyte processado e pode custar mais que a própria instância; dado saindo da AWS para a internet em grande volume; e a região de São Paulo, que custa mais que Virgínia — vale a diferença por latência e residência do dado, mas precisa ser planejada.

---

## Slide 13 — Dois desenhos

Vou mostrar dois exemplos concretos. O primeiro é um site estático: congresso, portfólio, página de evento. O conteúdo fica no S3 — HTML, PDF, imagem. O CloudFront entrega esse conteúdo num ponto de presença perto de quem acessa, com HTTPS. O Route 53 resolve o nome do domínio para a distribuição CloudFront. Se o site tiver um formulário de inscrição, API Gateway recebe a requisição, Lambda processa e DynamoDB guarda o dado. Não existe servidor web ligado. A cobrança é de centavos para tráfego pequeno — armazenamento e request. É o desenho mais barato da lista para um projeto de disciplina.

O segundo exemplo é o sistema web com usuários: matrícula, portal, ERP. Aqui tem estado, transação, regra de integridade — precisa de banco relacional e de servidor de aplicação. O Route 53 resolve o domínio, o CloudFront entrega o front-end estático e protege com WAF, o Application Load Balancer distribui o tráfego entre as instâncias EC2 ou a aplicação no Beanstalk. Tudo isso fica dentro de uma VPC: balanceador na sub-rede pública, aplicação e banco em sub-redes privadas. O RDS em Multi-AZ garante que uma falha de zona não derrube o banco. Documentos em PDF ficam no S3 com link pré-assinado, sem deixar o bucket público. A senha do banco não aparece no código — fica no Secrets Manager e a aplicação busca em execução. CloudWatch monitora e CloudTrail registra quem alterou a infraestrutura.

---

## Slide 14 — Casos reais

Três exemplos brasileiros para mostrar a escala que a plataforma suporta.

O Mercado Livre é o maior e-commerce da América Latina e roda na AWS. Eles migraram de um monólito para microsserviços e construíram uma plataforma interna chamada Fury, em cima do Amazon EKS. Os números publicados pela própria AWS: cerca de 24 mil microsserviços, 17 mil bancos de dados, 900 milhões de requests por minuto entre os serviços, operação em 18 países. Os serviços citados incluem DynamoDB, S3, EBS, Neptune e EC2 com Spot para reduzir custo. Picos como o Black Friday são absorvidos pela elasticidade horizontal do EKS com instâncias Spot.

O iFood descreveu numa publicação do blog da AWS a migração do middleware financeiro de monolítico para orientado a eventos. Os serviços usados: EventBridge como barramento central, SQS para as filas de cada serviço, DynamoDB, Kinesis, MSK para Kafka gerenciado e EKS. O resultado prático foi que a falha de um serviço deixou de se propagar pela cadeia de chamadas síncronas — quando a nota fiscal falha, o pedido do cliente não falha junto.

O Nubank tem mais de 110 milhões de clientes no Brasil, Colômbia e México. O blog de engenharia deles descreve mais de 4 mil microsserviços orquestrados com Kubernetes, 72 bilhões de eventos por dia processados em Kafka, e uma estratégia de múltiplas contas AWS para isolar domínios de negócio entre times. A infraestrutura é declarada em CloudFormation com milhares de stacks.

A Netflix entra como exemplo global. A AWS hospeda o plano de controle: catálogo, recomendação, pagamento, autenticação. O modo de operação é ativo-ativo em quatro regiões AWS. Um detalhe importante: o vídeo em si é distribuído pela CDN própria da Netflix, a Open Connect, com aparelhos instalados dentro de operadoras. Não é correto dizer que cada byte do filme sai de um bucket S3.

---

## Slide 15 — O que é microsserviço

Um sistema monolítico coloca catálogo, pedido, pagamento e notificação no mesmo processo, no mesmo deploy, no mesmo banco. Qualquer mudança pequena em uma parte pode travar o deploy de todas as outras.

Microsserviço parte isso: cada capacidade de negócio vira um processo separado. O time de pagamento publica sem esperar o time de catálogo. Um pico de notificação não força o estoque a escalar junto. O banco de dados é de cada serviço — o schema de um não trava o deploy do vizinho. A comunicação entre serviços é pela rede, via HTTP ou mensagem.

O ganho é independência operacional. O custo é complexidade operacional: aparecem descoberta de endereço, falha parcial, mensagem duplicada e rastreio de uma requisição que atravessa cinco serviços diferentes. A AWS não transforma o monólito em microsserviço. Ela fornece as peças para quem já tomou essa decisão arquitetural.

---

## Slide 16 — Tecnologias que suportam microsserviços

Vou passar pela tabela mostrando, para cada necessidade do estilo, qual tecnologia da AWS responde e como ela faz isso na prática.

Para computação por serviço, o Lambda é o mais enxuto: cada função pode ser um serviço inteiro ou uma ação de um serviço, com pipeline e escala próprios. Quando ninguém chama, o custo é zero. ECS com Fargate é o passo seguinte: o contêiner é a unidade de deploy, cada service ECS tem sua imagem e seu Auto Scaling independente, sem você gerenciar as máquinas do cluster. EKS é Kubernetes gerenciado pela AWS — o control plane fica com a AWS, os nós ficam com você ou com o Fargate. É o que o Mercado Livre usa na plataforma Fury. O custo é a curva de aprendizado do Kubernetes; para um sistema pequeno, ECS e Lambda resolvem com menos plataforma.

Para a porta de entrada, o API Gateway expõe um único hostname para o cliente. Por trás, a rota /pedidos vai para um serviço e /cardapio vai para outro. Trocar o runtime de Lambda para ECS não muda o cliente, desde que o contrato de API permaneça. O throttle do API Gateway protege um serviço pequeno de um pico que o derrubaria. O Application Load Balancer faz papel parecido com regras de path para os target groups dentro da VPC.

A comunicação assíncrona é onde microsserviço ganha ou perde. Chamada HTTP síncrona acopla disponibilidade: se o serviço de estoque está lento, o serviço de pedido fica lento também. SQS quebra esse acoplamento: o serviço de pedido envia uma mensagem para a fila e segue. O serviço de nota fiscal consome quando puder. Se a nota estiver fora do ar, a mensagem fica na fila por até 14 dias — o pedido do cliente não falha. A dead-letter queue recebe as mensagens que falharam várias vezes, para um dado ruim não travar a fila inteira.

EventBridge vai além: é um barramento onde o produtor publica um evento com conteúdo — "pedido criado, id 123, valor 50 reais" — e regras filtram por conteúdo e entregam para quem assinou. Um serviço novo começa a reagir a "pedido criado" com uma nova regra, sem nenhum deploy no serviço que criou o pedido. Foi exatamente isso que o iFood adotou no middleware financeiro.

SNS completa o cenário de fan-out: quando um fato precisa chegar a vários serviços ao mesmo tempo — "pedido pago" precisa acordar estoque, nota e notificação —, o SNS entrega o mesmo evento para todos. O padrão seguro é SNS disparando uma fila SQS por assinante, para que ninguém perca mensagem se estiver fora do ar por um momento.

Step Functions entra nos fluxos com dono e com compensação: cobrar, emitir nota, e estornar se a nota falhar. EventBridge anuncia fatos; Step Functions conduz uma instância de processo do começo ao fim com retry automático em cada passo.

Para o dado isolado, DynamoDB é natural: cada serviço cria suas tabelas, sem depender de um banco compartilhado. O schema de um não bloqueia o deploy do outro. DynamoDB Streams notificam outro serviço quando um item muda, sem que o segundo acesse diretamente a tabela do primeiro. RDS e Aurora também funcionam, desde que cada serviço tenha seu banco separado — compartilhar a mesma instância com dezenas de serviços reacopla o deploy.

Observabilidade é obrigatória em microsserviços: sem ela, a falha tem sintoma num serviço e causa em outro, e você não sabe onde olhar. CloudWatch coleta métrica e log por serviço — o alarme de erro do pagamento é separado do alarme de CPU do catálogo. X-Ray coloca um ID na requisição e mostra a cascata completa: onde o tempo foi gasto, qual chamada falhou, qual serviço é o gargalo.

Cloud Map registra o endereço atual das tasks de contêiner, porque o IP muda toda vez que uma task morre e outra sobe. VPC Lattice conecta serviços em VPCs e contas diferentes com autenticação IAM, sem precisar criar peering de rede para cada par de serviços. IAM role por serviço garante que o Lambda de pedido só acessa a própria fila e a própria tabela — não a fila de outro serviço.

Para deploy independente, ECR guarda a imagem versionada de cada serviço. CodePipeline e CodeDeploy publicam só o service afetado, com estratégia rolling, canário ou blue/green, sem precisar reimplantar o sistema inteiro.

Para amarrar tudo em um fluxo: o celular chama o API Gateway, que valida o token do Cognito. O Lambda do serviço de pedido grava no DynamoDB e publica um evento no EventBridge. Uma regra entrega o evento para a fila SQS da cozinha e outra para a fila SQS de notificação. Cada consumidor escala com a profundidade da própria fila. Se a notificação cair, a mensagem espera e a cozinha continua processando. Step Functions entra só se houver pagamento com possibilidade de estorno. X-Ray mostra a cascata inteira. IAM garante que cada função só alcança o que é dela.

---

## Slide 17 — Conclusão

Para fechar em cinco frases: a AWS aluga infraestrutura e plataforma e você paga pelo que usa. Os serviços centrais são EC2 e Lambda para computação, S3 para arquivo, RDS e DynamoDB para dado, VPC para rede, IAM para permissão e CloudWatch para observabilidade. Para IA, existem três caminhos: API pronta para modalidades fechadas, Bedrock para IA generativa, SageMaker para modelo próprio. A cobrança é feita por computação, armazenamento e saída de dados, com modelos de compra que vão do On-Demand ao Savings Plans e ao Spot. Microsserviços são sustentados por Lambda ou ECS para computação independente, API Gateway na borda, SQS e EventBridge para comunicação assíncrona, e CloudWatch com X-Ray para observabilidade.

Mercado Livre, iFood, Nubank e Netflix estão em produção nessa plataforma, com escalas que vão de milhares a dezenas de milhares de microsserviços. Obrigado.

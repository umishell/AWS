# 2. Principais serviços e para que servem

Documento de pesquisa para a pergunta “quais são os principais serviços e para que servem?”. Não é roteiro de slides.

A resposta longa já está em `servicos-aws.md`. Este arquivo é a resposta curta: o conjunto que sustenta a maior parte das arquiteturas e que cabe numa fala de 15 a 20 minutos. Cada serviço está explicado com mais calma na seção indicada do catálogo.

## Como agrupar na apresentação

Um serviço solto não explica a AWS. O público entende melhor se cada nome responder a uma pergunta de arquitetura.

| Pergunta da arquitetura | Serviços |
| --- | --- |
| Onde o código roda? | EC2, Lambda, e (se houver tempo) ECS/Fargate |
| Onde o arquivo e o dado ficam? | S3, EBS, RDS, DynamoDB |
| Como o usuário chega lá? | Route 53, CloudFront, VPC, balanceador, API Gateway |
| Quem pode fazer o quê, e como se vê o que aconteceu? | IAM, CloudWatch, CloudTrail |
| Como uma parte do sistema fala com a outra sem ficar presa? | SQS, SNS |

O restante do catálogo (mais de 200 serviços) existe para casos específicos: vídeo, IoT, migração, data warehouse, satélite, contact center. Não são “principais” no sentido desta pergunta.

## Computação

### Amazon EC2

Servidor virtual. A pessoa escolhe sistema operacional, tamanho da máquina, disco e rede, e paga enquanto a instância está ligada. Serve para qualquer carga que precise de um sistema operacional completo: aplicação web, sistema legado, laboratório, processo longo. É a base histórica da AWS. Detalhe em `servicos-aws.md`, seção 2.

### Elastic Load Balancing e Auto Scaling

O balanceador reparte o tráfego entre várias máquinas, para a aplicação não depender de uma instância só. O tipo usado em aplicação web é o Application Load Balancer (HTTP/HTTPS, rota por caminho e por host). O Auto Scaling aumenta ou diminui o número de instâncias conforme a métrica (CPU, requisições). Juntos, são o mecanismo clássico de elasticidade horizontal em cima do EC2.

### AWS Lambda

Executa uma função em resposta a um evento (pedido HTTP, arquivo no S3, mensagem numa fila, horário) sem a pessoa provisionar servidor. A AWS escala a função, inclusive até zero quando ninguém chama. Serve para API, processamento de arquivo, automação e a cola entre serviços. O limite prático importante: a execução tem teto de tempo (15 minutos na função sob demanda) e não guarda disco local permanente. Cobrança por invocação e por duração, detalhada em `04-como-funciona-a-cobranca.md`.

### Onde os contêineres entram, se sobrar uma frase

Amazon ECS agenda contêineres. AWS Fargate é o modo em que a pessoa não administra as máquinas do cluster. Amazon EKS é Kubernetes gerenciado, para quem já padronizou Kubernetes. Amazon ECR guarda as imagens. Servem quando a aplicação já é empacotada em contêiner e precisa de várias cópias atrás de um balanceador. O papel deles em microsserviços está em `06-suporte-a-microservicos.md`.

## Armazenamento

### Amazon S3

Armazenamento de objetos. Um arquivo entra num bucket por uma chave, via API. Não é um disco montado no sistema operacional. Durabilidade projetada altíssima, porque o objeto é gravado em várias zonas. Serve para arquivo de usuário, backup, site estático, data lake, origem de CDN e artefato de build. Classes de armazenamento (Standard, acesso infrequente, Glacier) existem para pagar menos por dado que quase não é lido.

### Amazon EBS

Disco de bloco de **uma** instância EC2, na mesma zona de disponibilidade. É o HD da máquina virtual: sistema operacional e dado de um banco que a pessoa administra sozinha. Não é compartilhado entre várias máquinas. Para isso existe o EFS, que é NFS; para arquivo solto, o S3.

## Banco de dados

### Amazon RDS

Banco relacional gerenciado: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Db2. A AWS instala, aplica patch, faz backup e pode manter uma réplica síncrona em outra zona (Multi-AZ). A equipe cuida de schema, índice e SQL. Serve para a aplicação que já fala SQL e quer tirar a operação do servidor de banco das próprias costas.

Amazon Aurora é a variante da AWS compatível com MySQL ou PostgreSQL, com armazenamento distribuído e desempenho maior. É o passo seguinte quando o RDS “comunitário” aperta. Não precisa de slide próprio se o tempo estiver curto: cabe como frase dentro do RDS.

### Amazon DynamoDB

Banco NoSQL de chave-valor, sem servidor para administrar, latência de poucos milissegundos em escala alta. A aplicação acessa por uma chave que ela já conhece (id do usuário, id do pedido). Serve para sessão, carrinho, perfil, telemetria. Não substitui o relacional quando a pergunta é um relatório com vários joins.

## Rede

### Amazon VPC

A rede privada da conta. A pessoa define a faixa de IP e as sub-redes. O desenho padrão de uma aplicação web: sub-rede pública com o balanceador, sub-rede privada com a aplicação, sub-rede privada com o banco. O banco não fica com rota direta para a internet.

### Amazon Route 53

DNS. Traduz o nome (`api.universidade.edu.br`) no endereço do balanceador, do CloudFront ou de um IP. Também registra domínio e faz failover se um health check falhar.

### Amazon CloudFront

CDN. Copia conteúdo para pontos de presença perto de quem acessa. A origem pode ser um bucket S3 ou um balanceador. Serve para acelerar site e arquivo, terminar TLS perto do usuário e absorver tráfego antes da origem.

### Amazon API Gateway

Porta de entrada de API. Recebe HTTPS, autentica, limita taxa e encaminha para Lambda ou para um backend. Serve para expor um contrato estável sem operar um gateway próprio. Quando a necessidade é só HTTP para contêineres, o Application Load Balancer cumpre o papel e o API Gateway fica para quando existem planos de uso, chaves, estágios e autorizadores.

## Segurança e operação

### AWS IAM

Identidade **da infraestrutura**: quem, pessoa ou serviço, pode chamar qual API em qual recurso. Não é o login do aluno no sistema da universidade; esse papel é do Amazon Cognito ou de outro provedor de identidade. O mecanismo preferido é a *role*: credencial temporária assumida pela função Lambda, pela tarefa ECS ou pela instância EC2. Sem IAM não há arquitetura séria na AWS. A falha clássica é política ampla demais (`Action: *` em `Resource: *`) ou chave de acesso commitada no código.

### Amazon CloudWatch

Métricas, logs e alarmes. Responde “a aplicação está saudável?”. CPU da instância, erro 5xx do balanceador, invocação do Lambda, log da aplicação.

### AWS CloudTrail

Registra as chamadas de API da conta: quem fez, o quê, quando, de qual IP. Responde “quem mudou essa configuração?”. Não lê o conteúdo dos arquivos. Complementa o CloudWatch, não o substitui.

### AWS CloudFormation

Infraestrutura descrita em arquivo (YAML ou JSON) e recriada de forma repetível. Serve para o ambiente não depender de clique no console que ninguém lembra. O AWS CDK é a mesma ideia escrita em linguagem de programação; por baixo ele gera CloudFormation.

## Integração

### Amazon SQS

Fila. Quem produz o trabalho envia uma mensagem; quem processa puxa quando puder. Se o consumidor cair, a mensagem fica na fila. Serve para absorver pico e para um serviço não depender do outro estar no ar naquele segundo.

### Amazon SNS

Publicação e assinatura. Uma mensagem num tópico chega a vários destinos (filas, Lambda, e-mail, HTTP). Serve quando vários interessados precisam da mesma notícia. O desenho clássico é SNS na frente e uma fila SQS por serviço assinante.

## O que fica de fora desta resposta de propósito

Está no catálogo e é real, mas não é “principal” para esta apresentação: família Snow, Outposts, mainframe, Ground Station, Braket, GameLift, WorkSpaces, a maior parte de IoT, mídia Elemental, blockchain. Entram só se alguém perguntar.

ElastiCache, Redshift, Athena, Glue, Kinesis, Step Functions, EventBridge, Cognito, KMS, WAF e Secrets Manager são o anel seguinte. Estão resumidos na lista “se sobrar tempo” de `servicos-aws.md`. EventBridge e Step Functions voltam em `06-suporte-a-microservicos.md` porque a pergunta 6 exige. Cognito volta se o exemplo tiver login de usuário final.

## Frase de fechamento deste bloco

Os serviços não competem entre si num cardápio. Um sistema pequeno junta domínio (Route 53), entrega (CloudFront ou balanceador), computação (EC2 ou Lambda), dado (RDS ou DynamoDB), arquivo (S3), permissão (IAM) e visibilidade (CloudWatch e CloudTrail). O catálogo grande é o que se acrescenta quando o problema deixa de ser “colocar a aplicação no ar”.

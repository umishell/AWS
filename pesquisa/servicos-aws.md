# Serviços da Amazon Web Services (AWS)

Guia de estudo que explica **o que é cada serviço da AWS e para que ele serve**.

A AWS é a plataforma de computação em nuvem da Amazon. Em vez de comprar servidores, storages e data centers, você aluga capacidade sob demanda e paga pelo que usa. Os serviços se combinam: um site típico usa computação (EC2 ou Lambda), armazenamento (S3), banco de dados (RDS ou DynamoDB), rede (VPC, Route 53, CloudFront) e identidade (IAM).

Este documento segue as categorias oficiais da AWS e cobre o catálogo principal usado em arquitetura, operação e certificações. A AWS lança, renomeia e aposenta serviços com frequência. A referência viva está em [aws.amazon.com/products](https://aws.amazon.com/products/) e no whitepaper [Overview of Amazon Web Services](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html).

**Como ler este guia**

- Cada serviço tem: o que é, para que serve e quando faz sentido usá-lo.
- Serviços de base (EC2, S3, IAM, VPC, RDS, Lambda) estão descritos com mais profundidade porque quase toda arquitetura passa por eles.
- Nomes entre parênteses, como Amazon S3, são a marca oficial. A sigla é o que aparece no console e nas provas.

---

## Serviços principais para a apresentação

O catálogo completo está no sumário abaixo. Para os slides, estes são os serviços mais usados e os que o público espera ver. Cada nome leva à seção em que ele é explicado.

### Núcleo (vale um slide cada)

**Computação**

- [Amazon EC2](#amazon-ec2-elastic-compute-cloud) — servidor virtual. É a base da computação na AWS.
- [Elastic Load Balancing](#elastic-load-balancing-elb) — distribui o tráfego entre várias máquinas.
- [Auto Scaling](#amazon-ec2-auto-scaling-aws-auto-scaling-e-compute-optimizer) — aumenta ou diminui a quantidade de servidores conforme a demanda.
- [AWS Lambda](#aws-lambda) — executa código sem servidor, pago por invocação.

**Armazenamento**

- [Amazon S3](#amazon-s3-simple-storage-service) — arquivos e objetos (backups, site estático, data lake). O armazenamento mais famoso da AWS.
- [Amazon EBS](#amazon-ebs-elastic-block-store) — disco da instância EC2.

**Banco de dados**

- [Amazon RDS](#amazon-rds-relational-database-service) — banco relacional gerenciado (MySQL, PostgreSQL, SQL Server, Oracle).
- [Amazon DynamoDB](#amazon-dynamodb) — banco NoSQL de chave-valor, em escala e sem servidor para administrar.

**Rede**

- [Amazon VPC](#amazon-vpc-virtual-private-cloud) — a rede privada onde os recursos ficam isolados.
- [Amazon Route 53](#amazon-route-53) — DNS: traduz o domínio no endereço do serviço.
- [Amazon CloudFront](#amazon-cloudfront) — CDN. Entrega conteúdo perto do usuário.
- [Amazon API Gateway](#amazon-api-gateway) — porta de entrada das APIs.

**Segurança**

- [AWS IAM](#aws-iam-identity-and-access-management) — quem pode fazer o quê na conta. Serviço obrigatório em qualquer arquitetura.
- [AWS CloudTrail](#aws-cloudtrail) — auditoria: registra as chamadas de API.

**Operação**

- [Amazon CloudWatch](#amazon-cloudwatch) — métricas, logs e alarmes.
- [AWS CloudFormation](#aws-cloudformation) — infraestrutura descrita em arquivo e recriada de forma repetível.

**Integração entre aplicações**

- [Amazon SQS](#amazon-sqs-simple-queue-service) — fila. Desacopla quem produz o trabalho de quem processa.
- [Amazon SNS](#amazon-sns-simple-notification-service) — notificação. Uma mensagem chega a vários destinos.

### Se sobrar tempo

- [AWS Elastic Beanstalk](#aws-elastic-beanstalk) — sobe uma aplicação web sem montar EC2, balanceador e escala na mão.
- [Amazon EFS](#amazon-efs-elastic-file-system) — disco de rede compartilhado entre várias instâncias.
- [Amazon Aurora](#amazon-aurora) — relacional compatível com MySQL e PostgreSQL, de alto desempenho.
- [Amazon ElastiCache](#amazon-elasticache) — cache em memória (Redis ou Memcached).
- [Amazon Redshift](#amazon-redshift) — data warehouse para relatórios e BI.
- [Amazon ECS](#amazon-ecs-elastic-container-service), [Amazon EKS](#amazon-eks-elastic-kubernetes-service) e [AWS Fargate](#aws-fargate) — onde os contêineres rodam.
- [Amazon ECR](#amazon-ecr-elastic-container-registry) — repositório das imagens de contêiner.
- [Amazon Cognito](#amazon-cognito) — login dos usuários finais do aplicativo.
- [AWS KMS](#aws-kms-key-management-service) — chaves de criptografia.
- [AWS WAF e Shield](#aws-waf-shield-e-shield-advanced) — proteção de aplicação web e contra DDoS.
- [AWS Step Functions](#aws-step-functions) — orquestra um processo de vários passos.
- [Amazon EventBridge](#amazon-eventbridge) — reage a eventos da AWS e da aplicação.
- [Amazon Athena](#amazon-athena) — consulta SQL direto nos arquivos do S3.
- [AWS Glue](#aws-glue) — catálogo e ETL do data lake.
- [Amazon Kinesis](#amazon-kinesis) — dados em tempo real (streaming).
- [Amazon SageMaker](#amazon-sagemaker) — treinar e publicar modelos de machine learning.
- [Amazon Bedrock](#amazon-bedrock) — modelos de IA generativa por API.
- [AWS Database Migration Service](#aws-database-migration-service-dms) — migra bancos com a origem ainda no ar.

Um roteiro fechado de arquitetura, útil como slide final, está em [Como os serviços se encaixam](#25-como-os-serviços-se-encaixam).

---

## Sumário

1. [Como a AWS se organiza](#1-como-a-aws-se-organiza)
2. [Computação](#2-computação)
3. [Contêineres](#3-contêineres)
4. [Armazenamento](#4-armazenamento)
5. [Bancos de dados](#5-bancos-de-dados)
6. [Rede e entrega de conteúdo](#6-rede-e-entrega-de-conteúdo)
7. [Segurança, identidade e conformidade](#7-segurança-identidade-e-conformidade)
8. [Gerenciamento e governança](#8-gerenciamento-e-governança)
9. [Ferramentas de desenvolvedor](#9-ferramentas-de-desenvolvedor)
10. [Integração de aplicações](#10-integração-de-aplicações)
11. [Análise de dados](#11-análise-de-dados)
12. [Machine learning e inteligência artificial](#12-machine-learning-e-inteligência-artificial)
13. [Migração e transferência](#13-migração-e-transferência)
14. [Mídia](#14-mídia)
15. [Front-end web e mobile](#15-front-end-web-e-mobile)
16. [Computação para o usuário final](#16-computação-para-o-usuário-final)
17. [Internet das Coisas (IoT)](#17-internet-das-coisas-iot)
18. [Aplicações de negócio](#18-aplicações-de-negócio)
19. [Gestão financeira da nuvem](#19-gestão-financeira-da-nuvem)
20. [Blockchain](#20-blockchain)
21. [Games](#21-games)
22. [Tecnologias quânticas](#22-tecnologias-quânticas)
23. [Satélite](#23-satélite)
24. [Suporte e capacitação](#24-suporte-e-capacitação)
25. [Como os serviços se encaixam](#25-como-os-serviços-se-encaixam)
26. [Glossário rápido](#26-glossário-rápido)

---

## 1. Como a AWS se organiza

### Conta, região e zona de disponibilidade

- **Conta AWS.** Fronteira de faturamento, identidade e isolamento. Uma empresa costuma ter várias contas (produção, homologação, segurança) reunidas por **AWS Organizations**.
- **Região.** Área geográfica isolada, como `sa-east-1` (São Paulo) ou `us-east-1` (Norte da Virgínia). Você escolhe a região por latência, residência de dados, preço e serviços disponíveis. Nem todo serviço existe em toda região.
- **Zona de Disponibilidade (AZ).** Um ou mais data centers independentes dentro da região, com energia e rede próprias. Colocar recursos em duas ou mais AZs é a forma padrão de alta disponibilidade.
- **Local Zone.** Extensão da região colocada perto de uma cidade, para latência muito baixa (jogos, mídia, trading).
- **Wavelength Zone.** Infraestrutura AWS dentro da rede 5G de operadoras, para dispositivos móveis com latência de poucos milissegundos.
- **Edge location.** Ponto da rede global usado por CloudFront, Route 53, Global Accelerator e WAF. Não é uma região onde você sobe servidores gerais.

### Formas de acessar

| Forma | Para que serve |
| --- | --- |
| **Console de gerenciamento** | Interface web para criar e operar recursos |
| **AWS CLI** | Linha de comando para scripts e automação |
| **SDKs** | Bibliotecas (Python/boto3, Java, JavaScript, Go, .NET, etc.) para chamar APIs no código |
| **CloudFormation / CDK / Terraform** | Infraestrutura como código: a nuvem é descrita em arquivos e recriada de forma repetível |
| **APIs** | Tudo na AWS é API. Console, CLI e SDK são clientes dessa mesma API |

### Modelos de responsabilidade e de preço

No **modelo de responsabilidade compartilhada**, a AWS cuida da segurança *da* nuvem (data center, hardware, hypervisor). Você cuida da segurança *na* nuvem (sistema operacional do EC2, dados, IAM, configuração de buckets, patches da aplicação).

Preço, em linhas gerais:

- **Sob demanda:** paga por hora ou por segundo, sem compromisso.
- **Savings Plans e Instâncias Reservadas:** desconto em troca de compromisso de 1 ou 3 anos.
- **Spot:** capacidade ociosa com desconto grande, que a AWS pode retirar com aviso curto. Serve para cargas que toleram interrupção (batch, CI, render).
- **Free Tier:** cota gratuita limitada no tempo ou no volume, para experimentar.

---

## 2. Computação

Computação é onde o código roda: máquina virtual, função sem servidor, lote ou plataforma gerenciada.

### Amazon EC2 (Elastic Compute Cloud)

Servidores virtuais na nuvem. Você escolhe o sistema operacional (Amazon Linux, Ubuntu, Windows), o tamanho da máquina (tipo de instância), o disco e a rede, e paga enquanto a instância está ligada.

**Para que serve:** qualquer carga que precise de um sistema operacional completo — aplicações web, bancos que você mesmo administra, renderização, servidores de jogo, laboratórios.

Conceitos que importam:

- **AMI (Amazon Machine Image).** Molde da instância: SO + software pré-instalado. Você parte de uma AMI pública ou cria a sua.
- **Tipo de instância.** Família define o perfil de hardware.
  - **T** (burstable): uso geral barato, com créditos de CPU. Bom para sites pequenos e dev.
  - **M:** uso geral equilibrado (CPU e memória).
  - **C:** muita CPU (processamento, jogos, HPC).
  - **R / X:** muita memória (bancos em memória, caches grandes).
  - **P / G / Inf / Trn:** GPU ou chips de IA (treino e inferência).
  - **I / D / H:** disco local rápido (I/O intensivo).
  - **Mac:** macOS para build de apps Apple.
- **Grupos de segurança.** Firewall da instância: quais portas e origens podem entrar ou sair.
- **Par de chaves.** Chave SSH (Linux) ou senha de administrador (Windows) para acesso.
- **User data.** Script que roda na primeira inicialização (instalar pacotes, puxar código).
- **IP elástico.** Endereço IPv4 público fixo que você associa à instância. Se a instância morrer, o IP pode ir para outra.
- **Placement group.** Controla como instâncias ficam fisicamente: juntas (baixa latência entre elas), espalhadas (tolerância a falha) ou em partição.

**EC2 Auto Scaling** ajusta sozinho a quantidade de instâncias. Você define um mínimo, um máximo e uma métrica (CPU, requisições no balanceador). Se o tráfego sobe, nascem instâncias; se cai, elas são encerradas. É o mecanismo clássico de elasticidade horizontal.

**Modelos de compra no EC2:** On-Demand, Reserved, Savings Plans, Spot e Dedicated Hosts (servidor físico exclusivo, útil para licenças por socket e conformidade).

### Amazon EC2 Image Builder

Pipeline gerenciado para construir e atualizar AMIs (e também imagens de contêiner). Automatiza patch de SO, instalação de agentes e testes, para você não manter imagens “na mão” e desatualizadas.

### Elastic Load Balancing (ELB)

Distribui tráfego entre vários destinos (instâncias, contêineres, IPs, funções Lambda) para não depender de uma única máquina e para atravessar falha de uma AZ.

Há quatro tipos:

| Balanceador | Uso típico |
| --- | --- |
| **Application Load Balancer (ALB)** | HTTP e HTTPS. Roteia por caminho (`/api`), host e header. O mais usado em aplicações web |
| **Network Load Balancer (NLB)** | TCP/UDP em altíssima performance e IP estático. Útil para protocolos que não são HTTP |
| **Gateway Load Balancer (GWLB)** | Encaminha tráfego para appliances virtuais (firewall, IDS) de terceiros |
| **Classic Load Balancer** | Geração antiga. Não se usa em arquitetura nova |

### AWS Lambda

Executa código sem você provisionar servidor. Você envia uma função (Python, Node.js, Java, Go, .NET, Ruby, ou uma imagem de contêiner) e a AWS a dispara em resposta a um evento: upload no S3, mensagem no SQS, rota no API Gateway, agenda, alteração no DynamoDB.

**Para que serve:** APIs, processamento de arquivos, automação, backends leves, glue entre serviços.

Características:

- Cobra por número de invocações e por duração (GB-segundo).
- Escala sozinha, inclusive a zero (sem tráfego, custo quase zero).
- Limites importantes: tempo máximo de execução (15 minutos), tamanho do pacote e concorrência. Não serve para processo longo contínuo nem para algo que precise de estado em disco local permanente.
- **Cold start:** a primeira invocação depois de um período ocioso pode ser mais lenta, porque o ambiente de execução é criado na hora.

### AWS Elastic Beanstalk

Plataforma que sobe a aplicação web (Java, .NET, Node, Python, PHP, Ruby, Go, Docker) e, por baixo, cria EC2, balanceador, Auto Scaling e monitoramento. Você entrega o código; o Beanstalk monta a infraestrutura.

**Para que serve:** colocar uma aplicação no ar rápido, sem desenhar cada recurso. Quando a arquitetura fica complexa, times costumam migrar para ECS, EKS ou CloudFormation explícito.

### AWS Batch

Executa trabalhos em lote (jobs) em escala: milhares de tarefas de computação que não são um servidor sempre ligado. O Batch provisiona a frota (inclusive Spot), enfileira os jobs e os distribui.

**Para que serve:** render, simulações, genômica, processamento de imagens de satélite, ETL pesado.

### Amazon Lightsail

VPS simplificado, com preço mensal previsível: instância, disco, IP e um pacote de transferência. Também oferece bancos gerenciados, balanceador, CDN e contêineres em versão reduzida.

**Para que serve:** sites, WordPress, projetos pequenos e quem quer a nuvem sem a complexidade do EC2/VPC. Quando precisar de rede avançada ou dezenas de serviços integrados, o caminho é a AWS “completa”.

### AWS Outposts

Rack ou servidor da AWS instalado **no seu data center ou na sua loja**, mas operado como extensão da região AWS. Você usa as mesmas APIs (EC2, EBS, ECS, RDS, S3 em algumas configurações) com os dados fisicamente no local.

**Para que serve:** latência baixíssima com sistemas locais, residência de dados exigida por regulação, e fábricas ou hospitais que não podem depender só do link até a região.

### Família AWS Snow

Dispositivos físicos ruggedizados para lugares com pouca ou nenhuma conectividade.

- **Snowcone:** aparelho pequeno. Leva dados ou roda computação na borda e depois os envia para a AWS.
- **Snowball Edge:** dispositivo maior, com armazenamento e computação (Storage Optimized ou Compute Optimized). Migração de dezenas a centenas de terabytes e processamento local.
- **Snowmobile:** contêiner transportado por caminhão, para exabytes. Raro; é migração extrema de data center.

**Para que serve:** quando a rede é lenta demais para enviar o volume pela internet. Você copia os dados no aparelho e a AWS (ou você) transporta o hardware.

### AWS Local Zones e AWS Wavelength

Já citados na organização da nuvem. **Local Zones** aproximam EC2, EBS e outros serviços de uma cidade. **Wavelength** coloca computação dentro da rede 5G da operadora.

### VMware Cloud on AWS

Ambiente VMware (vSphere) rodando em infraestrutura AWS, para estender ou migrar cargas que já vivem em VMware sem reescrevê-las. A oferta depende da parceria com o fornecedor do hypervisor; confirme a disponibilidade atual antes de planejar em cima dela.

### AWS SimSpace Weaver

Simulação espacial em larga escala (cidades, multidões, entidades) distribuída em várias instâncias. Voltado a simulação e gêmeos digitais, não a uma aplicação web comum.

### AWS ParallelCluster e serviços de HPC

**AWS Parallel Computing Service** e **AWS ParallelCluster** sobem clusters de computação de alto desempenho (filas Slurm, rede rápida entre nós). **Para que serve:** pesquisa científica, dinâmica de fluidos, modelagem financeira, qualquer carga MPI que antes rodava em supercomputador local.

### Amazon EC2 Auto Scaling, AWS Auto Scaling e Compute Optimizer

- **EC2 Auto Scaling:** escala grupos de instâncias EC2.
- **AWS Auto Scaling:** visão mais ampla, também para DynamoDB, ECS, Aurora e Spot Fleets, com planos de escalabilidade.
- **AWS Compute Optimizer:** analisa métricas reais (CloudWatch) e recomenda tipo de instância, volume EBS ou configuração de Lambda menores ou mais adequados. É uma ferramenta de custo e desempenho, não um serviço que executa a aplicação.

---

## 3. Contêineres

Contêiner empacota a aplicação e as dependências numa imagem portátil (Docker/OCI). A AWS oferece o registro das imagens, o orquestrador e a opção de não gerenciar servidor nenhum.

### Amazon ECR (Elastic Container Registry)

Repositório privado de imagens de contêiner, integrado a IAM. Equivale a um Docker Hub privado dentro da sua conta, com varredura de vulnerabilidades nas imagens.

**Para que serve:** guardar e versionar as imagens que ECS, EKS, Lambda ou App Runner vão executar.

### Amazon ECS (Elastic Container Service)

Orquestrador de contêineres da AWS. Você define uma **task** (quais contêineres, CPU, memória, portas) e um **service** (quantas cópias manter no ar, atrás de um balanceador). O ECS coloca essas tasks em:

- **EC2:** você gerencia o cluster de máquinas.
- **Fargate:** você não vê servidor; informa só CPU e memória da task.
- **ECS Anywhere:** o mesmo control plane agenda contêineres em máquinas suas, fora da AWS.

**Para que serve:** microserviços, APIs e workers em contêiner, com integração nativa a ALB, IAM, CloudWatch e Service Discovery.

### Amazon EKS (Elastic Kubernetes Service)

Kubernetes gerenciado. A AWS opera o control plane (API server, etcd); você opera os nós (ou usa Fargate / EKS Auto Mode). Compatível com o ecossistema Kubernetes (Helm, Ingress, service mesh, operadores).

**Para que serve:** quando o time já padronizou Kubernetes, ou quando a carga precisa ser portátil entre nuvens e on-premises.

**EKS Anywhere** roda clusters Kubernetes com a distribuição da AWS na sua infraestrutura, para o mesmo modelo operacional dentro e fora da nuvem.

### AWS Fargate

Motor serverless de contêiner. Não é um orquestrador separado: é o “onde rodar” do ECS e do EKS quando você não quer gerenciar EC2. Você paga por vCPU e memória da task ou do pod, pelo tempo em que estão no ar.

### AWS App Runner

Caminho mais curto para publicar um contêiner ou um repositório de código como serviço web com HTTPS, escala automática e balanceamento. Menos controle que ECS; menos trabalho operacional.

**Para que serve:** APIs e sites em contêiner que não precisam de malha de rede elaborada.

### AWS Copilot

Interface de linha de comando que cria a infraestrutura ECS/Fargate (serviço, pipeline, ambiente) a partir da aplicação. É ferramenta de desenvolvedor em cima do ECS, não um runtime novo.

### Red Hat OpenShift Service on AWS (ROSA)

OpenShift (Kubernetes empresarial da Red Hat) operado em conjunto na AWS. **Para que serve:** empresas que já padronizaram OpenShift e querem esse mesmo plano de controle na nuvem.

---

## 4. Armazenamento

A AWS separa armazenamento de objeto, de bloco e de arquivo. Escolher errado é um dos erros mais caros: banco de dados não mora bem em sistema de arquivos compartilhado genérico, e backup de longo prazo não precisa do disco mais rápido.

### Amazon S3 (Simple Storage Service)

Armazenamento de **objetos**: você grava um arquivo (objeto) dentro de um **bucket**, identificado por uma chave (o “caminho” do objeto). Não é um disco que você monta no sistema operacional. É uma API (`PutObject`, `GetObject`) com durabilidade projetada de 11 noves (99,999999999%) ao gravar em várias AZs.

**Para que serve:** arquivos de usuários, backups, data lakes, sites estáticos, origem de uma CDN, logs, artefatos de build, qualquer blob.

Conceitos:

- **Classes de armazenamento.** O mesmo bucket pode misturar classes via lifecycle.
  - **S3 Standard:** acesso frequente.
  - **S3 Intelligent-Tiering:** a AWS move o objeto entre camadas quentes e frias conforme o acesso.
  - **S3 Standard-IA e One Zone-IA:** acesso infrequente, mais barato, com taxa de recuperação. One Zone fica numa única AZ (menos resiliente, mais barato).
  - **S3 Glacier Instant Retrieval, Flexible Retrieval e Deep Archive:** arquivo frio. Deep Archive é o mais barato e a recuperação pode levar horas. Glacier Instant devolve na hora.
  - **S3 Express One Zone:** latência muito baixa para dados quentes numa única AZ.
- **Versionamento.** Guarda várias versões do mesmo objeto. Protege contra sobrescrita e exclusão acidental.
- **Lifecycle.** Regras que transitam ou expiram objetos (por exemplo: Standard por 30 dias, depois IA, depois Glacier, apagar após 7 anos).
- **Bloqueio de objeto (Object Lock).** WORM: o objeto não pode ser apagado nem alterado até uma data. Usado em trilha de auditoria e retenção legal.
- **Criptografia.** SSE-S3 (chaves geridas pelo S3), SSE-KMS (chaves no KMS, com auditoria) ou SSE-C (você envia a chave a cada request).
- **Bucket policy e ACL.** Quem pode ler ou escrever. Bucket público é a causa clássica de vazamento; o padrão moderno é bloquear acesso público na conta.
- **S3 Replication.** Copia objetos para outro bucket, na mesma região ou em outra, para DR ou residência.
- **S3 Event Notifications.** Dispara Lambda, SQS ou SNS quando um objeto chega. Base de pipelines “arquivo caiu, processa”.
- **URLs pré-assinadas.** Link temporário que autoriza upload ou download sem tornar o bucket público.
- **S3 Select e acesso via Athena.** Ler parte de um objeto ou consultá-lo com SQL sem baixar o arquivo inteiro.
- **Pontos de acesso multi-região e S3 Access Points.** Simplificam políticas quando muitos times usam o mesmo lago de dados.
- **S3 on Outposts.** Mantém objetos no rack Outposts, no seu prédio.

**Amazon S3 Glacier** era o nome antigo do arquivo frio. Hoje as classes Glacier vivem dentro do S3. O serviço separado **S3 Glacier** (vaults) ainda existe em contas antigas; arquitetura nova usa classes de armazenamento do S3.

### Amazon EBS (Elastic Block Store)

Disco de bloco anexado a **uma** instância EC2 na mesma AZ. Funciona como o HD/SSD da máquina virtual. Tipos principais: gp3 (SSD de uso geral, o padrão atual), io2 (IOPS provisionado para banco), st1/sc1 (HDD para throughput ou arquivo).

**Para que serve:** sistema operacional da instância, dados de banco que roda no EC2, qualquer coisa que precise de sistema de arquivos de bloco.

Snapshots do EBS vão para o S3 (de forma incremental) e são a base de backup do disco. Um snapshot pode virar um volume em outra AZ.

### Amazon EFS (Elastic File System)

Sistema de arquivos **NFS compartilhado**, elástico, que várias instâncias EC2, tasks ECS e funções Lambda (com alguma configuração) montam ao mesmo tempo. Cresce e encolhe sozinho.

**Para que serve:** home directories, conteúdo compartilhado entre servidores web, persistência de contêineres que precisam do mesmo diretório.

Não substitui EBS quando uma única máquina precisa do menor latência de disco local, nem substitui S3 para data lake.

### Amazon FSx

Família de sistemas de arquivos gerenciados, cada um compatível com um protocolo que a aplicação já espera:

| Serviço | Protocolo / origem | Uso |
| --- | --- | --- |
| **FSx for Windows File Server** | SMB, Active Directory | File server Windows, pastas de rede corporativas |
| **FSx for Lustre** | Lustre (HPC) | Processamento paralelo de arquivos enormes, acoplado ao S3 |
| **FSx for NetApp ONTAP** | NFS, SMB, iSCSI | Recursos NetApp (snapshots, replicação, multiprotocolo) |
| **FSx for OpenZFS** | NFS, OpenZFS | Cargas que dependem de snapshots e clones ZFS |

### AWS Storage Gateway

Appliance (virtual ou hardware) que fica na sua rede e apresenta armazenamento local cujo backend é a AWS.

- **S3 File Gateway:** compartilhamento NFS/SMB cujos arquivos são objetos no S3.
- **Tape Gateway:** biblioteca de fitas virtuais que substitui backup em fita física, gravando no S3/Glacier.
- **Volume Gateway:** volumes de bloco em cache ou armazenados, com snapshot no EBS.

**Para que serve:** híbrido. Aplicações locais continuam falando SMB/NFS/iSCSI/fita, e os dados passam a viver na nuvem.

### AWS Backup

Painel único de política de backup para EBS, RDS, Aurora, DynamoDB, EFS, FSx, Storage Gateway, EC2, S3, entre outros. Define frequência, retenção, cofre com bloqueio (backup vault lock) e cópia entre regiões ou contas.

**Para que serve:** não inventar um script de snapshot por serviço. Centraliza conformidade de retenção.

### AWS Elastic Disaster Recovery (DRS)

Replica servidores (na AWS ou on-premises) de forma contínua e permite failover para EC2 em minutos. Substitui, na prática, o antigo CloudEndure Disaster Recovery dentro da AWS.

**Para que serve:** recuperação de desastre com RPO baixo, sem manter um segundo data center quente o tempo todo.

### AWS DataSync

Agente que copia dados entre NFS, SMB, HDFS, objeto on-premises e S3, EFS ou FSx, com validação e agendamento. Mais eficiente que um `aws s3 sync` artesanal para grandes volumes recorrentes.

---

## 5. Bancos de dados

A escolha depende do modelo de dados e do padrão de acesso, não de moda.

| Necessidade | Serviço típico |
| --- | --- |
| Relacional, SQL, transações | RDS ou Aurora |
| Chave-valor em escala massiva, latência de milissegundo | DynamoDB |
| Cache | ElastiCache ou MemoryDB |
| Documentos no estilo MongoDB | DocumentDB |
| Grafos | Neptune |
| Colunar compatível com Cassandra | Keyspaces |
| Séries temporais | Timestream |
| Data warehouse / analytics SQL | Redshift |
| Ledger imutável | QLDB |

### Amazon RDS (Relational Database Service)

Banco relacional gerenciado. A AWS cuida de instalação, patch, backup automático, failover e armazenamento. Você cuida de schema, índices e SQL. Motores: **MySQL, PostgreSQL, MariaDB, Oracle, SQL Server** e também **Db2**.

**Para que serve:** a aplicação que já usa um desses bancos e precisa de operação mais simples do que um banco instalado no EC2.

**Multi-AZ** mantém uma réplica síncrona em outra zona e faz failover automático. **Read replicas** são cópias assíncronas para leitura (relatórios, alívio do primário). Você pode ter réplicas em outra região.

Rodar o banco você mesmo no EC2 dá mais controle (e mais plantão). RDS é o padrão quando o motor cabe no serviço gerenciado.

### Amazon Aurora

Banco relacional compatível com **MySQL** ou **PostgreSQL**, construído pela AWS em cima de uma camada de armazenamento distribuída. Réplicas leem o mesmo volume, o failover é rápido e o desempenho costuma ser maior que o MySQL/PostgreSQL “comunitário” no RDS, com custo também maior.

Variantes:

- **Aurora Serverless v2:** a capacidade (ACUs) sobe e desce com a carga. Bom para tráfego irregular e ambientes que ficam ociosos parte do dia.
- **Aurora Global Database:** uma região primária e réplicas de leitura em outras regiões, com failover regional.
- **Babelfish:** camada que permite a Aurora PostgreSQL aceitar conexões no protocolo do SQL Server (T-SQL), para reduzir reescrita numa migração.
- **Aurora Limitless Database / DSQL:** caminhos para escalar além de um único writer tradicional. **Aurora DSQL** é um SQL distribuído, serverless e fortemente consistente, para aplicações que precisam de escala horizontal sem gerenciar shards.

### Amazon DynamoDB

Banco NoSQL de chave-valor e documento, totalmente gerenciado, com latência de poucos milissegundos em qualquer escala. Não há servidor para dimensionar. Você define tabela, chave de partição (e opcionalmente chave de ordenação) e a AWS particiona os dados.

**Para que serve:** sessões, carrinhos, perfis, IoT, leaderboards, qualquer acesso por chave conhecida em volume alto. Não serve bem para consultas ad hoc complexas com muitos joins; isso continua sendo trabalho de relacional ou de warehouse.

Conceitos:

- **Capacidade sob demanda ou provisionada.** Sob demanda cobra por request. Provisionada você reserva RCU/WCU e pode usar **Auto Scaling**.
- **Índice secundário global (GSI) e local (LSI).** Outras chaves de busca além da chave primária.
- **DynamoDB Streams.** Log ordenado das alterações. Alimenta Lambda (por exemplo, replicar para o OpenSearch ou atualizar um agregado).
- **Tabelas globais.** Replicação multi-região ativa-ativa.
- **DAX (DynamoDB Accelerator).** Cache em memória na frente da tabela, para leituras repetidas em microssegundos.
- **TTL.** Apaga itens automaticamente após uma data (sessões, tokens).
- **Transações.** Operações ACID em um ou mais itens, com limite de tamanho.
- **Backup point-in-time e on-demand.** Restauração da tabela.
- **Export para S3 e import.** Integração com analytics sem full scan pela aplicação.

### Amazon ElastiCache

Cache em memória gerenciado, compatível com **Redis (OSS)** ou **Memcached**. Fica na frente do banco para guardar respostas quentes e reduzir latência e carga.

**Para que serve:** cache de sessão, ranking, rate limit, resultado de query cara. Os dados podem ser efêmeros: se o nó cair, a aplicação busca de novo na origem.

**Amazon MemoryDB for Redis** é diferente: é um banco em memória **durável** (transações registradas em log multi-AZ), com API compatível com Redis. Use MemoryDB quando o dado em memória **é** a fonte da verdade. Use ElastiCache quando for cache.

### Amazon DocumentDB

Banco de documentos gerenciado, compatível com a API do MongoDB. **Para que serve:** migrar ou operar cargas que usam documentos JSON e consultas no estilo Mongo, sem administrar o cluster você mesmo.

### Amazon Neptune

Banco de grafos gerenciado. Consultas em **Gremlin**, **openCypher** ou **SPARQL**. **Para que serve:** redes de relacionamento — detecção de fraude (quem se conecta a quem), grafos de conhecimento, recomendações, redes sociais, topologia.

**Neptune Analytics** é o motor de análise em memória sobre grafos grandes (algoritmos como PageRank e caminho mais curto), separado do banco transacional.

### Amazon Keyspaces (for Apache Cassandra)

Cassandra gerenciado, compatível com CQL. **Para que serve:** cargas que já usam Cassandra e querem tirar a operação do cluster (gossip, compactação, nós) da equipe.

### Amazon Timestream

Banco de séries temporais. Ingere medidas com carimbo de tempo (sensores, métricas de aplicação, telemetria) e separa armazenamento recente (memória, consulta rápida) de armazenamento magnético (histórico barato).

**Para que serve:** IoT industrial, métricas de frota, dados que são “valor no instante T” e quase nunca são atualizados.

### Amazon Redshift

Data warehouse colunar para analytics em terabytes ou petabytes. Você carrega dados (do S3, via Glue, DMS, zero-ETL) e consulta com SQL. Não é o banco operacional da aplicação (esse é RDS/Aurora/DynamoDB); é onde relatórios e BI leem agregações grandes.

**Para que serve:** “quanto vendemos por região no trimestre”, junções de fatos enormes, alimentação do QuickSight ou de outra ferramenta de BI.

Recursos: **Redshift Serverless** (sem gerenciar cluster), **RA3** com armazenamento gerenciado separado da computação, **Spectrum** (consultar dados que continuam no S3), **data sharing** entre clusters e **zero-ETL** com Aurora/RDS (replicação para analytics sem pipeline que você mantém).

### Amazon QLDB (Quantum Ledger Database)

Banco de livro-razão. Cada alteração fica num histórico criptograficamente verificável (hash em cadeia). Não é blockchain descentralizada: há um dono (a sua conta AWS). **Para que serve:** rastrear cadeia de custódia, registros financeiros internos, histórico de alterações em que ninguém, nem o administrador, deveria reescrever o passado sem que isso fosse detectável.

O serviço entrou em caminho de descontinuação para clientes novos em alguns anúncios da AWS; confirme o status antes de adotá-lo em projeto novo. Para trilha de auditoria operacional, CloudTrail + S3 Object Lock cobre outro problema (quem chamou qual API).

### Amazon RDS Custom e Amazon RDS para bancos no EC2

**RDS Custom** deixa você acessar o sistema operacional e aplicar patches próprios, ainda com backup e monitoramento da AWS. Existe para Oracle e SQL Server quando a aplicação exige configuração que o RDS padrão não permite.

---

## 6. Rede e entrega de conteúdo

### Amazon VPC (Virtual Private Cloud)

Sua rede privada dentro da AWS. Nada de computação “fala com a internet” por padrão sem você desenhar isso. Uma VPC tem um bloco CIDR (por exemplo `10.0.0.0/16`) e é dividida em **sub-redes**.

Conceitos:

- **Sub-rede pública.** Tem rota para um **Internet Gateway**. Instâncias com IP público saem e podem ser alcançadas.
- **Sub-rede privada.** Sem rota direta para a internet. Bancos ficam aqui. Para baixar atualizações, usa-se um **NAT Gateway** na sub-rede pública (só saída).
- **Tabelas de rotas.** Dizem para onde cada destino de IP vai (local, IGW, NAT, peering, gateway de trânsito).
- **Security groups.** Firewall por interface de rede. Stateful: se você libera a entrada, a resposta volta. Regras de permissão apenas.
- **NACL (Network ACL).** Firewall da sub-rede. Stateless: precisa liberar ida e volta. Permite deny explícito. Menos usado no dia a dia que o security group; entra quando você quer uma barreira extra na borda da sub-rede.
- **VPC Peering.** Liga duas VPCs por IP privado. Não é transitivo (A–B e B–C não faz A chegar em C).
- **VPC Endpoints.** Acesso privado a serviços AWS sem sair para a internet.
  - **Gateway endpoint:** S3 e DynamoDB, por rota.
  - **Interface endpoint (PrivateLink):** ENI na sua sub-rede para outros serviços (API da AWS, ou serviço de outra conta).
- **Flow Logs.** Registro dos fluxos de rede aceitos e rejeitados. Vai para CloudWatch Logs ou S3. Essencial para diagnóstico e segurança.

Padrão de arquitetura web em três camadas: sub-rede pública com o balanceador, sub-rede privada de aplicação (EC2/ECS), sub-rede privada de dados (RDS). Só o balanceador é alcançável da internet.

### Elastic IP, ENI e NAT Gateway

- **ENI:** placa de rede virtual. Uma instância pode ter várias.
- **Elastic IP:** IPv4 público estável. Endereço parado (não associado) gera custo, de propósito, para não acumular IPv4 ocioso.
- **NAT Gateway:** gerenciado, por AZ, permite que a rede privada inicie conexões de saída. Para alta disponibilidade, um NAT por AZ.

### AWS Transit Gateway

Hub regional que conecta VPCs, VPNs e Direct Connect sem uma malha de peerings N para N. As VPCs “falam” com o hub; o hub encaminha. Escala a arquitetura de rede de empresas com dezenas de contas.

### AWS Cloud WAN

Rede global gerenciada entre regiões e sites, com políticas de segmento (produção isolada de desenvolvimento) definidas de forma central. Um nível acima do Transit Gateway quando a malha é mundial.

### AWS Site-to-Site VPN e Client VPN

- **Site-to-Site VPN:** túnel IPsec entre o seu firewall/roteador e a VPC. Rápido de ativar; o link é a internet pública criptografada.
- **Client VPN:** funcionários abrem um cliente e entram na VPC. Acesso de pessoas, não de data center.

### AWS Direct Connect

Circuito dedicado (porta física num parceiro ou no local da AWS) entre o seu data center e a AWS. Não atravessa a internet pública. Latência mais estável e, em volume alto, custo de transferência de saída menor que VPN.

**Para que serve:** híbrido sério — ERP local falando com VPC o dia inteiro, migração grande, requisito de circuito privado.

Pode ser combinado com VPN como caminho de backup, ou com Transit Gateway e Direct Connect Gateway para alcançar várias regiões e VPCs.

### Amazon Route 53

Serviço de **DNS** autoritativo e também de registro de domínio. Traduz `api.empresa.com` no endereço do balanceador, da distribuição CloudFront ou de um IP.

Além de DNS comum, oferece **políticas de roteamento**:

- simples;
- ponderado (canário: 10% para a versão nova);
- latência (manda o usuário para a região mais próxima);
- geolocalização e geoproximidade;
- failover (se o health check falhar, troca o destino);
- resposta de vários valores.

**Health checks** testam um endpoint e alimentam failover e alarmes. **Resolver** encaminha consultas DNS entre a VPC e o DNS da empresa (híbrido).

### Amazon CloudFront

CDN. Copia o conteúdo para edge locations perto do usuário. Na origem pode estar um bucket S3, um ALB, um API Gateway ou qualquer HTTP.

**Para que serve:** acelerar sites e APIs, baixar carga da origem, terminar TLS perto do usuário, servir vídeo e arquivos grandes. Integrado a **AWS WAF** e **Shield** para filtrar ataque antes de chegar na aplicação. **CloudFront Functions** e **Lambda@Edge** rodam código na borda (reescrever URL, autenticar, personalizar header) — Functions para lógica mínima e rápida; Lambda@Edge para lógica maior.

### AWS Global Accelerator

Dá endereços IP anycast estáticos que entram na rede global da AWS no ponto mais próximo do usuário e de lá seguem até o ALB, NLB ou IP da sua aplicação, na região saudável. Diferente do CloudFront: não é cache de HTTP; é aceleração e failover de **qualquer tráfego TCP/UDP**, inclusive não HTTP.

### Amazon API Gateway

Porta de entrada gerenciada para APIs. Recebe HTTP (ou WebSocket), autentica (IAM, Cognito, Lambda authorizer), aplica throttle e cota, e encaminha para Lambda, HTTP de backend, ECS ou outro serviço AWS.

Modalidades:

- **HTTP API:** mais barata e simples, para proxy e Lambda.
- **REST API:** mais recursos (validação de request, API keys, usage plans, cache, transformação).
- **WebSocket API:** conexões persistentes (chat, painel ao vivo).

**Para que serve:** expor microsserviços com um contrato estável, sem você operar um gateway próprio. Em arquiteturas novas, **Application Load Balancer** também pode invocar Lambda diretamente; o API Gateway entra quando você precisa do conjunto de recursos de API (planos de uso, chaves, estágios, autorizadores).

### AWS App Mesh

Malha de serviços (service mesh) baseada em Envoy. Controla tráfego entre microsserviços: retry, timeout, mTLS, roteamento canário, sem colocar essa lógica em cada aplicação. O serviço está em modo de manutenção; para Kubernetes novo, o ecossistema costuma avaliar alternativas (VPC Lattice, malhas mantidas pela comunidade). Confirme o roadmap antes de adotar.

### Amazon VPC Lattice

Conecta serviços (ECS, EKS, EC2, Lambda) entre VPCs e contas com políticas de autenticação IAM e descoberta, sem você costurar peering e security groups serviço a serviço. É a proposta da AWS para comunicação leste-oeste entre aplicações.

### AWS PrivateLink

Expõe um serviço seu (ou consome um de terceiro) por endpoint privado, sem tornar a VPC roteável para a outra. O consumidor vê apenas um endpoint na sub-rede dele. **Para que serve:** SaaS que atende clientes dentro da rede deles, e acesso a serviços AWS sem internet.

### AWS Cloud Map

Descoberta de serviços: registra instâncias, tasks e nomes (namespace DNS ou API) para que a aplicação ache o endereço atual de um microsserviço.

### AWS Verified Access e Client VPN

**Verified Access** libera acesso a aplicações internas sem VPN tradicional: cada request é autorizado com identidade (IAM Identity Center, provedor OIDC) e postura do dispositivo. Modelo “zero trust” de acesso a app web corporativa.

### Outros componentes de rede

- **AWS Network Firewall.** Firewall gerenciado de VPC, com regras de domínio, suricata e inspeção, para tráfego norte-sul e leste-oeste.
- **AWS Firewall Manager.** Aplica WAF, Shield Advanced, security groups e Network Firewall em todas as contas da organização a partir de uma política central.
- **Elastic Load Balancing** já descrito em computação; é peça de rede tanto quanto de computação.
- **AWS Direct Connect Gateway, Transit Gateway Connect e VPN CloudHub.** Peças de topologias híbridas maiores (várias VPCs num único circuito, SD-WAN, vários sites VPN).
- **IPv6 na VPC.** A AWS suporta dual-stack e, em vários serviços, arquiteturas somente IPv6, o que evita depender de IPv4 público escasso.

---

## 7. Segurança, identidade e conformidade

Segurança na AWS começa em identidade. A maior parte dos incidentes graves é bucket aberto, credencial em código ou política IAM ampla demais (`Action: *`, `Resource: *`).

### AWS IAM (Identity and Access Management)

Quem pode fazer o quê na conta. Não é o login dos usuários finais do seu aplicativo (esse papel é do Cognito ou de um IdP externo). IAM é a identidade **da infraestrutura**.

Peças:

- **Usuário IAM.** Identidade de longa duração com senha e, opcionalmente, access keys. O uso moderno evita usuário IAM para pessoas: pessoas entram pelo Identity Center. Usuário IAM ainda aparece em automações antigas.
- **Grupo.** Conjunto de usuários com as mesmas políticas.
- **Função (role).** Identidade que alguém ou algum serviço **assume** temporariamente, recebendo credenciais de curta duração. EC2, Lambda e ECS assumem roles. Contas cruzadas também. É o mecanismo preferido.
- **Política.** Documento JSON que permite ou nega ações em recursos. Anexada a usuário, grupo ou role. Há políticas gerenciadas pela AWS, políticas gerenciadas por você e políticas inline.
- **Política de limite de permissões (permissions boundary).** Teto: a role não pode exceder esse conjunto, mesmo que outra política conceda mais.
- **Política de confiança.** Quem pode assumir a role (`sts:AssumeRole`).
- **IAM Access Analyzer.** Aponta recursos compartilhados com o exterior da conta ou da organização (buckets, roles, chaves KMS) e também sugere políticas a partir do uso real.

Avaliação de política: negação explícita ganha de permissão. Sem permissão, o padrão é negar.

**AWS STS (Security Token Service)** emite as credenciais temporárias usadas quando uma role é assumida. Você não “cria um STS”; ele está no caminho de toda role.

### AWS IAM Identity Center (sucessor do AWS SSO)

Login único da organização para várias contas AWS e para aplicações de negócio (Salesforce, Office 365, aplicações SAML/OIDC). Integra com Active Directory ou com um IdP externo. A pessoa autentica uma vez e assume roles em contas diferentes, sem um usuário IAM em cada conta.

**Para que serve:** o jeito atual de dar acesso humano à AWS em empresas.

### Amazon Cognito

Identidade para **usuários finais** da sua aplicação.

- **User pools:** cadastro, login, MFA, login social (Google, Apple) e federação SAML/OIDC. Entrega tokens JWT que o API Gateway ou a aplicação validam.
- **Identity pools (federated identities):** trocam um token por credenciais AWS temporárias, para o app acessar S3 ou DynamoDB diretamente com permissão limitada. Use com cuidado; muitas arquiteturas preferem que só o backend acesse os dados.

### AWS Directory Service

Active Directory gerenciado.

- **Managed Microsoft AD:** AD completo na AWS, para ingressar instâncias Windows, FSx for Windows e aplicações que exigem LDAP/Kerberos.
- **AD Connector:** proxy para o AD que você já tem on-premises, sem replicar o diretório.
- **Simple AD:** diretório compatível com Samba, para cenários pequenos que não precisam do AD da Microsoft.

### AWS Organizations

Agrupa contas em uma organização, com uma conta de gerenciamento e contas-membro em **unidades organizacionais** (OUs). Permite:

- **SCPs (Service Control Policies):** teto do que as contas podem fazer, mesmo que um administrador local tente. Exemplo: proibir desligar CloudTrail ou criar recursos fora de `sa-east-1`.
- faturamento consolidado;
- compartilhamento de descontos;
- integração com Control Tower, IAM Identity Center e Firewall Manager.

SCP não concede permissão. Ela só restringe. A permissão continua vindo do IAM da conta.

### AWS Control Tower

Landing zone pronta: cria a organização, contas de auditoria e de log, guardrails (SCPs e regras do Config) e um console para provisionar contas novas dentro do padrão. **Para que serve:** começar multi-conta sem montar cada peça à mão.

### AWS KMS (Key Management Service)

Cria e controla chaves de criptografia. Os serviços (S3, EBS, RDS, Secrets Manager) pedem ao KMS para cifrar e decifrar. A chave mestra não sai do KMS em claro; o que circula são chaves de dados.

- **Chave gerenciada pela AWS:** zero configuração, menos auditoria fina.
- **Chave gerenciada pelo cliente (CMK):** você define rotação, política e quem pode usar. CloudTrail registra cada uso.

**Para que serve:** criptografia com controle de quem decifra e com trilha de auditoria. Também assina e verifica (algumas APIs) e gera dados criptográficos.

**AWS CloudHSM** é o módulo de hardware dedicado (HSM) na sua VPC, quando a norma exige que você, e não a AWS, controle o material da chave no aparelho (FIPS 140). Mais caro e mais trabalhoso que o KMS. O KMS pode usar um CloudHSM como repositório customizado de chaves.

### AWS Secrets Manager

Guarda segredos (senhas de banco, tokens de API), cifra com KMS, faz rotação automática (há integrações prontas para RDS) e entrega o valor à aplicação em tempo de execução via API ou SDK. A aplicação não tem a senha no código nem na variável de ambiente commitada.

**AWS Systems Manager Parameter Store** também guarda parâmetros e segredos (SecureString com KMS), com um nível gratuito mais generoso e menos recursos de rotação. Secrets Manager é a escolha quando rotação automática e replicação entre regiões são requisito.

### AWS Certificate Manager (ACM)

Emite e renova certificados TLS públicos para ALB, CloudFront, API Gateway e outros serviços integrados, sem custo do certificado em si. Também guarda certificados importados. **Para que serve:** HTTPS sem planilha de vencimento de certificado. Para certificados privados (mTLS interno), existe a **CA privada do ACM**.

### AWS WAF, Shield e Shield Advanced

- **AWS WAF.** Firewall de aplicação web na frente de CloudFront, ALB, API Gateway e AppSync. Regras contra SQL injection, XSS, bots, geo bloqueio, rate limit e listas de IPs. Você monta **web ACLs** com regras gerenciadas pela AWS ou suas.
- **AWS Shield Standard.** Proteção contra DDoS de rede e transporte, ligada por padrão, sem custo extra.
- **Shield Advanced.** Proteção DDoS com resposta da equipe da AWS, visibilidade maior e proteção de custo (alguns créditos se o ataque inflar a conta). Para aplicações críticas expostas à internet.

### AWS Network Firewall e AWS Firewall Manager

Já citados na seção de rede. Network Firewall inspeciona tráfego na VPC. Firewall Manager distribui políticas de segurança em todas as contas.

### Amazon GuardDuty

Detecção de ameaças. Analisa CloudTrail, Flow Logs, DNS e, opcionalmente, logs de EKS, S3, RDS e malware em EBS, e abre **findings** (achados) quando o comportamento parece comprometimento: mineração de cripto, chamada de API a partir de IP malicioso, desativação de trilha de auditoria, credencial usada de um país incomum.

**Para que serve:** alarme inteligente sem você escrever cada regra. Não bloqueia sozinho; você reage com EventBridge + Lambda ou com um playbook no Security Hub.

### Amazon Inspector

Varre continuamente **vulnerabilidades** em instâncias EC2, imagens no ECR e funções Lambda (CVEs em pacotes do SO e bibliotecas). Diferente do GuardDuty: Inspector olha “o que está instalado e está vulnerável”; GuardDuty olha “o que está se comportando como ataque”.

### Amazon Macie

Usa machine learning para achar **dados sensíveis** em buckets S3 (CPF em massa, chaves de acesso, números de cartão, PII) e alerta sobre buckets públicos ou compartilhados de forma arriscada.

### AWS Security Hub

Agrega findings de GuardDuty, Inspector, Macie, Firewall Manager e de ferramentas de terceiros num painel, pontua contra padrões (Foundational Security Best Practices, CIS) e permite automatizar a resposta. **Para que serve:** uma fila única de segurança, em vez de cinco consoles.

### Amazon Detective

Investigação. Quando o GuardDuty aponta um achado, o Detective monta gráficos de entidades (usuário, role, IP, instância) e o histórico de comportamento para responder “essa role fez o quê nas últimas semanas?”. É a ferramenta de análise depois do alerta, não o detector.

### AWS CloudTrail

Registra chamadas de API da conta: quem fez, o quê, quando, de qual IP, com sucesso ou erro. Trilha essencial de auditoria. Deve ir para um bucket S3 em conta separada, com versionamento e Object Lock, para o invasor da conta de produção não apagar a própria evidência.

Há também **CloudTrail Lake**, que consulta os eventos com SQL sem você montar um pipeline de logs.

**Importante:** CloudTrail não lê o conteúdo dos seus arquivos. Ele registra a ação `s3:GetObject`, não o texto do objeto.

### AWS Config

Inventário contínuo da configuração dos recursos e histórico de mudanças. Regras (gerenciadas ou suas, em Lambda) marcam recurso como **compliant** ou não: “todo bucket precisa de criptografia”, “nenhum security group abre a porta 22 para o mundo”, “RDS precisa ser Multi-AZ”.

**Para que serve:** conformidade e resposta a drift. Diferente do CloudTrail: Config mostra o **estado** do recurso ao longo do tempo; CloudTrail mostra **quem chamou a API**.

### AWS Audit Manager

Mapeia evidências (Config, CloudTrail, Security Hub) para frameworks de auditoria (PCI-DSS, HIPAA, e outros) e monta relatórios para o auditor. Não “certifica” a empresa; organiza a prova.

### AWS Artifact

Portal para baixar relatórios de conformidade **da AWS** (SOC, ISO, PCI) e aceitar acordos. É o armário de documentos da AWS sobre o data center e os serviços, usado quando o seu auditor pergunta como a nuvem foi avaliada.

### Amazon Security Lake

Centraliza logs de segurança (CloudTrail, VPC Flow, WAF, fontes de terceiros) no formato **OCSF** em um data lake no S3. **Para que serve:** os analistas e as ferramentas de SIEM consultarem um único lugar.

### AWS Signer e criptografia em trânsito

**AWS Signer** assina artefatos de código (Lambda, contêineres) para você implantar só o que foi assinado. Complementa o ACM, que cuida de TLS.

### Amazon Verified Permissions

Autorização nas **aplicações**, separada do IAM da conta AWS. Você descreve políticas em **Cedar** (“o usuário pode editar o documento se for o dono”) e a aplicação consulta o serviço a cada decisão. **Para que serve:** tirar ifs de permissão espalhados no código e ter um ponto auditável. Não substitui IAM para chamar APIs da AWS.

---

## 8. Gerenciamento e governança

### Amazon CloudWatch

Observabilidade operacional.

- **Métricas:** CPU da instância, invocações do Lambda, erros 5xx do ALB, métricas que você publica (`PutMetricData`).
- **Alarmes:** disparam SNS, Auto Scaling ou Systems Manager quando a métrica cruza um limiar.
- **Logs:** agente ou driver envia logs de aplicação e de sistema. **Logs Insights** consulta com uma linguagem própria.
- **Dashboards:** painéis.
- **CloudWatch Synthetics:** canários que simulam o usuário (um script acessa a URL) e alertam se o fluxo quebrar.
- **CloudWatch RUM:** performance real no navegador do usuário.
- **Evidently:** experimentos e feature flags com métricas.
- **Application Signals / ServiceLens:** visão de serviço com traços, métricas e SLOs.

**Para que serve:** saber se a aplicação está saudável e por que deixou de estar. Não substitui um SIEM; para segurança, CloudTrail e Security Hub são o caminho principal, embora logs do CloudWatch também alimentem detecções.

### AWS CloudFormation

Infraestrutura como código. Um template (YAML ou JSON) descreve recursos e as dependências. A AWS cria, atualiza ou apaga a **stack**. Se a atualização falha no meio, há rollback.

**Para que serve:** ambientes reproduzíveis, revisão de infraestrutura em pull request, fim do “cliquei no console e não lembro o que fiz”.

**StackSets** aplicam o mesmo template em várias contas e regiões.

### AWS CDK (Cloud Development Kit)

Você descreve a infraestrutura em TypeScript, Python, Java, C# ou Go. O CDK gera um template CloudFormation. É abstração em cima do CloudFormation, com constructos reutilizáveis (`ApplicationLoadBalancedFargateService` cria o serviço inteiro).

**AWS SAM (Serverless Application Model)** é um recorte do CloudFormation voltado a Lambda, API Gateway e DynamoDB, com CLI para testar funções localmente.

### AWS Systems Manager (SSM)

Conjunto de capacidades de operação, o “canivete” da frota:

- **Session Manager:** shell na instância sem abrir porta 22 e sem bastion, via IAM e auditoria.
- **Run Command e State Manager:** executam scripts e mantêm a configuração desejada em várias instâncias.
- **Patch Manager:** janelas de patch.
- **Parameter Store:** configuração e segredos simples.
- **Inventory e Fleet Manager:** o que está instalado e um console das máquinas.
- **Automation:** runbooks (reiniciar, criar AMI, remediar um achado).
- **Distributor:** instala pacotes (agente CloudWatch, antivírus) na frota.
- **Change Manager e Incident Manager:** fluxo de mudança aprovada e coordenação de incidente (planos de escalação, linhas do tempo).
- **OpsCenter:** fila de problemas operacionais (OpsItems) ligados a alarmes.

### AWS Trusted Advisor

Recomendações na conta: segurança (security groups abertos), custo (instâncias ociosas), performance, tolerância a falhas e cotas de serviço. O conjunto completo de checagens depende do plano de suporte (Business ou superior para todas).

### AWS Health Dashboard

Mostra eventos que afetam **os seus** recursos (degradação de um serviço numa AZ onde você opera) e a saúde geral da AWS. Diferente do status page público, porque filtra pelo que você usa.

### AWS Service Catalog

Catálogo de produtos aprovados (uma stack CloudFormation embrulhada como “produto”). O desenvolvedor lança “bucket padrão da empresa” ou “VPC padrão” sem liberdade para inventar uma topologia fora da norma.

### AWS License Manager

Rastreia licenças de software (Microsoft, Oracle e outras) usadas em EC2 e em máquinas on-premises, para não estourar o contrato nem pagar a mais por alocação errada.

### AWS Well-Architected Tool

Questionário oficial dos seis pilares (excelência operacional, segurança, confiabilidade, eficiência de performance, otimização de custo, sustentabilidade). Você responde sobre uma carga e recebe riscos e melhorias. É um processo de revisão, não um serviço que roda a aplicação.

### AWS Compute Optimizer e AWS Resilience Hub

- **Compute Optimizer:** recomenda dimensionamento de EC2, EBS, Lambda e ECS a partir do uso real.
- **Resilience Hub:** avalia se a arquitetura aguenta o objetivo de RTO/RPO que você declarou e sugere testes.

### AWS Proton

Plataforma interna de autosserviço: a equipe de plataforma publica templates (ambiente + serviço) e os times de aplicação preenchem parâmetros. Menos usado que CDK/Terraform com pipelines próprios; existe para padronizar deploys sem cada time aprender toda a conta.

### AWS Resource Access Manager (RAM)

Compartilha recursos (sub-redes, regras do Route 53 Resolver, licenças, grupos de usuários) entre contas da organização sem duplicá-los.

### AWS Service Quotas

Console e API das cotas (limites) da conta: quantas instâncias, quantos VPCs, quantas funções Lambda concorrentes. Você consulta e pede aumento. Estourar cota silenciosamente é uma causa comum de falha em pico.

### Amazon Managed Grafana e Amazon Managed Service for Prometheus

Observabilidade de estilo open source, gerenciada.

- **Managed Prometheus:** armazena métricas no formato Prometheus (muito usado com Kubernetes).
- **Managed Grafana:** monta dashboards em cima dessas métricas, do CloudWatch, do OpenSearch e de outras fontes.

**Para que serve:** times que já operam com Prometheus/Grafana e não querem manter o armazenamento e a disponibilidade dessas ferramentas.

### AWS Chatbot

Leva alarmes do CloudWatch e achados de segurança para salas no **Amazon Chime**, **Slack** ou **Microsoft Teams**, e permite alguns comandos operacionais de lá.

### AWS User Notifications

Central de notificações de eventos da AWS (Health, alarmes) para e-mail, chat e console, com regras do que cada time recebe.

---

## 9. Ferramentas de desenvolvedor

### AWS CodeCommit

Repositório Git privado gerenciado. Em 2024 a AWS deixou de aceitar **clientes novos** e recomenda outros Git (GitHub, GitLab, CodeCatalyst). Contas que já usam continuam no serviço. Para projeto novo, não é a escolha padrão.

### AWS CodeBuild

Compila, testa e produz artefatos (um jar, uma imagem Docker enviada ao ECR) em agentes gerenciados. Você descreve as fases num `buildspec.yml`. Substitui manter servidores de CI.

### AWS CodeDeploy

Publica uma nova versão em EC2, Lambda, ECS ou servidores on-premises, com estratégias: in-place, blue/green, canário. Integra com balanceador para tirar a instância do rodízio durante o deploy.

### AWS CodePipeline

Orquestra o fluxo: commit no Git → CodeBuild → aprovação manual → CodeDeploy (ou CloudFormation, ECS, outras ações). É o CI/CD que amarra as outras peças. Também se conecta a GitHub e Bitbucket.

### AWS CodeArtifact

Repositório de pacotes (npm, Maven, PyPI, NuGet) privado, com proxy para os repositórios públicos. **Para que serve:** controlar dependências, guardar pacotes internos e não depender só do registro público na hora do build.

### Amazon CodeCatalyst

Serviço mais novo de desenvolvimento integrado: repositórios, issues, pipelines e ambientes de desenvolvimento, com blueprints (por exemplo “API Lambda + DynamoDB”). É a resposta da AWS a uma experiência unificada de time, concorrendo com GitHub.

### AWS Cloud9

IDE no navegador, rodando numa instância EC2. Útil em workshops. Para desenvolvimento diário, editores locais (com AWS Toolkit) são mais comuns. Verifique se a conta ainda pode criar ambientes novos; o serviço teve restrições para clientes novos.

### AWS CloudShell

Terminal no navegador já autenticado com as credenciais do console, com AWS CLI instalada. **Para que serve:** um comando rápido sem configurar access key na sua máquina.

### AWS X-Ray

Rastreamento distribuído. A requisição ganha um ID e cada serviço (API Gateway, Lambda, ECS, SDK instrumentado) acrescenta um segmento. Você vê a cascata: onde o tempo foi gasto e qual chamada falhou.

**Para que serve:** achar a latência num sistema de vários serviços. CloudWatch Application Signals usa traços no mesmo espírito, no ecossistema OpenTelemetry.

### AWS FIS (Fault Injection Service)

Engenharia do caos controlada: injeta falha (derruba instâncias, corta rede, estressa CPU) em um alvo que você delimita, para provar que o failover funciona **antes** do incidente real.

### Amazon CodeGuru

Duas funções históricas: **Reviewer** (análise estática de pull requests Java e Python) e **Profiler** (onde a CPU gasta tempo em produção). Parte da experiência de assistente de código migrou para **Amazon Q Developer**. O profiler continua útil para achar linhas caras.

### AWS Application Composer e ferramentas de diagrama

**Application Composer** (no console) desenha aplicações serverless e gera o template SAM/CloudFormation correspondente. Ajuda a aprender a integração entre Lambda, API Gateway, DynamoDB e EventBridge.

---

## 10. Integração de aplicações

Sistemas grandes não se chamam só por HTTP síncrono. Filas, tópicos e máquinas de estado desacoplam e sobrevivem a pico e a falha parcial.

### Amazon SQS (Simple Queue Service)

Fila. O produtor envia uma mensagem; o consumidor puxa quando puder. Se o consumidor cair, a mensagem fica na fila (por até 14 dias).

- **Fila padrão:** throughput altíssimo, entrega pelo menos uma vez, ordem não garantida de ponta a ponta.
- **Fila FIFO:** ordem e deduplicação, com throughput menor (há modo de alto throughput).

**Visibility timeout:** ao receber a mensagem, ela fica invisível para os outros. Se o consumidor não apagar a mensagem a tempo, ela volta. **DLQ (dead-letter queue):** depois de N falhas, a mensagem vai para uma fila de análise, em vez de travar o processamento.

**Para que serve:** absorver pico (a API responde rápido e o trabalho pesado acontece depois), comunicar microsserviços sem acoplar a disponibilidade deles.

### Amazon SNS (Simple Notification Service)

Pub/sub. Um publicador manda uma mensagem a um **tópico**; todos os assinantes recebem: filas SQS, Lambda, e-mail, SMS, HTTP, push mobile.

Diferença prática: **SQS é fila de trabalho (um consumidor processa cada mensagem)**. **SNS é fan-out (vários interessados recebem a mesma notícia)**. O padrão clássico é SNS → várias filas SQS, uma por serviço assinante, para ninguém perder mensagem se estiver fora do ar.

### Amazon EventBridge

Barramento de eventos. Recebe eventos dos serviços AWS (“objeto criado no S3”, “EC2 entrou em estado running”), de aplicações suas e de parceiros SaaS, e encaminha por **regras** de padrão para destinos (Lambda, SQS, Step Functions, API).

**Para que serve:** arquitetura orientada a eventos sem cada produtor conhecer cada consumidor. Também tem **Scheduler** (agendamentos cron que invocam um alvo) e **Pipes** (ligar uma origem, um filtro e um destino com transformação no meio, sem Lambda só para repassar).

**Schema Registry** guarda o formato dos eventos para os times não quebrarem o contrato.

### AWS Step Functions

Máquina de estado visual. Cada passo é uma tarefa (Lambda, Batch, ECS, aprovação humana, espera, chamada de API de quase qualquer serviço AWS). Você descreve retentativa, catch, paralelismo e escolha.

**Para que serve:** processos de vários passos que não cabem numa função só — cadastro que valida, cobra, emite nota e envia e-mail; pipelines com compensação se um passo falhar. Dois tipos: **Standard** (duração longa, exatamente uma execução, cobrado por transição) e **Express** (alto volume, curta duração, cobrado por execução e duração).

### Amazon MQ

Message broker gerenciado compatível com **Apache ActiveMQ** e **RabbitMQ**. **Para que serve:** migrar aplicações que já falam JMS, AMQP ou MQTT para um broker desses, sem reescrever para SQS. Se você está desenhando do zero na AWS, SQS/SNS/EventBridge costumam ser mais simples.

### Amazon AppSync

API **GraphQL** gerenciada, com resolução direta para DynamoDB, RDS, Lambda, HTTP e OpenSearch, e com assinaturas em tempo real (WebSocket). **Para que serve:** backends de aplicativos móveis e web em que o cliente pede exatamente os campos que precisa e recebe atualizações ao vivo.

### AWS AppFabric

Normaliza logs de auditoria de aplicações SaaS (o funcionário fez o quê no Slack, no Microsoft 365, etc.) para ferramentas de segurança. É integração de **observabilidade de SaaS corporativo**, não um barramento geral de microsserviços.

### Amazon Simple Workflow Service (SWF)

Orquestrador antigo de fluxos. Step Functions o substitui em projetos novos. Ainda aparece em sistemas legados e em algumas integrações (por exemplo, trechos antigos do Amazon Simple Email e de mídia).

### Amazon EventBridge, SQS e SNS — como escolher

| Situação | Serviço |
| --- | --- |
| Um trabalho, um consumidor, precisa de buffer | SQS |
| Uma notícia, vários destinos | SNS |
| Reagir a eventos da AWS ou rotear por conteúdo, com vários produtores e consumidores soltos | EventBridge |
| Fluxo com ordem, espera, compensação e estado | Step Functions |
| Aplicação já escrita para RabbitMQ ou JMS | Amazon MQ |

---

## 11. Análise de dados

O desenho mais comum de analytics na AWS é o **data lake**: arquivos no S3, catálogo no Glue, consulta com Athena ou processamento com EMR/Spark, warehouse no Redshift para BI, streaming com Kinesis ou MSK.

### Amazon Athena

Consulta SQL **direto no S3** (CSV, JSON, Parquet, ORC) sem carregar um banco. Serverless: você paga pelos dados lidos. Particionar e usar Parquet/ORC reduz custo de forma dramática.

**Para que serve:** perguntas ad hoc em logs e no data lake. Não substitui o banco operacional nem um warehouse quando as consultas são pesadas, recorrentes e com muitos joins curados.

### AWS Glue

Serviço de integração de dados.

- **Crawlers** inspecionam o S3 (ou JDBC) e preenchem o **Data Catalog** (tabelas, colunas, partições). Athena, Redshift Spectrum e EMR usam esse catálogo.
- **Jobs** de ETL rodam Spark (ou Python shell) gerenciado para transformar dados.
- **DataBrew** é a versão visual, sem código, para analistas limparem dados.
- **Glue Studio** monta o job num diagrama.
- **Classifiers e sensitive data detection** ajudam a marcar PII.

**Para que serve:** o “encanamento” e o catálogo do lago. Sem catálogo, o S3 é só uma pilha de arquivos.

### Amazon EMR (Elastic MapReduce)

Cluster gerenciado de big data: **Spark, Hadoop, Hive, Presto/Trino, HBase, Flink**. Você processa volumes que não cabem num job Glue simples ou precisa de controle fino do cluster (versões, bootstrap, nós Spot).

**EMR Serverless** executa Spark e Hive sem você manter o cluster ligado.

### Amazon Kinesis

Família de streaming:

- **Kinesis Data Streams:** coleta eventos em tempo real (cliques, logs, sensores), retém por horas ou dias, e vários consumidores leem de forma independente. Você dimensiona shards (ou usa modo sob demanda).
- **Amazon Data Firehose:** entrega o stream em um destino (S3, Redshift, OpenSearch, HTTP) com buffer, transformação opcional via Lambda e quase nenhuma administração. Antes se chamava Kinesis Data Firehose.
- **Kinesis Video Streams:** ingestão de vídeo e áudio de câmeras e dispositivos, para análise ou arquivo.
- **Managed Service for Apache Flink:** processa streams com SQL ou Apache Flink (agregações em janela, detecção de padrão). O nome antigo era Kinesis Data Analytics.

**Para que serve:** qualquer coisa em que o valor está em reagir em segundos, não em consultar amanhã.

### Amazon MSK (Managed Streaming for Apache Kafka)

Kafka gerenciado (brokers, patch, opção serverless). **Para que serve:** o ecossistema Kafka que você já tem (produtores, consumidores, Kafka Connect, schema registry) sem operar ZooKeeper/KRaft e discos dos brokers. Escolha MSK quando o padrão da empresa é Kafka. Escolha Kinesis quando quer o serviço mais “nativo AWS” e aceita a API dele.

### Amazon OpenSearch Service

Busca e análise de logs, sucessora do Amazon Elasticsearch Service. Indexa documentos e responde consultas de texto, agregações e dashboards (OpenSearch Dashboards, o fork do Kibana).

**Para que serve:** busca do produto (“encontrar pedidos pelo nome do cliente”), observabilidade de logs em volume alto e detecção operacional. **OpenSearch Serverless** separa indexação e consulta e escala as duas.

Não confunda com CloudWatch Logs Insights: Insights é consulta pontual de logs operacionais. OpenSearch é uma plataforma de busca que você modela e opera (mesmo gerenciada).

### Amazon QuickSight

BI serverless. Conecta em Redshift, Athena, RDS, S3 e outras fontes, monta painéis interativos e cobra por sessão ou por usuário (leitor pode ser mais barato que autor). Tem recursos de perguntas em linguagem natural (**QuickSight Q** / pesquisa com IA, conforme a geração do produto).

**Para que serve:** o painel que a área de negócio abre, sem instalar Tableau ou Power BI — embora esses também se conectem aos mesmos bancos.

### AWS Lake Formation

Governança em cima do lago no S3 e do catálogo Glue: quem pode ver qual tabela e qual coluna, com concessão central. **Para que serve:** o lago deixar de ser um bucket que qualquer role da conta lê inteiro.

### Amazon DataZone

Catálogo de dados de negócio: publicadores registram conjuntos, consumidores pedem acesso, e o dado é encontrado por domínio (vendas, risco, logística), não só pelo nome do bucket.

### AWS Clean Rooms

Várias empresas cruzam dados **sem revelar a base bruta** uma à outra. Cada parte coloca a tabela; a sala executa a consulta aprovada e devolve só o resultado agregado. **Para que serve:** colaboração (varejo e marca, pesquisa) com restrição de privacidade.

### Amazon FinSpace

Ambiente de dados e análise voltado ao mercado financeiro (séries de mercado, notebooks, catálogos). Nicho; não é um warehouse geral.

### AWS Entity Resolution

Casa registros da mesma entidade vindos de bases diferentes (“este CRM e este cadastro são a mesma pessoa?”) sem você escrever toda a heurística de matching.

### Amazon CloudSearch

Busca gerenciada antiga. Para projeto novo, OpenSearch Service é a escolha usual.

### AWS Data Exchange e Amazon Data Firehose

**Data Exchange** é o mercado de conjuntos de dados de terceiros, assinados e entregues no S3. **Data Firehose** já foi descrito junto do Kinesis: é a mangueira que leva eventos até o destino com o mínimo de operação.

### Amazon Redshift

Descrito em bancos de dados. No desenho analítico, ele é o warehouse onde o modelo dimensional (fatos e dimensões) é consultado com performance previsível. O lago no S3 guarda o histórico cru; o Redshift guarda o que o BI pergunta toda hora.

### Zero-ETL e Amazon RDS/Aurora → Redshift

Integrações **zero-ETL** replicam dados transacionais para o Redshift (e, em alguns caminhos, para OpenSearch) sem você manter um pipeline Glue. **Para que serve:** analytics perto do tempo real sem construir e vigiar jobs de extração.

---

## 12. Machine learning e inteligência artificial

Há dois jeitos de usar IA na AWS: serviços prontos (uma API de visão, de fala, de texto) e a plataforma para construir o seu modelo (SageMaker e o hardware por baixo).

### Amazon SageMaker

Plataforma para o ciclo de machine learning clássico: preparar dados, treinar, ajustar hiperparâmetros, registrar o modelo, implantar endpoint de inferência, monitorar drift.

Peças que aparecem com frequência:

- **Studio:** IDE web do cientista de dados.
- **Notebooks:** instâncias Jupyter gerenciadas.
- **Training jobs:** treino distribuído em instâncias (inclusive GPU) que desligam ao terminar.
- **Processing jobs:** pré-processamento.
- **Pipelines:** orquestração do fluxo de ML.
- **Model Registry:** versões aprovadas para produção.
- **Endpoints:** inferência em tempo real; há também inferência assíncrona, serverless e batch transform.
- **Feature Store:** variáveis reutilizáveis entre treino e inferência, para não haver divergência.
- **Clarify:** explicabilidade e viés.
- **Ground Truth:** rotulagem de dados com força de trabalho humana.
- **JumpStart:** modelos e soluções de partida.
- **SageMaker Canvas:** interface sem código para analistas gerarem previsões.

**Para que serve:** quando o modelo é seu (os dados são seus, o algoritmo é seu ou fine-tuning é seu) e você precisa de controle do ciclo. Para “extrair texto de um PDF” ou “traduzir esta frase”, os serviços de IA prontos são mais curtos.

### Amazon Bedrock

Serviço gerenciado de **modelos fundacionais** (generative AI) de vários fornecedores (famílias como Anthropic Claude, Meta Llama, Amazon Titan, Mistral, Cohere, e outros conforme a região e o contrato), atrás de uma API única.

**Para que serve:** colocar geração de texto, chat, resumo, embeddings e, em alguns modelos, imagem no produto sem treinar um modelo do zero nem operar GPU.

Recursos associados:

- **Knowledge Bases para Bedrock:** RAG. Você aponta documentos no S3; o serviço quebra, gera embeddings e recupera trechos na hora da pergunta.
- **Agents para Bedrock:** o modelo decide chamar APIs suas para cumprir uma tarefa (consultar pedido, abrir chamado).
- **Guardrails:** filtros de conteúdo, negação de tópicos, mascaramento de PII na entrada e na saída.
- **Evaluations e model customization:** comparar modelos e adaptar com fine-tuning quando a API oferece.
- **Flows:** encadeia prompts, bases e condições.

Bedrock não substitui toda a engenharia de produto. Você ainda desenha o prompt, as ferramentas, a permissão e a avaliação de qualidade.

### Amazon Q

Família de assistentes:

- **Amazon Q Developer:** ajuda no IDE (código, explicação, transformação de código, inclusive upgrades de linguagem em alguns fluxos).
- **Amazon Q Business:** assistente interno da empresa, conectado a repositórios documentais (SharePoint, S3, wikis) com controle de permissão do que cada funcionário pode perguntar.
- **Amazon Q in QuickSight / in Connect:** perguntas em linguagem natural no BI e auxílio no contact center.

### APIs de IA prontas

Cada uma resolve uma modalidade. Você manda o dado e recebe a estrutura; não treina o modelo (embora várias aceitem adaptação).

| Serviço | Para que serve |
| --- | --- |
| **Amazon Rekognition** | Visão: objetos, rostos, texto em imagem, moderação de conteúdo, PPE em vídeo de câmera |
| **Amazon Textract** | OCR de documentos: extrai texto e também campos de formulário e tabelas, inclusive em PDF |
| **Amazon Transcribe** | Fala para texto. Há versão médica (Transcribe Medical) |
| **Amazon Polly** | Texto para fala, com vozes neurais |
| **Amazon Translate** | Tradução de texto |
| **Amazon Comprehend** | PLN: sentimento, entidades, frases-chave, idioma, classificação. **Comprehend Medical** extrai entidades clínicas |
| **Amazon Lex** | Conversas (o mesmo tipo de tecnologia do Alexa) para chatbots com intenções e slots |
| **Amazon Forecast** | Previsão de séries temporais (demanda, tráfego). Parte do roadmap foi direcionada a outras ferramentas; confirme antes de projeto novo |
| **Amazon Personalize** | Recomendação (produtos, conteúdo) treinada com as interações dos seus usuários |
| **Amazon Fraud Detector** | Modelos de fraude em cadastro e pagamento |
| **Amazon Kendra** | Busca inteligente em documentos da empresa, com conectores e perguntas em linguagem natural. Knowledge Bases do Bedrock cobre parte dos casos novos de “perguntar aos documentos” |
| **Amazon Polly, Transcribe e Lex juntos** | Base de URA e de atendimento por voz: fala → texto → intenção → resposta falada |
| **AWS Panorama** | Appliance para visão computacional na borda (câmeras no chão de fábrica), quando o vídeo não deve ir inteiro para a região |
| **Amazon Monitron** | Sensores e serviço para manutenção preditiva de equipamento industrial |
| **Amazon Lookout for Vision / Equipment / Metrics** | Detecção de anomalia em imagem industrial, sensores e métricas. Vários Lookouts tiveram o ciclo de vida encerrado ou restrito; trate como legado e confirme |
| **Amazon CodeGuru** | Já citado em ferramentas de desenvolvedor: revisão e perfil de código |
| **Amazon DevOps Guru** | Olha métricas e logs e aponta comportamento anômalo de operação (latência, erros) com hipótese de causa |

### Infraestrutura de IA

- **Instâncias P, G, Trn, Inf do EC2.** GPU NVIDIA (P e G) para treino e inferência gerais; **Trainium** (Trn) e **Inferentia** (Inf) são chips da AWS para treino e inferência com custo menor em cargas compatíveis.
- **AWS Neuron.** SDK para compilar modelos nos chips Trainium e Inferentia.
- **Amazon EC2 Capacity Blocks e UltraClusters.** Reserva de blocos de GPU e redes de baixíssima latência entre centenas de aceleradores, para treino grande.
- **AWS Deep Learning AMIs e containers.** Imagens com drivers, CUDA e frameworks (PyTorch, TensorFlow) já instalados.
- **SageMaker HyperPod.** Cluster de treino de longa duração, com resiliência (substitui nó que falha) para modelos grandes.

### Amazon Augmented AI (A2I)

Insere revisão humana num fluxo de ML: quando a confiança do Textract ou do Rekognition é baixa, a tarefa vai para uma fila de pessoas e depois volta ao pipeline.

---

## 13. Migração e transferência

### AWS Migration Hub

Painel que acompanha migrações vindas de várias ferramentas (servidores, bancos) num só lugar, com status por aplicação.

### AWS Application Discovery Service

Coleta inventário do data center: servidores, processos, dependências de rede. **Para que serve:** saber o que existe e o que fala com o quê **antes** de mover. Há coleta sem agente (via conector no hypervisor) e com agente.

### AWS Application Migration Service (MGN)

Principal ferramenta de **lift-and-shift**. Instala um agente de replicação no servidor de origem (físico, virtual, outra nuvem) e replica os discos para a AWS. No cutover, sobe uma instância EC2 correspondente. Substitui o antigo Server Migration Service.

**Para que serve:** mudar o servidor de lugar com pouca alteração. Não moderniza a aplicação; isso é uma fase seguinte (contêiner, banco gerenciado, etc.).

### AWS Database Migration Service (DMS)

Replica dados entre bancos com a origem ainda no ar. Origem e destino podem ser Oracle, SQL Server, MySQL, PostgreSQL, MongoDB, S3, Redshift, DynamoDB, Aurora, entre outros. **CDC (change data capture)** mantém o destino sincronizado até o corte.

**Schema Conversion Tool (DMS SCT)** converte schema e código SQL de um motor para outro (Oracle → PostgreSQL, por exemplo) e aponta o que não converte sozinho.

**Para que serve:** trocar de banco ou levar o banco para RDS/Aurora sem uma janela de parada longa.

### AWS DataSync e família Snow

Já descritos em armazenamento. DataSync é cópia contínua pela rede. Snowball é cópia em aparelho físico quando a rede não dá conta.

### AWS Transfer Family

Servidor gerenciado de **SFTP, FTPS e FTP** cujo backend é o S3 ou o EFS. Parceiros que só sabem enviar arquivo por SFTP passam a gravar direto no lago, sem você manter uma VM com OpenSSH.

### AWS Mainframe Modernization

Ferramentas para analisar, transformar (por exemplo Cobol) e redefinir plataforma de cargas de mainframe, ou para rehospedar em runtime gerenciado. Nicho e de projeto longo.

### Migration Evaluator

Coleta utilização do ambiente atual e estima custo na AWS. É a etapa de business case, antes do MGN.

### AWS App2Container

Analisa uma aplicação Java ou .NET rodando numa VM e gera a imagem de contêiner, o pipeline e os artefatos ECS/EKS. **Para que serve:** o primeiro empacotamento, não a reescrita.

---

## 14. Mídia

Serviços para vídeo ao vivo, sob demanda e para estúdios. A maioria dos sistemas web não precisa deles.

| Serviço | Para que serve |
| --- | --- |
| **AWS Elemental MediaConvert** | Transcodifica arquivos (gera HLS/DASH em várias resoluções a partir de um mezzanine) |
| **AWS Elemental MediaLive** | Codifica vídeo **ao vivo** para transmissão |
| **AWS Elemental MediaPackage** | Empacota e protege (DRM) o stream para diferentes dispositivos |
| **AWS Elemental MediaStore / MediaConnect** | Armazenamento otimizado para mídia e transporte confiável de contribuição (o sinal da produtora até a nuvem) |
| **AWS Elemental MediaTailor** | Insere anúncios personalizados (ssai) no stream |
| **Amazon Interactive Video Service (IVS)** | Transmissão ao vivo gerenciada, de implementação mais simples que a cadeia Elemental completa, inclusive com chat e baixa latência |
| **Amazon Kinesis Video Streams** | Ingere vídeo de dispositivos para armazenamento, reprodução e análise (Rekognition etc.) |
| **AWS Deadline Cloud** | Render farm gerenciada para estúdios (jobs de renderização) |
| **Amazon Nimble Studio** | Estações virtuais de criação de conteúdo (o serviço passou por mudanças de ciclo de vida; confirme a oferta atual de VDI para estúdio) |
| **AWS Elemental Appliances** | Hardware on-premises da família Elemental que se integra à nuvem |

Fluxo típico de streaming: MediaLive (ou contribution via MediaConnect) → MediaPackage → CloudFront → player. Arquivos gravados: S3 → MediaConvert → S3 → CloudFront.

---

## 15. Front-end web e mobile

### AWS Amplify

Conjunto para aplicações web e mobile.

- **Amplify Hosting:** publica SPA e sites estáticos (React, Next, Vue) com CI a partir do Git, HTTPS e CDN.
- **Bibliotecas Amplify:** autenticação (Cognito), API (AppSync ou REST), armazenamento (S3) e banco no cliente, com menos código de integração.
- **Amplify Studio:** liga um desenho (inclusive Figma) a componentes e a um backend.

**Para que serve:** produto digital em que o mesmo time faz front e um backend serverless. Arquiteturas com plataforma própria podem usar só o Hosting, ou nem isso (CloudFront + S3 direto).

### Amazon API Gateway, AppSync, Cognito e Location

API Gateway, AppSync e Cognito já foram descritos: são as portas de API e de identidade que o front consome.

**Amazon Location Service** oferece mapas, geocodificação, rotas e cercas geográficas sem você operar um servidor de mapas. Os dados vêm de provedores (Esri, HERE, GrabMaps). **Para que serve:** rastrear frota, “lojas perto de mim”, comprovação de visita.

### AWS Device Farm

Testa o app Android, iOS ou web em **aparelhos reais** na nuvem da AWS, em paralelo. **Para que serve:** achar falha que o emulador não reproduz.

### Amazon Pinpoint

Mensagens para usuários finais: push, e-mail, SMS e voz, com segmentos e campanhas. Partes do engajamento evoluíram para outros serviços (por exemplo **Amazon End User Messaging** para SMS e push). Para e-mail transacional de aplicação, **Amazon SES** é o serviço base.

---

## 16. Computação para o usuário final

Desktop e produtividade na nuvem, no sentido de “o funcionário acessa um ambiente”, não de “a aplicação da empresa”.

| Serviço | Para que serve |
| --- | --- |
| **Amazon WorkSpaces** | Desktop como serviço (Windows ou Linux). O usuário abre um cliente e trabalha numa área de trabalho na AWS. Útil para terceiros, BYOD e ambientes controlados |
| **Amazon WorkSpaces Thin Client** | Dispositivo físico barato que só acessa o WorkSpaces |
| **Amazon AppStream 2.0** | Entrega **uma aplicação** (não o desktop inteiro) via streaming. O programa roda na AWS; o usuário vê a tela. Bom para software pesado (CAD, legado) sem instalar na máquina local |
| **Amazon WorkSpaces Secure Browser** | Navegador isolado para acessar aplicações web internas sem VPN completa |
| **Amazon WorkMail** | E-mail e calendário corporativos gerenciados, compatíveis com clientes IMAP/Exchange |
| **Amazon WorkDocs** | Compartilhamento e colaboração em documentos. O ciclo de vida comercial mudou; para projeto novo, confirme a oferta ou use outra suíte |
| **Amazon Chime SDK** | Componentes de áudio, vídeo e chat para você embutir conferência no seu produto. O aplicativo Amazon Chime de reuniões foi descontinuado; o SDK permanece para desenvolvedores |

---

## 17. Internet das Coisas (IoT)

### AWS IoT Core

Broker gerenciado para dispositivos. Eles se conectam por **MQTT, MQTT over WSS, HTTP ou LoRaWAN** (via IoT Core for LoRaWAN), autenticam com certificado X.509 e publicam telemetria. Regras encaminham a mensagem para S3, Lambda, Kinesis, DynamoDB, etc.

**Para que serve:** a porta de entrada de milhões de dispositivos. Você não mantém o broker MQTT.

**Device Shadow** guarda o último estado desejado e o estado reportado, para o dispositivo que estava offline receber o comando quando voltar.

### AWS IoT Greengrass

Runtime que roda **na borda** (um gateway no chão de fábrica, um Raspberry Pi industrial). Executa Lambda e contêineres locais, sincroniza com o IoT Core e continua operando se a conexão cair.

**Para que serve:** decisão local (parar a esteira) que não pode esperar a região AWS.

### Outros serviços de IoT

| Serviço | Para que serve |
| --- | --- |
| **AWS IoT Device Management** | Inventário, grupos, jobs de atualização de firmware (OTA) e túnel para diagnosticar um dispositivo |
| **AWS IoT Device Defender** | Auditoria de configuração e detecção de comportamento anormal da frota (dispositivo falando com IP estranho) |
| **AWS IoT Events** | Detecta padrões em telemetria (“temperatura alta por 5 minutos e válvula fechada”) e dispara ações |
| **AWS IoT Analytics** | Pipeline antigo de limpeza e análise de telemetria. Cargas novas costumam ir para Kinesis, S3 e Athena/SageMaker |
| **AWS IoT SiteWise** | Modela equipamentos industriais (planta, linha, ativo), coleta de historiadores OPC-UA e calcula métricas (OEE) |
| **AWS IoT TwinMaker** | Gêmeos digitais: relaciona dados de sensores a um modelo 3D da instalação |
| **AWS IoT FleetWise** | Coleta de dados de veículos (sinais do carro) de forma seletiva, para não enviar o barramento CAN inteiro |
| **FreeRTOS** | Sistema operacional de tempo real para microcontroladores, com bibliotecas para conectar ao IoT Core |
| **AWS IoT ExpressLink** | Módulos de conectividade certificados que abstraem a ligação com o IoT Core para hardware simples |
| **AWS IoT Button** | Botão de hardware de demonstração (legado educacional); a ideia era disparar uma Lambda com um clique físico |

---

## 18. Aplicações de negócio

Produtos de usuário final da AWS, não blocos de infraestrutura.

| Serviço | Para que serve |
| --- | --- |
| **Amazon Connect** | Contact center na nuvem (voz, chat, tarefas). Substitui PABX e discador com pay-per-use. Integra Lex (bot), Polly, Lambda e CRM |
| **Amazon Connect Contact Lens** | Analisa chamadas e chats (sentimento, conformidade de script, temas) |
| **Amazon SES (Simple Email Service)** | Envio e recebimento de e-mail em escala para a **aplicação** (confirmação de cadastro, nota fiscal). Não é caixa postal de funcionário (isso seria WorkMail) |
| **Amazon Pinpoint / End User Messaging** | Campanhas e mensagens transacionais SMS, push e e-mail para usuários do produto |
| **Amazon WorkDocs, WorkMail, Chime** | Já descritos em computação de usuário final |
| **AWS Supply Chain** | Aplicação de visibilidade de cadeia de suprimentos em cima de dados que a empresa já tem |
| **AWS Wickr** | Mensageiro com criptografia de ponta a ponta para organizações que precisam desse controle |
| **Amazon One** | Identificação biométrica pela palma da mão, usada em pagamentos e acesso físico em alguns comércios |
| **Alexa for Business** | Descontinuado como oferta ampla; habilidades Alexa ainda existem no ecossistema de dispositivos, mas não é peça de arquitetura corporativa nova |
| **AWS AppFabric** | Já citado: normaliza auditoria de aplicações SaaS |
| **Amazon Simple Email e SNS SMS** | SNS também envia SMS e push móvel; SES é o especialista em e-mail |

---

## 19. Gestão financeira da nuvem

A conta AWS cresce por recurso esquecido tanto quanto por tráfego legítimo. Estes serviços existem para ver, atribuir e limitar gasto.

| Serviço | Para que serve |
| --- | --- |
| **AWS Cost Explorer** | Gráficos e filtros de custo e uso (por serviço, conta, tag, região), com previsão |
| **AWS Budgets** | Orçamentos com alerta por e-mail/SNS quando o gasto ou o uso previsto cruza o limite. Pode ter ações (negar compra de instância fora do padrão, em alguns cenários) |
| **AWS Cost and Usage Report (CUR)** | Relatório detalhado, no S3, linha a linha. É a fonte da verdade para chargeback e para ferramentas externas |
| **AWS Cost Categories e cost allocation tags** | Agrupa custos do jeito do organograma (time, produto, ambiente). Tag sem ativação no faturamento não aparece no Explorer |
| **AWS Pricing Calculator** | Estima preço **antes** de criar o recurso. Não olha a sua conta; é uma calculadora pública |
| **AWS Billing Conductor** | Remarca preços para mostrar a contas-membro um valor diferente do custo real (útil para revenda interna ou MSPs) |
| **Reserved Instance e Savings Plans reports** | Mostram cobertura e utilização do compromisso. Compromisso sem utilização é prejuízo |
| **AWS Compute Optimizer** | Já citado: recomenda tamanho certo, o que é ao mesmo tempo performance e custo |
| **S3 Storage Lens** | Visão de uso, atividade e custo dos buckets |
| **AWS Trusted Advisor** | Checagens de custo (endereços IP ociosos, volumes órfãos, load balancers sem alvo) |
| **Credits, Free Tier e faturas consolidadas** | Organizations junta a fatura. Créditos (programas educacionais, Activate para startups) abatem a conta |

Prática que mais reduz surpresa: taguear recurso com `projeto` e `ambiente`, ativar a tag no billing, criar um budget por conta e revisar o Explorer toda semana.

---

## 20. Blockchain

| Serviço | Para que serve |
| --- | --- |
| **Amazon Managed Blockchain** | Opera nós de blockchain. Historicamente Hyperledger Fabric; hoje o uso mais comum é **nós de redes públicas**, em especial acesso ao Bitcoin e Ethereum (consultas e transações) sem você manter o nó |
| **Amazon QLDB** | Livro-razão **centralizado** e verificável. Não é uma rede blockchain entre organizações. Ver a seção de bancos de dados sobre o ciclo de vida |

Se várias organizações que não confiam umas nas outras precisam escrever no mesmo livro, estuda-se uma rede blockchain. Se só a sua empresa precisa de histórico à prova de adulteração, um ledger centralizado ou um log com hash (QLDB, ou CloudTrail em bucket com Object Lock) é operacionalmente mais simples.

---

## 21. Games

| Serviço | Para que serve |
| --- | --- |
| **Amazon GameLift Servers** | Hospeda servidores de jogo multiplayer dedicados: sobe processos de sessão, escolhe região perto dos jogadores e escala a frota (inclusive Spot) |
| **Amazon GameLift Streams** | Transmite a execução do jogo a partir da nuvem para o dispositivo do jogador (cloud gaming) |
| **AWS Lumberyard** | Motor de jogo antigo da AWS; o sucessor comunitário é o Open 3D Engine (O3DE), que não é um serviço gerenciado da conta AWS |

Backend de jogo (contas, inventário, ranking) em geral usa os serviços gerais: Cognito, DynamoDB, Lambda, API Gateway. GameLift entra na parte de **sessão em tempo real** com servidor autoritativo.

---

## 22. Tecnologias quânticas

### Amazon Braket

Acesso a computadores quânticos (de parceiros de hardware, como supercondutores e íons aprisionados) e a simuladores, com um SDK único. Você submete o circuito e paga por tarefa.

**Para que serve:** pesquisa e experimentação de algoritmos quânticos (otimização, simulação química, criptografia pós-quântica em estudo). Não acelera uma API web comum. Computação quântica prática de uso geral ainda é de pesquisa; o serviço existe para esse trabalho ter hardware sem a empresa comprar o laboratório.

---

## 23. Satélite

| Serviço | Para que serve |
| --- | --- |
| **AWS Ground Station** | Antenas gerenciadas da AWS que falam com satélites em órbita. Você agenda o contato, baixa a telemetria para a VPC e processa com EC2, S3 ou SageMaker |
| **AWS Ground Station Wide Area Network** | Leva o dado do contato até a região de processamento |

**Para que serve:** operadores de satélite que não querem construir estações terrenas em vários continentes. É um serviço de nicho aeroespacial, não um componente de aplicação corporativa.

---

## 24. Suporte e capacitação

| Serviço | Para que serve |
| --- | --- |
| **AWS Support** | Planos Developer, Business, Enterprise On-Ramp e Enterprise. Mudam tempo de resposta, canal (chat, telefone) e programas como Infrastructure Event Management |
| **AWS Trusted Advisor** | Checagens automáticas; o conjunto completo acompanha o plano de suporte |
| **AWS Health** | Eventos que afetam seus recursos |
| **AWS re:Post** | Fórum comunitário sucessor dos AWS Forums |
| **AWS Training and Certification** | Treinamentos e exames (Cloud Practitioner, Solutions Architect, Developer, SysOps, e especialidades) |
| **AWS Skill Builder** | Cursos digitais, alguns gratuitos, laboratórios |
| **AWS Activate** | Créditos e suporte para startups |
| **AWS IQ** | Marketplace histórico para contratar especialistas em projetos AWS |
| **AWS Professional Services e parceiros** | Consultoria da AWS e da rede de parceiros (APN) para projetos que excedem o suporte reativo |
| **AWS Countroom / prescriptive guidance** | A AWS publica **Prescriptive Guidance** (guias de migração e arquitetura) e **Architecture Center** (diagramas de referência). São documentação, não um serviço faturável |
| **AWS Well-Architected** | Framework e ferramenta de revisão, já descritos |
| **Service Quotas e Trusted Advisor** | Evitam descobrir no meio de um lançamento que a conta não pode abrir mais instâncias |

Planos de suporte não mudam o preço dos recursos. Mudam o acesso a humanos e a ferramentas quando algo quebra.

---

## 25. Como os serviços se encaixam

### Site ou API em três camadas

1. **Route 53** resolve o domínio.
2. **CloudFront** entrega estáticos e pode proteger com **WAF**.
3. **ALB** distribui para **EC2** ou para um serviço **ECS/Fargate**.
4. A aplicação lê e grava em **RDS/Aurora** ou **DynamoDB**, guarda arquivos no **S3** e segredos no **Secrets Manager**.
5. Tudo vive numa **VPC** com sub-redes privadas para o banco.
6. **IAM roles** autorizam a aplicação. **CloudWatch** alerta. **CloudTrail** audita.

### API serverless

1. **API Gateway** ou URL de função recebe HTTPS.
2. **Lambda** executa a regra de negócio.
3. **DynamoDB** guarda estado.
4. **SQS** amortece trabalho assíncrono; outra Lambda consome a fila.
5. **Cognito** autentica o usuário final.

### Dados

1. A aplicação operacional fica em **Aurora**.
2. **Zero-ETL** ou **DMS** leva os dados ao **Redshift**, e eventos vão ao **S3** via **Firehose**.
3. **Glue** cataloga. **Athena** responde perguntas soltas. **QuickSight** publica o painel.
4. **Lake Formation** restringe quem vê qual coluna.

### Conta empresarial mínima

1. **Organizations** + **Control Tower** criam contas de log, auditoria e cargas.
2. **IAM Identity Center** dá acesso às pessoas.
3. **CloudTrail** organizacional grava na conta de log.
4. **GuardDuty** e **Security Hub** ligados em todas as contas.
5. **Budgets** alertam gasto.
6. Infraestrutura sai de **CloudFormation** ou **CDK**, não de clique manual.

### O que não misturar

- **S3 não é sistema de arquivos de banco.** Banco relacional usa RDS/Aurora (armazenamento gerenciado) ou EBS se você operar o motor no EC2.
- **IAM não autentica o cliente do aplicativo.** Para isso, Cognito ou outro IdP, e o backend assume uma role.
- **CloudFront não substitui o balanceador** quando você precisa de regras de roteamento e alvos privados na VPC. Eles se complementam: CloudFront na frente, ALB na origem.
- **SQS não entrega para vários consumidores a mesma mensagem de trabalho** no modelo competidor. Se vários sistemas precisam da mesma mensagem, use SNS ou EventBridge na frente.
- **Redshift não atende o checkout do e-commerce.** Checkout pede banco transacional. Redshift atende o relatório do checkout.

---

## 26. Glossário rápido

| Termo | Significado |
| --- | --- |
| **Região** | Conjunto isolado de AZs numa geografia |
| **AZ** | Data center isolado dentro da região |
| **VPC** | Rede privada virtual sua |
| **AMI** | Imagem de máquina para criar instâncias EC2 |
| **Bucket** | Contêiner de objetos no S3 |
| **IAM role** | Identidade assumida temporariamente por pessoa ou serviço |
| **Serverless** | Você não provisiona nem corrige o servidor; paga por uso (Lambda, Fargate, DynamoDB sob demanda, Athena) |
| **Alta disponibilidade** | Continuar no ar se uma AZ falhar (Multi-AZ, vários alvos no balanceador) |
| **DR** | Recuperação se uma região inteira falhar (outra região, backups, Global Database) |
| **RPO** | Quanto de dado você aceita perder (janela de replicação) |
| **RTO** | Quanto tempo aceita ficar parado até voltar |
| **Escalabilidade horizontal** | Mais máquinas ou mais funções |
| **Escalabilidade vertical** | Uma máquina maior |
| **Lift-and-shift** | Mudar o servidor como está (MGN) |
| **Refactor / re-architect** | Passar a usar serviços gerenciados e desenho nativo de nuvem |
| **Data lake** | Dados brutos e refinados em objeto (S3), com catálogo |
| **Data warehouse** | Dados modelados para consulta analítica (Redshift) |
| **CIDR** | Faixa de endereços IP da VPC ou da sub-rede |
| **Least privilege** | Conceder só as ações e os recursos necessários |

---

## Referências

- [Lista de produtos AWS](https://aws.amazon.com/products/)
- [Visão geral oficial por categoria](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html)
- [Modelo de responsabilidade compartilhada](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [Framework Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [Regiões e zonas de disponibilidade](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)

Documento de estudo. Antes de escolher um serviço para produção, confirme região disponível, preço e se a AWS não anunciou sucessor ou encerramento — o catálogo muda ao longo do ano.

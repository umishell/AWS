# Roteiro de Falas e Slides — Amazon Web Services

### Apresentação de 15 a 20 minutos

---

> **Como ler este documento**
> Cada bloco tem: o título do slide, o conteúdo visual sugerido e, em itálico, a fala do apresentador.
> O tempo estimado está entre colchetes no título de cada slide.
> O roteiro foi escrito para cerca de 18 minutos. Se a turma já conhece nuvem, encurte o Bloco 1 e amplie o Bloco 6.

---

## SLIDE 0 — Capa [30 s]

**Conteúdo visual**

```
Amazon Web Services (AWS)
— o que é, como cobra, o que hospeda e como suporta microsserviços —
[nome, disciplina, data]
```

*Enquanto o slide fica na tela: "Bom dia/boa tarde. Vou apresentar a Amazon Web Services respondendo seis perguntas que passam do 'o que é' até 'como ela sustenta microsserviços'. São 15 a 20 minutos, e o catálogo de serviços vai aparecer em tabela quando o assunto for amplo demais para falar um a um."*

---

## BLOCO 1 — O QUE É A AWS [~ 2 min]

---

### SLIDE 1 — O que é a AWS [1 min]

**Conteúdo visual**

```
O que é a AWS?

A nuvem pública da Amazon — lançada em 2006
Mais de 200 serviços de infraestrutura e plataforma
39 regiões, 124 zonas de disponibilidade, 750+ pontos de presença
Receita anual (2025): ~US$ 129 bilhões
Participação no mercado de nuvem (Q2 2026): AWS 28% · Azure 20% · Google 15%

┌─────────────────────────────────────────────────────┐
│  IaaS                PaaS               SaaS (menor)│
│  EC2, VPC, EBS       Lambda, RDS,       Connect,    │
│  "máquina e rede"    Fargate, Beanstalk  WorkMail   │
└─────────────────────────────────────────────────────┘
```

*"A Amazon Web Services começou em 2006 com dois serviços: S3, para guardar arquivo, e EC2, para ligar uma máquina virtual. A origem foi prática: a própria Amazon precisava provisionar infraestrutura rápido para crescer, transformou isso em produto e passou a vender para qualquer um."*

*"Hoje o catálogo passa de 200 serviços. Eles se dividem em três modelos. IaaS é a camada mais baixa: você aluga a máquina e instala tudo em cima — EC2, a rede VPC e o disco EBS. PaaS é o que o Lambda, o RDS e o Fargate fazem: o runtime, o banco ou o orquestrador já vêm operados; você entrega código ou configuração. Há ainda alguns produtos SaaS — contact center, e-mail — mas são minoria. A maior parte do que se usa na AWS é IaaS e PaaS."*

---

### SLIDE 2 — Por que ir para a AWS [1 min]

**Conteúdo visual**

```
Por que usar a AWS?

❶ Custo variável, não fixo
   Pague a hora da máquina e o GB guardado. Não compre servidor.

❷ Velocidade
   Uma instância nasce em minutos. Um banco gerenciado, também.

❸ Escala automática
   A capacidade acompanha o pico sem compra antecipada de hardware.

❹ Responsabilidade compartilhada
   AWS cuida da segurança  da  nuvem (hardware, hypervisor).
   Você cuida da segurança  na   nuvem (SO, dados, IAM, patches).

Região Brasil → sa-east-1 (São Paulo)
```

*"O argumento econômico é trocar gasto fixo de data center por gasto variável. O argumento operacional é tempo: uma semana para montar um servidor em 2005 virou minutos na nuvem. Mas tem uma responsabilidade que não foi embora: a AWS cuida do prédio e do hardware. O sistema operacional da instância, a configuração do banco, as políticas de acesso e os dados continuam sendo responsabilidade de quem usa. A fatura do recurso esquecido ligado também."*

*"Para quem está no Brasil, a região é São Paulo, código sa-east-1. Usar essa região mantém o dado no país e reduz a latência, mas o preço costuma ser um pouco maior que o da Virginia."*

---

## BLOCO 2 — PRINCIPAIS SERVIÇOS [~ 5 min]

---

### SLIDE 3 — Como organizar os serviços [30 s]

**Conteúdo visual**

```
Mais de 200 serviços — como escolher o que importa?

Cada serviço responde a uma pergunta de arquitetura:

  Onde o código roda?          → Computação
  Onde o arquivo e dado ficam? → Armazenamento e banco
  Como o usuário chega lá?     → Rede e entrega
  Quem pode fazer o quê?       → Segurança e operação
  Como os serviços conversam?  → Integração  (detalhes no Bloco 6)
  Como fazer IA?               → IA           (detalhes no Bloco 3)
```

*"Em vez de listar 200 nomes, vou agrupar por pergunta de arquitetura. Integração e IA têm slides próprios depois, então aqui aparecem só de passagem."*

---

### SLIDE 4 — Computação [1 min]

**Conteúdo visual**

```
Computação — onde o código roda

Amazon EC2              Servidor virtual. Você escolhe SO, tamanho e paga enquanto
                        a instância está ligada. Base histórica da AWS.

  ├─ Tipos de instância  T (barato/burstable) · M (uso geral) · C (CPU)
  │                      R (memória) · P/G (GPU/IA) · I (disco rápido)
  ├─ Auto Scaling        Sobe/desce instâncias conforme a demanda
  └─ Load Balancer (ALB) Distribui o tráfego. Tira do rodízio o que está doente.

AWS Lambda              Executa função em resposta a evento (HTTP, fila, upload,
                        agenda) sem provisionar servidor. Escala até zero.
                        → Papel em microsserviços: Bloco 6

ECS / Fargate / EKS     Orquestração de contêineres.
                        → Detalhes no Bloco 6 (microsserviços)
```

*"EC2 é o servidor virtual clássico: você sobe uma máquina Linux ou Windows, instala o que quiser e paga enquanto ela estiver ligada. Com o Auto Scaling, a frota cresce no pico e encolhe fora dele. O Application Load Balancer reparte o tráfego entre as máquinas."*

*"Lambda é o extremo oposto: você sobe uma função em Python ou Node e a AWS a dispara quando chegar um evento — uma requisição HTTP, um arquivo que subiu, uma mensagem numa fila, um horário. Não há servidor para gerenciar e não há cobrança quando ninguém chama. ECS, Fargate e EKS são para contêineres; vou detalhar no bloco de microsserviços."*

---

### SLIDE 5 — Armazenamento e Banco de Dados [1 min 30 s]

**Conteúdo visual**

```
Armazenamento

Amazon S3      Objeto. Arquivo num bucket por chave, via API.
               Não é disco montado. Durabilidade de 11 noves.
               Serve para: backup, site estático, data lake, origem de CDN.
               Classes: Standard → IA → Glacier → Deep Archive (mais barato, mais lento)

Amazon EBS     Disco de bloco de UMA instância EC2. É o HD da VM.

─────────────────────────────────────────────────────────────────────
Banco de dados

Amazon RDS     Relacional gerenciado: MySQL · PostgreSQL · Oracle · SQL Server
               A AWS instala, faz patch, backup e Multi-AZ (réplica síncrona).
               Aurora = variante AWS com desempenho maior.

Amazon DynamoDB  NoSQL chave-valor. Sem servidor para administrar.
                 Latência de milissegundos em qualquer escala.
                 Serve para: sessão, carrinho, perfil, telemetria.
                 Não substitui SQL quando há muitos joins.
```

*"S3 é o serviço de armazenamento mais famoso da AWS. A ideia é simples: você manda um arquivo, ele ganha uma chave num bucket e fica armazenado em várias zonas de disponibilidade ao mesmo tempo. Não é um disco que você monta no sistema operacional, é uma API. As classes de armazenamento existem para pagar menos por dado frio: o Glacier, por exemplo, custa muito menos que o Standard, mas a leitura pode demorar minutos ou horas."*

*"EBS é o disco da instância EC2. Simples assim: o SO da VM mora aqui."*

*"No banco de dados, RDS é o relacional gerenciado. Você escolhe o motor — MySQL, Postgres, Oracle, SQL Server — e a AWS cuida da instalação, do patch e do backup. Se você habilitar Multi-AZ, há uma réplica síncrona em outra zona e failover automático. DynamoDB é o NoSQL da casa: tabela, chave de partição, sem servidor para dimensionar."*

---

### SLIDE 6 — Rede e Entrega [1 min]

**Conteúdo visual**

```
Rede e entrega de conteúdo

Amazon VPC        Rede privada da conta. Você define CIDR e sub-redes.
                  Padrão: balanceador na sub-rede pública → aplicação → banco
                  (banco nunca com rota direta para a internet)

Amazon Route 53   DNS. Traduz domínio em endereço de balanceador ou CloudFront.
                  Failover automático se o health check falhar.

Amazon CloudFront CDN. Copia conteúdo para 750+ pontos de presença.
                  Acelera site, termina TLS perto do usuário, absorve tráfego.
                  Integrado ao WAF para filtrar ataque antes da origem.

Amazon API Gateway Porta de entrada de API: autentica, limita taxa, encaminha
                  para Lambda ou backend. HTTP API (simples) ou REST API (completo).
```

*"VPC é a rede privada da sua conta. Você desenha as sub-redes: a sub-rede pública recebe o balanceador, que é alcançável da internet. A aplicação e o banco ficam em sub-redes privadas, sem rota direta para a internet."*

*"Route 53 é o DNS: transforma api.meusite.com no endereço do balanceador ou do CloudFront. CloudFront é a CDN: coloca uma cópia do conteúdo perto de quem acessa, para o usuário de São Paulo não precisar buscar o arquivo num data center em Virginia. API Gateway é a porta de entrada das APIs: recebe a requisição HTTPS, autentica, aplica limite de taxa e encaminha para Lambda ou para um serviço."*

---

### SLIDE 7 — Segurança, Operação e Integração [1 min]

**Conteúdo visual**

```
Segurança e operação

AWS IAM           Identidade da infraestrutura. Quem (pessoa ou serviço)
                  pode chamar qual API em qual recurso.
                  Role = credencial temporária assumida pelo Lambda ou ECS.
                  ⚠ Erro clássico: Action:* em Resource:* ou chave no código.

Amazon CloudWatch Métricas, logs e alarmes. "A aplicação está saudável?"
AWS CloudTrail    Auditoria de API. "Quem mudou essa configuração?"
AWS CloudFormation Infraestrutura como código (YAML/JSON). Ambiente reproduzível.

─────────────────────────────────────────────────────────────────────
Integração (papéis em microsserviços: Bloco 6)

Amazon SQS        Fila. Produtor envia; consumidor processa quando puder.
Amazon SNS        Pub/sub. Um tópico, vários assinantes.
```

*"IAM é a identidade da infraestrutura da conta — não é o login do usuário do seu aplicativo, que fica no Cognito ou em outro provedor. O mecanismo preferido é a role: o Lambda ou a tarefa do ECS assume uma role temporariamente e ganha só as permissões que precisa. Sem IAM não há arquitetura séria na AWS."*

*"CloudWatch coleta métricas e logs e dispara alarmes. CloudTrail registra quem chamou qual API da AWS, útil para auditoria. CloudFormation descreve a infraestrutura em arquivo e a recria de forma repetível — o oposto de clicar no console e não lembrar o que foi feito."*

*"SQS e SNS são os serviços de integração centrais. SQS é fila: um serviço produz trabalho, o outro processa quando puder. SNS é pub/sub: um fato chega a vários destinos. Vou detalhar os dois no bloco de microsserviços porque é lá que o papel fica mais claro."*

---

### SLIDE 8 — Catálogo completo: outros serviços [30 s]

**Conteúdo visual**


| Categoria           | Serviços                                                 | Para que serve                                               |
| ------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| Armazenamento extra | EFS, FSx, Storage Gateway, AWS Backup                    | Disco NFS compartilhado, file server Windows, backup central |
| Banco extra         | Aurora, ElastiCache, Neptune, Redshift, DocumentDB       | Cache, grafo, data warehouse, documentos                     |
| Análise             | Athena, Glue, Kinesis, EMR, QuickSight                   | Data lake, ETL, streaming, BI                                |
| Segurança extra     | Cognito, KMS, Secrets Manager, WAF, GuardDuty, Inspector | Login de usuário, chaves, varredura de ameaça                |
| Migração            | DMS, MGN, DataSync, família Snow                         | Mover banco, servidor ou dado em grande volume               |
| Mídia               | Elemental MediaLive/Convert, IVS                         | Vídeo ao vivo e sob demanda                                  |
| IoT                 | IoT Core, Greengrass                                     | Broker MQTT, edge                                            |


*"O catálogo tem mais de 200 serviços. Esta tabela registra os mais relevantes além do núcleo. Não vou abrir cada um aqui — o importante é saber que existe e para que serve a categoria. Os serviços de IA têm slide próprio agora."*

---

## BLOCO 3 — SERVIÇOS DE IA [~ 3 min]

---

### SLIDE 9 — Três caminhos para IA na AWS [1 min]

**Conteúdo visual**

```
IA na AWS: três caminhos

┌───────────────┬──────────────────────────────────────┬──────────────────────────────┐
│ Caminho       │ Ideia                                │ Quando usar                  │
├───────────────┼──────────────────────────────────────┼──────────────────────────────┤
│ API pronta    │ Modelo treinado. Manda dado, recebe  │ OCR, transcrição, tradução,  │
│               │ estrutura.                           │ sentimento, chatbot.         │
├───────────────┼──────────────────────────────────────┼──────────────────────────────┤
│ IA generativa │ Modelo fundacional por API.           │ Chat, resumo, assistente,   │
│ (Bedrock)     │ Você monta prompt e ferramentas.     │ resposta sobre documentos.   │
├───────────────┼──────────────────────────────────────┼──────────────────────────────┤
│ Modelo próprio│ Você treina, ajusta e publica.       │ Dado exclusivo, fine-tuning  │
│ (SageMaker)   │                                      │ com controle total do ciclo. │
└───────────────┴──────────────────────────────────────┴──────────────────────────────┘
```

*"A AWS não tem 'um serviço de IA'. Tem três caminhos. No primeiro, você chama uma API pronta: manda a imagem, o áudio ou o texto e recebe uma estrutura de dados de volta — não há treino. No segundo, você acessa um modelo fundacional pelo Bedrock: é como a OpenAI, mas com vários modelos de fornecedores diferentes e integrado com os outros serviços da AWS. No terceiro, você controla o ciclo inteiro com o SageMaker: seus dados, seu algoritmo, seu pipeline de treino."*

---

### SLIDE 10 — APIs prontas e IA generativa [2 min]

**Conteúdo visual**

**APIs prontas — manda dado, recebe estrutura**


| Serviço         | O que faz                                                 | Exemplo                         |
| --------------- | --------------------------------------------------------- | ------------------------------- |
| **Rekognition** | Visão: objetos, rostos, texto, moderação de imagem        | Bloquear upload impróprio       |
| **Textract**    | OCR avançado: extrai campos de formulário e tabela em PDF | Ler campos de boleto            |
| **Transcribe**  | Fala → texto (versão médica disponível)                   | Legenda automática de aula      |
| **Polly**       | Texto → fala com vozes neurais                            | Resposta falada de URA          |
| **Translate**   | Tradução de texto                                         | Internacionalizar interface     |
| **Comprehend**  | Sentimento, entidades, idioma, classificação              | Marcar reclamação em comentário |
| **Lex**         | Bot com intenções e slots (tecnologia do Alexa)           | Chatbot de atendimento          |


**IA generativa**

```
Amazon Bedrock          API única para modelos fundacionais
                        (Claude · Llama · Titan · Mistral · Cohere · e outros)
  ├─ Knowledge Bases   RAG: documentos no S3 → embeddings → responde com base neles
  ├─ Agents           Modelo chama APIs da aplicação para cumprir tarefa
  └─ Guardrails       Filtro de conteúdo e mascaramento de PII

Amazon Q Developer       Assistente de código no editor (sugere, explica, transforma)
Amazon Q Business        Assistente corporativo sobre documentos internos

Amazon SageMaker         Plataforma de ML próprio: treino, pipeline, endpoint, monitoramento
                         Hardware: GPU (P/G) · Trainium (treino) · Inferentia (inferência)
```

*"Nas APIs prontas, destaco Transcribe, Lex e Polly porque juntos formam um atendimento por voz: fala vira texto no Transcribe, o Lex entende a intenção, e o Polly fala a resposta. Rekognition e Textract são os mais usados em formulários e moderação de conteúdo."*

*"O Bedrock é o caminho generativo. Por trás da API podem estar Claude, Llama, Titan ou outros, conforme a região. Para que um chatbot responda sobre os documentos da empresa, você usa as Knowledge Bases: os PDFs ficam no S3, o serviço quebra, gera embeddings e, na hora da pergunta, recupera os trechos certos. Guardrails adiciona filtro de conteúdo e mascara dado pessoal."*

*"Amazon Q Developer é o assistente de código — funciona no editor. Q Business é o assistente corporativo, conectado ao SharePoint, wikis e S3 da empresa, respeitando o que cada funcionário pode ver."*

*"SageMaker é para quem tem dado próprio e quer controlar o ciclo de treino. Para 'ler este PDF' ou 'traduzir esta frase', Textract e Translate são o caminho curto. SageMaker é o caminho longo — e o certo quando o modelo é exclusivo da organização."*

---

## BLOCO 4 — COBRANÇA [~ 3 min]

---

### SLIDE 11 — Como a fatura é formada [1 min 30 s]

**Conteúdo visual**

```
Como funciona a cobrança?

Regra: pague pelo uso, por serviço, por região. Sem mensalidade única.

Três vetores de custo (whitepaper oficial da AWS):
  ① Computação    hora ou segundo da instância / GB-segundo da função
  ② Armazenamento GB-mês do disco ou do objeto
  ③ Saída de dados GB que sai da AWS para a internet
  (④ Request)      milhão de chamadas — importante em Lambda, S3, API Gateway

Modelos de compra:
┌───────────────┬─────────────────────────────┬──────────────────────────────┐
│ Modelo        │ Compromisso                 │ Serve para                   │
├───────────────┼─────────────────────────────┼──────────────────────────────┤
│ On-Demand     │ Nenhum — preço cheio        │ Experimento, pico, novo      │
│ Savings Plans │ US$/hora por 1 ou 3 anos   │ Computação estável (EC2,     │
│               │                             │ Lambda, Fargate)             │
│ Reservada     │ Capacidade por 1 ou 3 anos │ RDS, Redshift, ElastiCache   │
│ Spot          │ Nenhum — pode ser retomada │ Batch, CI, render            │
└───────────────┴─────────────────────────────┴──────────────────────────────┘

⚠ Compromisso não usado = prejuízo. A hora contratada é paga mesmo ociosa.
```

*"Não existe uma assinatura mensal da AWS. A conta do mês é a soma de tudo que foi usado, em cada serviço, em cada região. Parar um recurso encerra a cobrança daquele recurso. Não há multa de cancelamento."*

*"Os três vetores são computação — por hora ou segundo —, armazenamento por gigabyte-mês, e saída de dados para a internet. Request entra como quarto vetor em Lambda, S3, API Gateway e DynamoDB."*

*"Há quatro modelos de compra. On-Demand é o preço cheio, sem contrato. Savings Plans troca um compromisso de consumo mínimo por desconto — o plano Compute cobre EC2, Lambda e Fargate em qualquer combinação. Spot usa capacidade ociosa com desconto grande, mas a AWS pode retomar com aviso curto — útil para processamento em lote. Instâncias reservadas são para bancos e clusters estáveis."*

---

### SLIDE 12 — Free Tier, preços de referência e o que gera susto [1 min 30 s]

**Conteúdo visual**

```
Free Tier (contas criadas a partir de jul/2025)

  US$ 100 ao criar a conta + até US$ 100 completando atividades = até US$ 200 em créditos
  Plano gratuito: sem cobrança por 6 meses ou até o crédito acabar
  Always free (todo mês, para sempre):
    Lambda   → 1 milhão de requests + 400.000 GB-segundos/mês
    DynamoDB → 25 GB + 25 RCU/WCU/mês

Preços de referência — us-east-1 (Virginia), Linux, sem imposto:
  EC2 t3.micro   US$ 0,0104/hora  (≈ US$ 7,60/mês ligado 24h)
  EC2 t3.micro   US$ 0,0168/hora  (≈ US$ 12/mês em sa-east-1 — São Paulo)
  S3 Standard    US$ 0,023/GB-mês
  Saída internet US$ 0 nos primeiros 100 GB/mês → US$ 0,09/GB até 10 TB

  ▶ Use calculator.aws antes de cravar um número

O que gera susto na fatura:
  • Instância EC2 ligada sem uso
  • IPv4 público: US$ 0,005/hora mesmo parado (≈ US$ 3,50/mês)
  • NAT Gateway: cobra hora + GB processado
  • Dado saindo da AWS para a internet em grande volume
  • São Paulo custa mais que Virginia — latência e residência do dado justificam
```

*"O Free Tier mudou em julho de 2025. Para contas novas, são até 200 dólares em crédito para explorar. O always free não tem prazo: Lambda fica com 1 milhão de chamadas por mês de graça para sempre."*

*"Para ter ordem de grandeza: um t3.micro ligado 24 horas por dia o mês inteiro custa cerca de 7 dólares e 60 centavos em Virginia. Em São Paulo, perto de 12 dólares. O S3 Standard custa 2,3 centavos por gigabyte-mês — 100 GB saem por 2 dólares e 30 centavos. A transferência para a internet nos primeiros 100 GB é de graça; depois são 9 centavos por gigabyte."*

*"O que gera susto: instância que ninguém desligou, IPv4 público esquecido, NAT Gateway com tráfego, e dado saindo da AWS em volume. Antes de subir qualquer arquitetura nova, a calculadora da AWS dá uma estimativa. O Cost Explorer e os Budgets mostram o que já está sendo gasto."*

---

## BLOCO 5 — EXEMPLOS DE APLICAÇÕES [~ 3 min]

---

### SLIDE 13 — Dois desenhos: do simples ao completo [1 min 30 s]

**Conteúdo visual**

**Exemplo A — Site estático (congresso, portfólio, eventos)**

```
Usuário → Route 53 → CloudFront → S3  (HTML, PDF, imagem)
                      ↓
              API Gateway + Lambda + DynamoDB  (formulário de inscrição)
```

*Sem servidor web ligado. Cobrança por armazenamento e request. Baixíssimo custo.*

---

**Exemplo B — Sistema web com usuários (matrícula, ERP, portal)**

```
Usuário → Route 53 → CloudFront → ALB → EC2 / Beanstalk (Auto Scaling)
                                          │
                                         VPC (sub-rede privada)
                                          │
                              RDS Multi-AZ · S3 · Secrets Manager
                              CloudWatch · CloudTrail · IAM
```

*"EC2 com balanceador e escala. Banco relacional em sub-rede privada. Segredo no Secrets Manager, auditoria no CloudTrail."*

---

*"O primeiro exemplo mostra que não é preciso nenhuma máquina virtual para ter um site no ar. O conteúdo fica no S3, o CloudFront entrega perto do usuário, e o Lambda cuida de formulários. A cobrança é de centavos para tráfego pequeno."*

*"O segundo exemplo é o sistema web clássico. O Load Balancer distribui o tráfego entre instâncias EC2, que ficam em sub-rede privada. O banco relacional fica em outra sub-rede privada. O CloudWatch monitora, o CloudTrail audita e o Secrets Manager guarda a senha do banco para não aparecer no código."*

---

### SLIDE 14 — Casos reais [1 min 30 s]

**Conteúdo visual**

```
Quem usa a AWS — casos com fonte

  Mercado Livre  → ~24 mil microsserviços em Amazon EKS (plataforma Fury)
                   900 milhões de requests/min entre serviços
                   DynamoDB · S3 · EBS · Neptune · EC2 Spot
                   Fonte: AWS case study, summit e innovators

  iFood          → Migrou middleware financeiro de monolito para evento
                   Amazon EventBridge · SQS · DynamoDB · Kinesis · MSK · EKS
                   Falha num serviço não propaga para o pedido do cliente
                   Fonte: AWS Industries Blog

  Nubank         → +110 milhões de clientes · 4.000+ microsserviços (Kubernetes)
                   72 bilhões de eventos/dia em Kafka · CloudFormation em escala
                   Fonte: building.nu.com + AWS blog

  Netflix        → Plano de controle (catálogo, recomendação, pagamento) na AWS
                   Modo ativo-ativo em 4 regiões AWS
                   ⚠ O vídeo em si usa CDN própria (Open Connect), não só CloudFront
                   Fonte: ACM Queue (Titus) + re:Invent 2025
```

*"Três exemplos brasileiros: Mercado Livre, iFood e Nubank. O Mercado Livre tem vinte e quatro mil microsserviços rodando no EKS, mais de novecentos milhões de requests por minuto entre os serviços — tudo documentado na própria página de case da AWS."*

*"O iFood trocou um middleware financeiro monolítico por uma arquitetura de eventos com EventBridge e SQS. O resultado: a queda de um serviço deixou de se propagar pela cadeia de chamadas síncronas."*

*"O Nubank tem mais de 110 milhões de clientes, mais de 4 mil microsserviços em Kubernetes e 72 bilhões de eventos por dia em Kafka — tudo descrito no blog de engenharia deles."*

*"Netflix entra como exemplo global. A AWS hospeda o plano de controle — catálogo, recomendação, pagamento. O vídeo em si usa uma CDN própria da Netflix, então não é correto dizer que cada byte do filme sai de um bucket S3."*

---

## BLOCO 6 — MICROSSERVIÇOS [~ 3 min 30 s]

---

### SLIDE 15 — O que é microsserviço e o que a plataforma precisa [1 min]

**Conteúdo visual**

```
Microsserviços: o que muda em relação ao monólito

Monólito: catálogo + pedido + pagamento + notificação
          → mesmo processo, mesmo deploy, mesmo banco

Microsserviço: cada capacidade de negócio é um serviço separado
  ✔ Deploy independente      Um time publica sem esperar o outro
  ✔ Escala independente      Só o serviço quente cresce
  ✔ Dado próprio             Schema de um não trava o deploy do vizinho
  ✔ Comunicação pela rede    HTTP ou mensagem
  ✘ Custo operacional maior  Descoberta, falha parcial, rastreio entre serviços

A AWS não "transforma o monólito em microsserviço".
Ela oferece as peças para quem já decidiu partir o sistema.
```

*"Microsserviço significa que cada capacidade de negócio — pedido, estoque, notificação — é um processo separado, com deploy, escala e banco próprios. O time de pagamento publica sem esperar o de catálogo. Um pico de notificação não exige escalar o estoque. Mas o preço é operacional: aparecem descoberta de endereço, falha parcial e rastreio de uma requisição que atravessa cinco serviços."*

---

### SLIDE 16 — Tecnologias que suportam microsserviços [2 min 30 s]

**Conteúdo visual**


| Necessidade                | Tecnologia AWS                      | Para que serve                                | Como suporta o estilo                                                       |
| -------------------------- | ----------------------------------- | --------------------------------------------- | --------------------------------------------------------------------------- |
| **Computação por serviço** | **Lambda**                          | Executar código em resposta a evento          | Deploy e escala por função. Cresce até zero sem custo                       |
|                            | **ECS + Fargate**                   | Orquestrar contêineres sem gerenciar servidor | Um service, uma imagem, Auto Scaling independente                           |
|                            | **EKS**                             | Kubernetes gerenciado                         | Deployment/Service/HPA por serviço; pods isolados por namespace             |
| **Porta de entrada única** | **API Gateway**                     | Receber HTTP, autenticar, encaminhar          | `/pedidos` → serviço A; `/estoque` → serviço B; cliente não sabe quantos há |
|                            | **ALB**                             | Repartir HTTP por caminho dentro da VPC       | Regras de path para cada target group                                       |
| **Comunicação assíncrona** | **SQS**                             | Fila durável por serviço                      | Serviço lento não trava o produtor; DLQ isola mensagem problemática         |
|                            | **EventBridge**                     | Barramento de eventos filtrado por conteúdo   | Produtor não conhece consumidores; novo serviço assina sem novo deploy      |
|                            | **SNS**                             | Fan-out: um fato, vários assinantes           | SNS → várias filas SQS, uma por assinante                                   |
| **Orquestração de fluxo**  | **Step Functions**                  | Máquina de estado com retry e compensação     | Cobrar + emitir nota + estornar se falhar — um processo com dono            |
| **Dado isolado**           | **DynamoDB**                        | Tabela por serviço, escala automática         | Schema independente; Streams notificam vizinhos sem acesso direto           |
|                            | **RDS / Aurora**                    | Banco por serviço                             | Compartilhar tabela acopla deploy de volta                                  |
| **Observabilidade**        | **CloudWatch**                      | Métrica e log por serviço                     | Alarme de pagamento separado do alarme de catálogo                          |
|                            | **X-Ray**                           | Rastreio distribuído                          | Mostra em qual serviço o tempo foi gasto                                    |
| **Descoberta / rede**      | **Cloud Map**                       | Registro do endereço atual de uma task        | IP de contêiner muda; Cloud Map devolve o atual                             |
|                            | **VPC Lattice**                     | Conectar serviços entre VPCs e contas         | Time A chama time B sem abrir a rede inteira                                |
| **Identidade**             | **IAM role por serviço**            | Permissão mínima por função ou task           | Lambda de pedido só acessa a própria fila e tabela                          |
|                            | **Secrets Manager**                 | Segredo lido em execução                      | Senha do banco não aparece no código                                        |
| **Deploy independente**    | **ECR + CodePipeline + CodeDeploy** | Registro de imagem + CI/CD                    | Pipeline por serviço; publica só o service afetado                          |


---

**Um fluxo, para amarrar:**

```
Celular → API Gateway (Cognito valida token)
            → Lambda [serviço pedido] → DynamoDB + EventBridge
                                         → SQS [cozinha]   → Lambda/ECS escala com a fila
                                         → SQS [notificação] → Lambda envia push
               Step Functions entra se houver pagamento com estorno
               X-Ray mostra onde o tempo foi gasto
               IAM de cada função: só sua tabela e sua fila
```

*"Vou passar pela tabela mostrando o papel de cada tecnologia, não só o nome."*

*"Para computação, Lambda é o mais enxuto: cada função é um serviço com deploy e escala próprios, sem servidor. ECS com Fargate é para contêiner que precisa ficar residente. EKS entra quando o time já padronizou Kubernetes — é o que o Mercado Livre usa na plataforma Fury."*

*"Para a porta de entrada, API Gateway expõe um único hostname. Por baixo,* `/pedidos` *vai para um serviço,* `/cardapio` *para outro. Trocar o runtime de Lambda para ECS não muda o cliente se o contrato de API permanecer."*

*"A comunicação assíncrona é onde microsserviço ganha ou perde. SQS é a fila: o serviço de pedido envia uma mensagem, o serviço de nota consome quando puder. Se a nota estiver fora do ar, a mensagem fica na fila — o pedido do cliente não falha. EventBridge é o barramento: o produtor publica um evento, regras filtram e entregam para quem assinou. Um serviço novo começa a reagir a 'pedido criado' sem novo deploy no serviço de pedido. Foi isso que o iFood adotou no middleware financeiro."*

*"Step Functions entra nos fluxos com dono: cobrar, emitir nota, e estornar se a nota falhar. EventBridge anuncia fatos; Step Functions conduz um processo do começo ao fim."*

*"Observabilidade é obrigatória: CloudWatch coleta métrica e log por serviço, X-Ray mostra a cascata da requisição entre os serviços. Sem isso, microsserviço em produção vira caixa-preta."*

---

## SLIDE 17 — Conclusão [30 s]

**Conteúdo visual**

```
Resumo em cinco frases

① A AWS aluga infraestrutura e plataforma, você paga pelo que usa.
② Os serviços centrais são: EC2/Lambda · S3 · RDS/DynamoDB · VPC · IAM · CloudWatch.
③ Para IA: API pronta (Rekognition, Textract, Transcribe...) · Bedrock (generativa) · SageMaker (modelo próprio).
④ Cobrança: computação + armazenamento + saída de dados. On-Demand, Savings Plans ou Spot.
⑤ Microsserviços: ECS/Lambda por serviço · API Gateway na borda · SQS/EventBridge para comunicação.

Mercado Livre · iFood · Nubank · Netflix operam em produção nessa plataforma.
```

*"Para fechar: a AWS é um catálogo de serviços cobrado pelo uso. O núcleo é EC2 ou Lambda para computação, S3 para arquivo, RDS ou DynamoDB para dado, VPC para rede, IAM para permissão e CloudWatch para observabilidade. IA vem em três camadas: API pronta, Bedrock para generativa e SageMaker para modelo próprio. A cobrança é por computação, armazenamento e transferência de dados. E microsserviços são sustentados por Lambda e ECS para computar, API Gateway na borda, SQS e EventBridge para comunicação assíncrona. Obrigado."*

---

## APÊNDICE — Dados de referência para perguntas da plateia

### Preços (us-east-1, sem imposto, válidos como ordem de grandeza — confirmar em calculator.aws)


| Serviço                            | Unidade                  | Preço                      |
| ---------------------------------- | ------------------------ | -------------------------- |
| EC2 t3.micro On-Demand             | por hora                 | US$ 0,0104                 |
| EC2 t3.micro On-Demand (sa-east-1) | por hora                 | US$ 0,0168                 |
| EC2 IPv4 público                   | por hora por IP          | US$ 0,005                  |
| S3 Standard                        | GB-mês                   | US$ 0,023                  |
| S3 Glacier Deep Archive            | GB-mês                   | US$ 0,00099                |
| Saída internet (após 100 GB free)  | GB                       | US$ 0,09 (primeiros 10 TB) |
| Lambda request                     | por 1 milhão (após cota) | US$ 0,20                   |
| Lambda duração (128 MB, 1 ms)      | GB-segundo               | ~US$ 0,0000000167          |


### Always Free (contas de qualquer geração, sem prazo)

- Lambda: 1 milhão de requests + 400.000 GB-segundos por mês
- DynamoDB: 25 GB de armazenamento + 25 RCU + 25 WCU por mês
- CloudWatch: 10 métricas, 10 alarmes, 5 GB de logs por mês (limites variam)
- SNS: 1 milhão de publicações por mês

### Free Tier para contas criadas a partir de jul/2025

- US$ 100 ao criar + até US$ 100 em atividades = teto de US$ 200 em créditos
- Plano gratuito: sem cobrança por 6 meses ou até o crédito acabar
- Plano pago: paga o que exceder crédito e cota always free

### Infraestrutura global (out/2026)

- 39 regiões, 124 zonas de disponibilidade
- 750+ pontos de presença do CloudFront
- 46 Local Zones e Wavelength Zones
- ~20 milhões de km de fibra na rede global
- São Paulo: `sa-east-1`

### Participação de mercado (Synergy Research Group, Q2 2026)

- AWS: ~28% do gasto global em infraestrutura de nuvem
- Azure: ~20%
- Google Cloud: ~15%

### Casos reais — fontes


| Empresa       | Números chave                                                    | Fonte                                                                 |
| ------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------- |
| Mercado Livre | 24 mil microsserviços, 900 M req/min, EKS (Fury), 18 países      | aws.amazon.com/solutions/case-studies/mercado-livre-summit            |
| iFood         | EventBridge, SQS, EKS, MSK, DynamoDB                             | aws.amazon.com/blogs/industries/ifood-modernizes-financial-middleware |
| Nubank        | 110 M clientes, 4.000+ microsserviços, 72 B eventos/dia em Kafka | building.nu.com/managing-cloud-limits                                 |
| Netflix       | Ativo-ativo em 4 regiões; vídeo em CDN própria (Open Connect)    | ACM Queue Titus; AWS re:Invent 2025 IND387                            |
| NASA Artemis  | MediaLive, MediaConnect, CloudFront, GovCloud                    | aboutamazon.com/news/aws/nasa-artemis-video                           |



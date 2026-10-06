# 5. Exemplos de aplicações que podem estar na AWS

Documento de pesquisa para a pergunta “dê exemplos de aplicações que podem estar hospedados/servidos”. Não é roteiro de slides.

`servicos-aws.md` mostra desenhos genéricos na seção 25 (três camadas, API serverless, dados). Faltava nomear aplicações: o que o usuário faz e qual serviço segura cada pedaço. A primeira parte são aplicações que **podem** rodar na AWS, desenhadas com os serviços da pergunta 2. A segunda parte são casos reais, com fonte e com o limite do que a fonte realmente afirma. Arquitetura de empresa muda. Um case de dez anos prova que aquele conjunto de serviços hospedava o produto naquela época.

## O que “hospedado na AWS” inclui

Quase qualquer software que hoje rode num servidor cabe, de alguma forma:

- site e sistema web;
- backend de aplicativo móvel;
- API entre sistemas;
- processamento em lote e pipeline de dados;
- fila de trabalho (envio de e-mail, geração de PDF, cobrança);
- modelo de IA atrás de uma API;
- vídeo sob demanda ou ao vivo;
- dispositivo de IoT falando com um broker.

O que muda é o serviço, não a possibilidade. Um monolito em Java pode ir para um EC2 ou para o Elastic Beanstalk. O mesmo sistema, partido, pode ir para Lambda ou para contêiner. Os exemplos abaixo usam o caminho mais legível, não o único.

## Exemplo 1. Site de um evento

Um congresso publica programação, palestrantes e PDFs. O conteúdo muda pouco e é lido por muita gente de uma vez.

| Peça | Serviço | Função |
| --- | --- | --- |
| HTML, CSS, PDF | S3 | Guarda os arquivos |
| HTTPS e cache perto de quem lê | CloudFront | Entrega o site sem um servidor web ligado |
| Nome do evento | Route 53 | Aponta o domínio para o CloudFront |
| Formulário de inscrição, se houver | API Gateway + Lambda + DynamoDB | Grava a inscrição sem manter EC2 |

Não há banco relacional nem máquina virtual. A cobrança acompanha armazenamento e request. É o desenho mais barato da lista para um trabalho de disciplina.

## Exemplo 2. Sistema web com usuários e relatório

Um sistema de matrícula: login, oferta de turma, inscrição, histórico. Transação, regra de integridade, SQL.

| Peça | Serviço | Função |
| --- | --- | --- |
| Domínio | Route 53 | Nome do sistema |
| Tela | S3 + CloudFront, ou o próprio backend servindo a página | Front estático ou aplicação clássica |
| Aplicação | EC2 atrás de um Application Load Balancer, com Auto Scaling; ou Elastic Beanstalk, que monta isso | Onde o código do sistema roda |
| Rede | VPC | Balanceador na sub-rede pública, aplicação e banco nas privadas |
| Banco | RDS (PostgreSQL ou MySQL), Multi-AZ se a queda de uma zona for inaceitável | Turma, inscrição, aluno |
| Documento (histórico em PDF, atestado) | S3 | Arquivo, com link pré-assinado para não deixar o bucket público |
| Login do aluno | Cognito, ou o login dentro da aplicação | Identidade do usuário final. IAM continua sendo a identidade da infraestrutura |
| Senha do banco | Secrets Manager | A aplicação busca o segredo em execução |
| Métrica e auditoria | CloudWatch e CloudTrail | Saúde do sistema e trilha de quem alterou a infraestrutura |

Esse é o “site em três camadas” da seção 25, com nome de aplicação. Cabe num único servidor EC2 no laboratório. Em produção, o balanceador e mais de uma zona existem para a queda de uma máquina não derrubar a matrícula.

## Exemplo 3. Aplicativo móvel

Um aplicativo de cardápio do restaurante universitário. O celular não fala com o banco. Fala com uma API.

| Peça | Serviço | Função |
| --- | --- | --- |
| API | API Gateway | HTTPS, limite de taxa, autorização |
| Regra | Lambda, uma função por ação (ver cardápio, fazer pedido) | Escala com o horário do almoço e fica em zero de madrugada |
| Pedido e cardápio | DynamoDB | Acesso por chave (id do pedido, data do cardápio) |
| Foto do prato | S3 | Objeto |
| Login | Cognito | Cadastro, senha, token JWT que o API Gateway valida |
| Aviso “pedido pronto” | SNS | Empurra notificação ou e-mail |
| Fila da cozinha | SQS | O pedido entra na fila; a tela da cozinha consome no ritmo dela |

Se o pico do almoço for dez vezes a madrugada, Lambda e DynamoDB acompanham sem uma frota ligada o dia inteiro. Se a regra de negócio for um processo longo e com muito estado em memória, o mesmo desenho troca Lambda por um serviço em ECS/Fargate atrás do balanceador. A API que o celular vê pode permanecer.

## Exemplo 4. Loja

Catálogo, carrinho, pagamento, nota, recomendação.

- Catálogo e pedido transacional em **RDS ou Aurora**.
- Carrinho e sessão em **DynamoDB** ou **ElastiCache**, porque são acesso por chave e toleram um modelo diferente do pedido fechado.
- Imagem em **S3**, entregue por **CloudFront**.
- O pico de campanha em **Auto Scaling** no EC2 ou em tarefas **ECS**, ou em **Lambda** nas funções de borda do pedido.
- E-mail de confirmação pelo **Amazon SES**.
- “Quem comprou isto também viu” pelo **Amazon Personalize**, se a loja tiver volume para treinar recomendação. Sem volume, uma consulta SQL basta e a IA é custo sem retorno.
- Relatório do mês em **Redshift** ou, para pergunta solta em cima de arquivo, **Athena** no S3. O caixa do pedido não consulta o warehouse. Isso já está na seção 25 do catálogo: Redshift não atende o checkout.

Mercado Livre, abaixo, é esse tipo de sistema em escala de dezenas de milhares de microsserviços. O desenho desta seção é a versão que se explica em um slide.

## Exemplo 5. Aula gravada e transmissão ao vivo

- Arquivo-mestre no **S3**.
- **AWS Elemental MediaConvert** gera as versões (várias resoluções, HLS ou DASH).
- **CloudFront** entrega ao player.
- Ao vivo: **MediaLive** codifica, **MediaPackage** empacota, CloudFront distribui.

Um curso que só sobe MP4 e deixa o navegador baixar o arquivo pode parar em S3 + CloudFront, sem a família Elemental. Elemental entra quando há transcodificação, várias qualidades e player adaptativo. A seção 14 de `servicos-aws.md` tem a cadeia completa.

## Exemplo 6. Assistente em cima de documentos

Os PDFs de um departamento ficam no **S3**. Uma **Knowledge Base do Amazon Bedrock** indexa. Uma API em **Lambda** manda a pergunta e devolve a resposta com **Guardrails**. O detalhe dos serviços de IA está em `03-servicos-de-ia.md`. A aplicação hospedada é o assistente; o modelo não mora num EC2 da equipe.

## Exemplo 7. Telemetria

Sensores ou o aplicativo publicam eventos. **API Gateway** ou **IoT Core** recebe. **Kinesis** ou uma fila segura o pico. **Lambda** grava agregado no **DynamoDB** e o bruto no **S3**. **Athena** responde “qual sala passou de 30 °C esta semana”. Isso é aplicação de retaguarda, sem tela elaborada, e é um dos usos mais comuns da nuvem.

## Casos reais

### Netflix

Em 2008 a Netflix decidiu sair do data center próprio e levar a infraestrutura para a AWS. O artigo da ACM Queue sobre o Titus (o sistema de contêineres deles) diz que navegação no catálogo, cálculo de recomendação e pagamento são servidos a partir da AWS, em cima de uma malha de microsserviços. Em fala no re:Invent 2025, engenheiros da Netflix descreveram um plano de controle em modo ativo-ativo em quatro regiões AWS.

Limite importante para não exagerar no slide: o vídeo em si, na arquitetura que a Netflix tornou pública ao longo dos anos, é distribuído em grande parte pela CDN própria, a Open Connect, com aparelhos dentro de operadoras. A AWS hospeda o plano de controle, a personalização, a conta, o pagamento e a orquestração. Não é correto dizer que cada byte do filme sai de um bucket S3 via CloudFront.

Fontes: ACM Queue, “Titus: Introducing Containers to the Netflix Cloud”; AWS re:Invent 2025, sessão IND387.

### Mercado Livre

O maior comércio eletrônico da América Latina roda na AWS. O case publicado pela AWS descreve a ida de um monólito para microsserviços e a plataforma interna Fury, em cima de **Amazon EKS**. Os números que a própria página da AWS reproduz: cerca de **24 mil microsserviços**, cerca de **17 mil bancos**, na ordem de **900 milhões de requests por minuto** entre serviços, operação em **18 países**. Serviços citados nesse material: **DynamoDB, S3, EBS, Neptune**, além de EC2 com uso de **Spot** para custo. Há também analytics e machine learning para o Marketplace, o Mercado Pago e o Mercado Crédito.

Fonte: páginas de case da AWS sobre Mercado Livre / Mercado Libre (summit e innovators).

### iFood

O iFood descreveu, num blog da AWS, a troca de um middleware financeiro monolítico por arquitetura orientada a eventos. Os serviços nomeados no texto: **Amazon EventBridge** (barramento e Pipes), **SQS**, **DynamoDB**, **Kinesis**, **Amazon MSK** (Kafka gerenciado) e **EKS**. O ponto do case, para esta pergunta: uma aplicação de produção brasileira, de uso diário, está nesse conjunto. O ponto para a pergunta 6: a fila e o barramento existem para um serviço lento ou fora do ar não derrubar o pedido do cliente.

Fonte: AWS Industries Blog, “iFood modernizes its financial middleware to event-driven architecture”.

### Nubank

Banco digital brasileiro, na AWS. O blog de engenharia da empresa fala em mais de **110 milhões de clientes** (Brasil, Colômbia, México), mais de **4.000 microsserviços** orquestrados com Kubernetes, e dezenas de bilhões de eventos por dia em **Kafka**, em várias contas AWS. Um post da AWS descreve **CloudFormation** como base da infraestrutura imutável deles (milhares de stacks). O banco principal citado pela engenharia do Nubank é o **Datomic**, não o RDS. Usar Nubank como exemplo de “está na AWS” é correto. Usar como exemplo de “o banco deles é o DynamoDB” é incorreto.

Fontes: building.nu.com, “Managing Cloud Limits”; AWS Cloud Operations Blog sobre CloudFormation no Nubank.

### Airbnb

Case antigo e ainda útil como conjunto mínimo. Pouco depois do lançamento, a empresa levou a operação para a AWS. O texto publicado pela AWS cita **EC2** (aplicação, cache, busca), **Elastic Load Balancing**, **RDS** com MySQL em Multi-AZ, **S3** para backup e foto, **EMR** para processar dado e **CloudWatch**. Os números daquele texto (cerca de 200 instâncias, 10 TB de fotos) são de uma Airbnb muito menor que a atual. Servem para mostrar o desenho de três camadas com nome de produto. Não servem para descrever a Airbnb de hoje.

Fonte: AWS case study Airbnb.

### NASA

A AWS aparece em pedaços específicos, não como “a NASA inteira”. Para a transmissão da Artemis, o material público descreve **AWS Elemental MediaLive** e **MediaPackage** (e, na Artemis II, **MediaConnect**) com **CloudFront** levando o vídeo a parceiros e à plataforma NASA+. Cargas de missão com exigência governamental aparecem associadas à **AWS GovCloud**. É um exemplo de aplicação de mídia e de computação regulada, não de sistema web comum.

Fontes: AWS for M&E Blog sobre a Artemis I; About Amazon, sobre o vídeo da Artemis II.

## O que escolher para a fala

Dois exemplos desenhados bastam: o site do evento (S3 e CloudFront) e o sistema de matrícula ou o aplicativo do restaurante (computação, banco, fila). Um caso real fecha: Mercado Livre ou iFood, porque estão perto do público e porque a fonte é explícita sobre os serviços. Netflix entra só se der para dizer a ressalva da CDN. Airbnb entra só datado.

## Fontes

- `servicos-aws.md`, seção 25.
- ACM Queue, Titus / Netflix: https://queue.acm.org/doi/fullHtml/10.1145/3155112.3158370
- AWS re:Invent 2025, Netflix IND387 (plano de controle em quatro regiões).
- Mercado Livre na AWS: https://aws.amazon.com/solutions/case-studies/mercado-livre-summit/ e https://aws.amazon.com/solutions/case-studies/innovators/mercado-libre/
- iFood: https://aws.amazon.com/blogs/industries/ifood-modernizes-its-financial-middleware-to-event-driven-architecture/
- Nubank: https://building.nu.com/managing-cloud-limits/ e https://aws.amazon.com/blogs/mt/leveraging-immutable-infrastructure-nubank/
- Airbnb: https://aws.amazon.com/solutions/case-studies/airbnb-case-study/
- NASA / Artemis: https://aws.amazon.com/blogs/media/immersive-viewing-of-the-nasa-artemis-1-launch-with-futuralis-felix-paul-studios-and-aws/ e https://www.aboutamazon.com/news/aws/how-nasa-streamed-live-4k-video-from-the-moon

# O que faltava pesquisar

Documento de pesquisa. Não é roteiro de slides.

A apresentação precisa responder seis perguntas (`apresentaçao.txt`). O arquivo `servicos-aws.md` é um catálogo: explica o que cada serviço é e para que serve. Esse catálogo cobre bem uma das perguntas e deixa as outras pela metade. A tabela abaixo é o resultado da leitura cruzada. Os outros arquivos desta pasta fecham o que faltava.

## Mapa pergunta a pergunta

| Pergunta | O que `servicos-aws.md` já tem | O que faltava | Onde a resposta foi fechada |
| --- | --- | --- | --- |
| 1. O que é a AWS? | Um parágrafo de abertura e a seção “Como a AWS se organiza” (conta, região, zona de disponibilidade, formas de acesso, responsabilidade compartilhada, quatro modelos de preço em seis linhas) | Definição de computação em nuvem, de onde a AWS veio, o que ela vende de fato (IaaS, PaaS e alguns produtos de aplicação), tamanho da infraestrutura, posição em relação a Azure e Google Cloud, e por que uma aplicação vai para lá | `01-o-que-e-aws.md` |
| 2. Principais serviços e para que servem? | O miolo do arquivo. Há uma lista curta “para a apresentação” e o catálogo por categoria | Não faltava catálogo. Faltava uma resposta selecionável: quais serviços cabem numa fala de 15 a 20 minutos e como apresentá-los em grupos, sem abrir as mais de 20 categorias | `02-servicos-principais.md` (a profundidade continua em `servicos-aws.md`) |
| 3. Serviços de IA e para que servem? | A seção 12, com SageMaker, Bedrock, Amazon Q, APIs prontas e o hardware (GPU, Trainium, Inferentia) | Organizar os serviços em caminhos de uso. O catálogo lista dezenas de nomes; a pergunta pede “quais são e para que servem”, com critério de escolha e um exemplo em que vários serviços se juntam | `03-servicos-de-ia.md` |
| 4. Como funciona a cobrança? | Seis linhas na seção 1 (sob demanda, Savings Plans, reservadas, Spot, Free Tier) e a seção 19, que é de **controle** de gasto (Cost Explorer, Budgets, tags), não da **mecânica** da fatura | Como a conta é montada, os três vetores oficiais de custo, o que entra em cada serviço que a apresentação vai citar, transferência de dados, o Free Tier de 2025, o que continua cobrando com a máquina parada, e um exemplo numérico de ordem de grandeza | `04-como-funciona-a-cobranca.md` |
| 5. Exemplos de aplicações hospedadas | A seção 25 mostra desenhos genéricos (site em três camadas, API serverless, pipeline de dados) | Aplicações nomeadas, de ponta a ponta: o que o usuário faz e qual serviço sustenta cada pedaço. Também faltavam casos reais com fonte, com o cuidado de não tratar um case antigo como arquitetura atual | `05-exemplos-de-aplicacoes.md` |
| 6. O que dá suporte a microsserviços, para que serve e como suporta? | ECS, EKS, Fargate, Lambda, API Gateway, SQS, SNS, EventBridge, Step Functions, Cloud Map e VPC Lattice aparecem espalhados. Vários dizem “serve para microsserviços” numa frase | A definição do estilo arquitetural e, para cada tecnologia, **como** ela sustenta o estilo: deploy independente, escala independente, comunicação, descoberta, dado próprio, observabilidade. Sem isso a pergunta 6 fica uma lista de nomes | `06-suporte-a-microservicos.md` |

## O que não precisou ser pesquisado de novo

O catálogo por categoria já está feito e é a base da pergunta 2: computação, contêineres, armazenamento, banco, rede, segurança, operação, integração, analytics, migração, mídia, IoT e o restante. Repetir EC2, S3, RDS ou IAM neste pacote seria duplicar `servicos-aws.md`. Os arquivos novos citam o serviço e apontam para a seção em que ele já foi explicado.

Também já estavam respondidos, e por isso só entram de passagem:

- modelo de responsabilidade compartilhada (segurança *da* nuvem e segurança *na* nuvem);
- região, zona de disponibilidade e `sa-east-1` (São Paulo);
- a diferença entre fila (SQS) e notificação (SNS);
- o glossário (RPO, RTO, serverless, lift-and-shift).

## Recorte para 15 a 20 minutos

A pesquisa inteira não cabe na fala. O recorte abaixo é critério de edição para quando os slides forem montados. Não é o roteiro.

| Bloco | Tempo sugerido | Fica de fora da fala, fica na pesquisa |
| --- | --- | --- |
| O que é | cerca de 2 min | história detalhada, números de mercado além de uma comparação, Local Zone e Wavelength |
| Serviços principais | cerca de 5 min | tudo que em `servicos-aws.md` está em “se sobrar tempo”, e as categorias de nicho (satélite, quântica, games, blockchain) |
| IA | cerca de 3 min | Lookout, Forecast, Panorama, Monitron, hardware de treino além de uma menção |
| Cobrança | cerca de 3 min | CUR, Billing Conductor, cada centavo de NAT e de IPv4; fica o mecanismo e um exemplo |
| Exemplos | cerca de 3 min | um caso real comentado e dois desenhos (site e API). Os outros casos ficam de reserva |
| Microsserviços | cerca de 3 a 4 min | App Mesh, Amazon MQ, MSK. Ficam computação, porta de entrada, fila/evento e um fluxo |

Soma perto de 20 minutos se cada bloco for curto. Se a turma já viu nuvem, o bloco “o que é” encolhe e o de microsserviços cresce.

## Fontes usadas para fechar as lacunas

Os fatos novos (história, infraestrutura, participação de mercado, Free Tier vigente, preços de lista, casos de clientes) estão citados no fim de cada arquivo. Preço de lista muda por região e por data. Onde há número, o texto diz a região e manda confirmar na calculadora antes de cravar o valor num slide.

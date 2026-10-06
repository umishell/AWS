# 3. Serviços de IA e para que servem

Documento de pesquisa para a pergunta “quais são os serviços de IA e para que servem?”. Não é roteiro de slides.

A lista nome a nome está na seção 12 de `servicos-aws.md`. Aqui a resposta está organizada do jeito que a pergunta pede: três caminhos, o que cada serviço faz, e quando escolher um em vez de outro. Nomes cujo ciclo de vida a própria AWS restringiu ficam no fim, para não entrarem num slide como se fossem aposta atual.

## Os três caminhos

A AWS não tem “um serviço de IA”. Tem três formas de usar modelo, e elas resolvem problemas diferentes.

| Caminho | Ideia | Serviços centrais | Quando faz sentido |
| --- | --- | --- | --- |
| **API pronta** | O modelo já está treinado. A aplicação manda o dado e recebe a estrutura | Rekognition, Textract, Transcribe, Polly, Translate, Comprehend, Lex, Personalize | Extrair texto de um PDF, transcrever áudio, moderar imagem, traduzir, montar um bot de intenções. Não há conjunto de treino próprio relevante |
| **IA generativa por API** | Um modelo fundacional (texto, imagem, embeddings) é chamado por API. A aplicação monta o prompt, os documentos e as ferramentas | Amazon Bedrock, Amazon Q | Chat, resumo, resposta com base em documentos da empresa, assistente de código, agente que chama APIs |
| **Construir o modelo** | Os dados são da organização e o ciclo de treino, avaliação e publicação é dela | Amazon SageMaker, instâncias com GPU ou chip da AWS | Previsão com dado próprio, visão muito específica, fine-tuning com controle do ciclo, treino grande |

Os três convivem no mesmo produto. Um aplicativo de atendimento pode transcrever com Transcribe, classificar a intenção com Lex ou com um modelo no Bedrock, e guardar o histórico num banco comum (DynamoDB ou RDS).

## Caminho 1 — APIs prontas

Cada serviço cobre uma modalidade. A aplicação não treina o modelo. Manda o arquivo ou o texto e recebe JSON.

| Serviço | Para que serve | Exemplo de uso |
| --- | --- | --- |
| **Amazon Rekognition** | Visão computacional: objetos, rostos, texto em imagem, moderação de conteúdo | Bloquear imagem imprópria num upload; ler placa ou rótulo |
| **Amazon Textract** | OCR que entende formulário e tabela, não só o texto corrido | Extrair campos de um comprovante ou de uma folha de matrícula em PDF |
| **Amazon Transcribe** | Fala para texto. Existe variante médica | Legendar uma aula; gerar ata de uma reunião |
| **Amazon Polly** | Texto para fala, com vozes neurais | Resposta falada de uma URA; áudio de um aviso |
| **Amazon Translate** | Tradução de texto | Traduzir a descrição de um produto ou a interface |
| **Amazon Comprehend** | Linguagem: sentimento, entidades, frases-chave, idioma, classificação. Variante médica para entidade clínica | Marcar se um comentário é reclamação; achar nomes e datas num texto |
| **Amazon Lex** | Bot de conversa com intenções e “slots” (os dados que o bot precisa coletar). A mesma família de tecnologia do Alexa | “Quero remarcar para terça” vira intenção `Remarcar` e data preenchida |
| **Amazon Personalize** | Recomendação treinada com as interações dos usuários daquele produto | “Quem viu esta disciplina também viu” |
| **Amazon Kendra** | Busca em documentos da empresa, com conectores e pergunta em linguagem natural | Achar a cláusula certa num conjunto de PDFs |
| **Amazon Fraud Detector** | Modelo de fraude em cadastro e pagamento | Pontuar um cadastro novo |
| **Amazon DevOps Guru** | Olha métrica e log e aponta comportamento anômalo de operação | “A latência deste serviço mudou de padrão depois do deploy” |

Transcribe, Lex e Polly juntos são a base de um atendimento por voz: fala vira texto, o texto vira intenção, a resposta volta falada.

Amazon A2I (Augmented AI) encaixa revisão humana quando a confiança do Textract ou do Rekognition é baixa: a tarefa vai para uma fila de pessoas e volta ao fluxo. Serve para o caso em que errar automático é caro.

## Caminho 2 — generativa

### Amazon Bedrock

API única para modelos fundacionais de vários fornecedores (famílias como Anthropic Claude, Meta Llama, Amazon Titan, Mistral, Cohere, conforme a região e o contrato). A aplicação não opera GPU e não treina o modelo do zero.

Para que serve: gerar e transformar texto, conversar, resumir, produzir embeddings e, em alguns modelos, imagem.

Peças em volta da API, que são o que diferencia “chamar um chat” de “colocar IA no produto”:

- **Knowledge Bases.** RAG (retrieval augmented generation). Os documentos ficam no S3; o serviço fatia, gera embeddings e, na hora da pergunta, recupera trechos para o modelo responder com base neles, não só com o que decorou no treino.
- **Agents.** O modelo decide chamar uma API da aplicação (consultar pedido, abrir chamado) para cumprir a tarefa.
- **Guardrails.** Filtro de conteúdo, bloqueio de assunto e mascaramento de dado pessoal na entrada e na saída.
- **Flows.** Encadeia prompt, base de conhecimento e condição.
- **Avaliação e customização.** Comparar modelos e, quando a API oferece, adaptar com fine-tuning.

O Bedrock não desenha o produto. Prompt, permissão (IAM), ferramenta e teste de qualidade continuam com quem desenvolve.

### Amazon Q

Assistente com produto definido, em cima da mesma geração de modelos:

- **Amazon Q Developer:** no editor de código. Explica, sugere e, em alguns fluxos, ajuda a transformar código (por exemplo upgrade de versão de linguagem).
- **Amazon Q Business:** assistente interno da empresa, ligado a SharePoint, S3, wikis, respeitando o que cada funcionário pode ver.
- **Amazon Q no QuickSight e no Connect:** pergunta em linguagem natural no painel de BI e apoio no contact center.

Q Business e Knowledge Bases do Bedrock disputam o caso “perguntar aos documentos”. Kendra cobre o caso mais antigo de busca empresarial. Para projeto novo de perguntas e respostas sobre um acervo, o caminho que a AWS empurra é Bedrock com Knowledge Bases.

## Caminho 3 — construir e publicar o próprio modelo

### Amazon SageMaker

Plataforma do ciclo clássico de machine learning. A pessoa prepara dado, treina, ajusta hiperparâmetro, registra o modelo, publica um endpoint e acompanha drift.

Peças que importam na explicação, sem abrir o console inteiro:

- **Studio e notebooks:** o ambiente do cientista de dados.
- **Training jobs:** treino em instâncias (inclusive GPU) que desligam ao terminar, para não pagar a máquina a noite inteira.
- **Pipelines:** o fluxo de treino repetível.
- **Model Registry:** qual versão está aprovada para produção.
- **Endpoints:** inferência em tempo real. Também há inferência assíncrona, serverless e em lote (batch transform).
- **Feature Store:** as mesmas variáveis no treino e na inferência, para o modelo não ver um dado diferente do que viu ao aprender.
- **Clarify:** viés e explicabilidade.
- **Ground Truth:** rotulagem humana.
- **Canvas:** previsão sem código, para analista.

Serve quando o modelo é da organização: o dado é dela, o algoritmo é dela, ou o fine-tuning precisa de controle que uma API pronta não dá. Para “ler este PDF” ou “traduzir esta frase”, SageMaker é o caminho longo. Textract e Translate são o caminho curto.

### Hardware por baixo

Quando o treino ou a inferência não cabem num endpoint gerenciado, a computação é EC2:

- famílias **P** e **G:** GPU NVIDIA;
- **Trainium (Trn):** chip da AWS para treino;
- **Inferentia (Inf):** chip da AWS para inferência;
- **AWS Neuron:** o SDK que compila o modelo para esses chips;
- **SageMaker HyperPod:** cluster de treino longo, que substitui nó que falha;
- AMIs e contêineres de deep learning, com driver e framework já instalados.

Isso é infraestrutura de IA, não um serviço que a aplicação chama. Numa fala curta, uma frase basta: quem treina modelo grande aluga GPU ou chip da AWS; quem só consome um modelo pronto não vê esse hardware.

## Um exemplo em que os serviços se juntam

Perguntas e respostas sobre os PDFs de uma disciplina:

1. Os PDFs entram no **S3**.
2. Uma **Knowledge Base do Bedrock** indexa os arquivos.
3. A aplicação (por exemplo **Lambda** atrás de **API Gateway**, ou um serviço em **ECS**) manda a pergunta do aluno ao **Bedrock**.
4. Um **Guardrail** barra pedido fora do assunto e mascara dado pessoal se aparecer.
5. **IAM** permite que só essa função chame o modelo e leia aquele bucket.
6. **CloudWatch** mostra latência, erro e volume. A fatura de IA cresce com token e com armazenamento, não com “uma licença de IA”.

Outro exemplo, de voz: áudio no **Transcribe**, intenção no **Lex** (ou texto no Bedrock), resposta no **Polly**. O estado da conversa fica no **DynamoDB**.

## O que não colocar como recomendação atual

`servicos-aws.md` já avisa, e vale repetir para a apresentação não ensinar serviço em retração:

- **Amazon Forecast** teve o roadmap redirecionado. Confirmar antes de citar como escolha nova. Previsão de série pode ir para outros caminhos (SageMaker, ou modelos no Bedrock, conforme o caso).
- **Família Lookout** (Vision, Equipment, Metrics): vários encerraram ou restringiram o ciclo. Tratar como legado.
- **Amazon CodeGuru** em parte cedeu lugar ao Amazon Q Developer. O profiler ainda existe.

## Como escolher, em uma regra

- O problema é uma modalidade fechada (imagem, OCR, fala, tradução, sentimento) e o modelo genérico resolve: API pronta.
- O problema é gerar, resumir, conversar ou responder com base num acervo: Bedrock, com Guardrails e, se houver documentos, Knowledge Bases.
- O problema é um modelo cujo dado e cujo ciclo a organização precisa controlar: SageMaker, e hardware só se o volume de treino exigir.

## Fontes

- Catálogo interno: `servicos-aws.md`, seção 12.
- Visão geral de produtos AWS: https://aws.amazon.com/products/
- Amazon Bedrock: https://aws.amazon.com/bedrock/
- Amazon SageMaker: https://aws.amazon.com/sagemaker/
- Whitepaper de visão geral da AWS: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html

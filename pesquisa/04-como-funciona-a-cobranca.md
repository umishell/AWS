# 4. Como funciona a cobrança

Documento de pesquisa para a pergunta “como funciona a cobrança?”. Não é roteiro de slides.

`servicos-aws.md` registra os modelos de compra do EC2 em poucas linhas e, na seção 19, as ferramentas para **olhar** o gasto. Não explica como a fatura é formada. É esta a lacuna.

Os valores abaixo são ordem de grandeza de lista pública, em dólar, quase sempre para a região **us-east-1 (Norte da Virgínia)**, Linux, sem imposto. São Paulo (`sa-east-1`) costuma ser mais cara. A página de preço de cada serviço e a [AWS Pricing Calculator](https://calculator.aws/) são a fonte para cravar um número. Não usar estes centavos num slide como se fossem cotação fechada.

## A regra

Não existe uma mensalidade única da “plataforma AWS”. Cada serviço tem uma página de preço. A conta do mês é a soma do que foi usado, em cada região, mais impostos quando se aplicam. Parar de usar um recurso encerra a cobrança daquele recurso. Não há multa de cancelamento da conta. O que continua cobrando é o recurso que ficou ligado ou armazenado: instância que ninguém desligou, disco órfão, IP público parado, bucket cheio, NAT Gateway.

O whitepaper oficial *How AWS Pricing Works* resume o custo em três vetores: **computação, armazenamento e transferência de dados para fora**. Quase todo serviço da apresentação cabe num desses três, com um quarto que o whitepaper trata dentro deles: **pedido** (request), importante em S3, DynamoDB, API Gateway e Lambda.

A lógica econômica que a AWS repete: trocar custo fixo de data center por custo variável. Na prática o variável só é menor se alguém desliga o que não usa e escolhe o modelo de compra certo. Elasticidade sem governança aumenta a fatura.

## Como a fatura é montada

1. Tudo nasce numa **conta**. A conta é a fronteira de cobrança.
2. Cada recurso é criado numa **região**. O mesmo tipo de instância tem preço diferente em Virginia e em São Paulo.
3. Cada serviço emite **usage types**: hora de instância, GB-mês de disco, GB de saída para a internet, milhão de requests, GB-segundo de Lambda.
4. No fim do mês a AWS consolida isso numa fatura em dólar. Impostos (no Brasil, conforme o enquadramento da conta) entram à parte. O whitepaper lembra que os preços de lista em geral não incluem tributo.
5. **Tags** (`projeto`, `ambiente`, `disciplina`) não mudam o preço. Elas permitem filtrar a fatura. A tag só aparece no billing depois de ativada como tag de alocação de custo.
6. **AWS Organizations** junta várias contas (produção, homologação, laboratório) numa fatura consolidada e pode compartilhar desconto de compromisso. Uma SCP pode impedir que uma conta crie recurso fora da região combinada. Isso é governança, não um desconto automático.

Ferramentas para ver o resultado, já descritas na seção 19 de `servicos-aws.md`: Cost Explorer (gráfico por serviço, conta, tag), Budgets (alerta quando o previsto cruza um teto), Cost and Usage Report (linha a linha no S3), Pricing Calculator (estimar **antes** de criar).

## Modelos de compra da computação

O preço de lista cheio é o **On-Demand**: sem contrato, por hora ou por segundo (no EC2, mínimo de 60 segundos), do momento em que o recurso sobe até o momento em que ele para ou é terminado.

Os outros modelos trocam flexibilidade por desconto:

| Modelo | O que se compromete | Desconto típico anunciado | Serve para |
| --- | --- | --- | --- |
| **On-Demand** | Nada | Preço cheio | Experimento, pico, carga que não se conhece ainda |
| **Savings Plans** | Um valor em US$/hora, por 1 ou 3 anos | Menor que On-Demand. O desconto depende do prazo e do pagamento à vista | Computação estável. O plano Compute cobre EC2, Lambda e Fargate. Há também planos de EC2 por família, de banco e de SageMaker |
| **Instâncias reservadas** | Capacidade de um tipo, por 1 ou 3 anos, com pagamento à vista total, parcial ou nenhum | Até a casa de 70% em vários anúncios da AWS, conforme serviço e prazo | RDS, Redshift, ElastiCache, OpenSearch, e EC2 quando o tipo da máquina é estável |
| **Spot** | Nada, mas a AWS pode retomar a capacidade com aviso curto | Até cerca de 90% em relação ao On-Demand | Lote, CI, render, worker que pode ser interrompido e refeito |
| **Dedicated Host** | O servidor físico | Em geral mais caro | Licença amarrada a socket e exigência de conformidade |

Savings Plans é o compromisso mais usado hoje para computação geral, porque não prende a um tipo único de instância do jeito que a reserva clássica prendia. O que passar do compromisso volta a ser cobrado como On-Demand. Compromisso que não é usado é prejuízo: a hora contratada é paga mesmo ociosa.

Pagamento do Savings Plan: sem entrada (mais caro), parcial ou total à vista (mais barato). Database Savings Plans têm opção de pagamento mais restrita; o detalhe está na FAQ de Savings Plans.

## Free Tier: o programa antigo e o de 2025

Há dois regimes. A data de corte é **15 de julho de 2025**.

**Conta criada antes dessa data** continua no Free Tier legado: testes de 12 meses (o clássico de 750 horas/mês de `t2.micro` ou `t3.micro`, 5 GB de S3, entre outros), testes curtos e ofertas *always free*.

**Conta nova**, a partir dessa data, entra no programa anunciado no blog da AWS:

- **US$ 100** em créditos na criação da conta e até **mais US$ 100** ao completar atividades (EC2, RDS, Lambda, Bedrock, Budgets), teto de **US$ 200**.
- Dois planos na inscrição. No **plano gratuito**, a conta não gera cobrança além dos créditos; ele acaba em **6 meses** ou quando os créditos acabam, o que vier primeiro, e não acessa todo o catálogo (há serviços bloqueados porque consumiriam o crédito inteiro). No **plano pago**, o que passar do crédito e das cotas gratuitas é cobrado.
- Continua existindo um conjunto de ofertas **always free**, mensais, enquanto a conta existir. Lambda está nesse conjunto: **1 milhão de requests por mês** e **400.000 GB-segundos** de computação por mês.

Always free não significa “a AWS de graça”. Significa uma cota. Acima dela, o preço de lista entra. Crédito que acaba no meio do mês também.

Para um laboratório de disciplina, o risco prático não é o primeiro experimento dentro da cota. É a instância deixada ligada depois do trabalho, o IP público, o NAT e o disco.

## O que cada serviço principal cobra

### EC2

- hora (ou segundo) da instância, conforme o tipo e a região;
- disco **EBS** em GB-mês, mais IOPS se forem provisionados acima do incluído no gp3;
- endereço **IPv4 público**: desde 2024 a AWS cobra endereço IPv4 público na ordem de **US$ 0,005 por endereço por hora**, associado ou não. Um IP esquecido no mês inteiro fica na casa de poucos dólares, e some no meio de uma conta de laboratório;
- transferência de dados (abaixo);
- balanceador e NAT, se existirem: são serviços à parte, com cobrança por hora e por tráfego processado.

Ordem de grandeza oficial, página das instâncias T3, Linux em us-east-1: **t3.micro a US$ 0,0104 por hora**. Em 730 horas (um mês ligado sem parar) isso é cerca de **US$ 7,60** só de computação, antes de disco, IP e saída. Um agregador de preço lista o mesmo tipo em sa-east-1 na ordem de US$ 0,0168 por hora (cerca de US$ 12 no mês). Confirme na calculadora. Desligar a instância para de cobrar a hora de computação. O disco EBS e o IP público continuam.

### Lambda

Dois medidores na função sob demanda:

- **request:** a página de preço usa US$ 0,20 por milhão de requests nos exemplos de us-east-1, depois da cota gratuita;
- **duração:** GB-segundo. A memória configurada (de 128 MB a 10.240 MB) define CPU e o preço do milissegundo. A cota always free de 400.000 GB-segundos zera a duração de um volume pequeno.

Exemplo dentro da cota. Função com 128 MB (0,125 GB) e 200 ms por chamada: cada request gasta 0,025 GB-segundo. Um milhão de chamadas gasta 25.000 GB-segundos, abaixo de 400.000. A computação dessa API cabe no always free. O que pode custar à parte: DynamoDB ou S3 que a função toca, API Gateway se for o modelo REST mais caro, transferência para a internet e log no CloudWatch.

Lambda não cobra enquanto ninguém chama. Esse é o contraste com o EC2 ligado 24 horas para um trabalho que recebe dez acessos por dia.

### S3

Três medidores:

- **armazenamento:** GB-mês da classe. S3 Standard em us-east-1 está na ordem de **US$ 0,023 por GB-mês**. 100 GB ficam em torno de US$ 2,30 no mês, só de guarda. Glacier Deep Archive é muito mais barato e a leitura pode levar horas;
- **request:** PUT, GET, LIST têm preço por milhar. Pouco num site pequeno, relevante num data lake que lista milhão de arquivo;
- **saída:** GB que deixa a região em direção à internet.

A oferta always free clássica de S3, ainda descrita nas páginas de Free Tier, inclui na ordem de 5 GB Standard, 2.000 PUT e 20.000 GET por mês. Confirme na página vigente.

### RDS e DynamoDB

RDS cobra a hora da instância do banco (Multi-AZ cobra a capacidade da standby, então o custo de computação sobe), o armazenamento, backup acima da cota incluída, e transferência. Parar a instância de RDS, quando o motor permite, corta a hora de computação e mantém o armazenamento.

DynamoDB tem dois modos. **Sob demanda:** por request de leitura e de escrita. **Provisionado:** por RCU e WCU reservados, com Auto Scaling. Há uma oferta always free histórica de 25 GB de armazenamento e 25 RCU/25 WCU. Serve para o protótipo. Tabela global (várias regiões) e DAX (cache) são cobranças extras.

### API Gateway, CloudFront, Route 53

API Gateway HTTP é mais barato que API Gateway REST; os dois cobram por request, e o REST cobra também por outros recursos (cache, por exemplo). CloudFront cobra por request e por GB servido na borda, com uma franquia mensal de saída própria (a página de rede global da AWS cita 1 TB gratuito de saída do CloudFront por mês, além de uma franquia de saída das regiões). Route 53 cobra a zona hospedada por mês e as consultas, com franquia. Domínio registrado é outro item.

### Transferência de dados, o vetor que surpreende

Regras que a documentação de rede e o whitepaper repetem:

- **Entrada da internet para a AWS:** em geral não se cobra.
- **Saída da AWS para a internet:** cobra. Há uma franquia de **100 GB por mês** de saída das regiões para a internet, somada entre serviços e regiões (fora China e GovCloud). Acima disso, em us-east-1, a faixa seguinte fica na ordem de **US$ 0,09 por GB** até dezenas de TB, e cai um pouco em faixas maiores. 1 TB de saída no mês já passa de várias dezenas de dólares.
- **Entre serviços na mesma região** (Lambda e S3, EC2 e S3 via gateway endpoint, e vários pares listados na página de cada serviço): em geral sem cobrança de transferência. Há exceções. A página do serviço manda.
- **Entre zonas de disponibilidade:** tráfego que cruza AZ cobra, na ordem de US$ 0,01 por GB em cada sentido em várias regiões. Uma aplicação que fala o tempo todo com um banco em outra AZ paga isso.
- **Entre regiões:** replicação (S3, RDS, DynamoDB global) cobra por GB.
- **NAT Gateway:** a sub-rede privada precisa dele para baixar atualização. Cobra hora do gateway **e** GB processado. Um NAT de laboratório esquecido, mais o tráfego que passa por ele, pesa mais do que a instância `t3.micro`.
- **CloudFront na frente do S3:** a transferência de S3 para CloudFront na origem costuma não ser cobrada como saída direta do S3; a saída passa a ser a do CloudFront, em geral mais barata para conteúdo público. É o ajuste clássico de um site com muito download.

A saída é agregada entre vários serviços (EC2, S3, RDS e outros listados na página de preço) para subir de faixa. Não se soma “100 GB grátis do EC2” com “100 GB grátis do S3”: a franquia de 100 GB é da conta, para a saída à internet.

## Um mês de dois desenhos

Valores arredondados, us-east-1, só para comparar a forma da cobrança. Sem imposto, sem tráfego grande.

**Desenho A. Site num EC2 o mês inteiro.** Uma `t3.micro` Linux ligada 730 horas: cerca de US$ 7,60. Disco gp3 de 8 GB: na ordem de US$ 0,60 a US$ 1. Um IPv4 público o mês inteiro: cerca de US$ 3,50. Sem NAT, sem saída relevante, dentro da franquia de 100 GB. Total na casa de **US$ 12**. Se a instância for desligada fora do horário de demonstração (por exemplo 40 horas no mês), a computação cai para cerca de US$ 0,40 e sobram disco e IP.

**Desenho B. A mesma aplicação em Lambda, dentro da cota.** 1 milhão de requests e a duração do exemplo anterior: computação US$ 0 na cota always free. S3 para os estáticos, dentro de 5 GB: US$ 0 na cota. DynamoDB dentro de 25 GB e das RCUs gratuitas: US$ 0. Pode haver alguns centavos de log. O custo fixo de “estar no ar” some, porque não há máquina.

O desenho A é mais simples de explicar (“é um Linux”). O desenho B é mais barato para carga pequena e irregular. Os dois são AWS. A cobrança é que muda de forma.

Um terceiro número, para mostrar saída: 500 GB a mais de download direto do S3 para a internet, já fora da franquia, na faixa de US$ 0,09/GB, ficam na casa de **US$ 45**. O armazenamento desses 500 GB em Standard seria cerca de US$ 11,50. Em conteúdo muito baixado, a saída supera a guarda. Por isso vídeo e arquivo grande passam por CloudFront, e dado frio vai para classe Glacier em vez de Standard.

## O que a apresentação deve deixar explícito

- Paga-se pelo uso, por serviço, por região. Não há aluguel único da nuvem.
- Os três vetores são computação, armazenamento e saída de dados, mais request nos serviços de API e de objeto.
- On-Demand é o preço cheio. Savings Plans, reserva e Spot reduzem computação estável ou interrompível.
- Free Tier é cota e crédito, com regra diferente antes e depois de julho de 2025. Não é hospedagem ilimitada de graça.
- O que gera susto: recurso ligado sem uso, IPv4 público, NAT Gateway, saída para a internet, Multi-AZ esquecido, log verboso no CloudWatch.
- Região de São Paulo custa mais que Norte da Virgínia. Latência e residência do dado são o motivo de pagar essa diferença.
- Cost Explorer, Budgets e a calculadora existem para ver e estimar. Estão na seção 19 de `servicos-aws.md`.

## Fontes

- Whitepaper *How AWS Pricing Works* (publicação 24 de fevereiro de 2023; a própria página avisa que parte do conteúdo pode envelhecer). Os três vetores e os modelos On-Demand, Savings Plans, Spot e reserva estão ali: https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/welcome.html
- AWS, anúncio do Free Tier com até US$ 200 em créditos, 15 de julho de 2025: https://aws.amazon.com/blogs/aws/aws-free-tier-update-new-customers-can-get-started-and-explore-aws-with-up-to-200-in-credits/
- FAQ do Free Tier: https://aws.amazon.com/free/free-tier-faqs/
- Página de preço do Lambda (request, GB-segundo, 1 milhão de requests e 400.000 GB-segundos): https://aws.amazon.com/lambda/pricing/
- Instâncias T3, t3.micro Linux us-east-1 a US$ 0,0104/hora: https://aws.amazon.com/ec2/instance-types/t3/
- FAQ da rede global (100 GB de saída gratuita por mês, 1 TB de CloudFront): https://aws.amazon.com/about-aws/global-infrastructure/global-network/faqs/
- Savings Plans FAQ (planos Compute, EC2, Database e SageMaker; prazos de 1 e 3 anos): https://aws.amazon.com/savingsplans/faqs/
- Calculadora: https://calculator.aws/

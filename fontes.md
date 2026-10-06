# Fontes da pesquisa

Lista das fontes usadas nos arquivos da pasta `pesquisa`. Os roteiros (`roteiroFalas.md` e `roteiro-apresentacao.md`) não consultam fonte própria: repetem o que está aqui.

`02-servicos-principais.md` também não tem fonte externa. É um recorte de `pesquisa/servicos-aws.md`.

Três itens estavam citados pelo nome, sem URL, nos arquivos originais. O link foi localizado depois e está incluído abaixo: Synergy Research Group, NIST SP 800-145 e a sessão IND387 do re:Invent 2025.

---

## Catálogo de serviços

Arquivo: `pesquisa/servicos-aws.md`

A estrutura por categoria segue a visão geral oficial e a página de produtos. A descrição de cada serviço não veio de uma página consultada serviço por serviço.

- Lista de produtos AWS. https://aws.amazon.com/products/
- Whitepaper *Overview of Amazon Web Services* (categorias oficiais). https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html
- Modelo de responsabilidade compartilhada. https://aws.amazon.com/compliance/shared-responsibility-model/
- AWS Well-Architected Framework. https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html
- Regiões e zonas de disponibilidade. https://aws.amazon.com/about-aws/global-infrastructure/regions_az/

## O que é a AWS

Arquivo: `pesquisa/01-o-que-e-aws.md`

- AWS, “Our Origins”. https://aws.amazon.com/about-aws/our-origins/
- AWS Global Infrastructure (39 regiões e 124 zonas de disponibilidade). https://aws.amazon.com/about-aws/global-infrastructure/
- GeekWire, “AWS at 20” (lançamento em 14 de março de 2006 e receita na casa de US$ 129 bilhões). https://www.geekwire.com/2026/aws-at-20-inside-the-rise-of-amazons-cloud-empire-and-whats-at-stake-in-the-ai-era/
- Synergy Research Group, “Q2 Cloud Market Passes $143 Billion” (2º trimestre de 2026: AWS cerca de 28%, Microsoft cerca de 20%, Google cerca de 15%). https://www.srgresearch.com/articles/q2-cloud-market-passes-143-billion-highest-growth-rate-in-eight-years
- NIST SP 800-145, *The NIST Definition of Cloud Computing* (Mell e Grance, setembro de 2011). https://csrc.nist.gov/pubs/sp/800/145/final
- Modelo de responsabilidade compartilhada. https://aws.amazon.com/compliance/shared-responsibility-model/

## Serviços de IA

Arquivo: `pesquisa/03-servicos-de-ia.md`

Além das páginas abaixo, o texto parte da seção 12 de `pesquisa/servicos-aws.md`.

- Visão geral de produtos AWS. https://aws.amazon.com/products/
- Amazon Bedrock. https://aws.amazon.com/bedrock/
- Amazon SageMaker. https://aws.amazon.com/sagemaker/
- Whitepaper *Overview of Amazon Web Services*. https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html

## Cobrança

Arquivo: `pesquisa/04-como-funciona-a-cobranca.md`

Os valores de lista são ordem de grandeza, em geral para `us-east-1`. A calculadora é a fonte para cravar um número.

- Whitepaper *How AWS Pricing Works* (24 de fevereiro de 2023). Vetores de custo e modelos On-Demand, Savings Plans, Spot e reserva. https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/welcome.html
- AWS, anúncio do Free Tier com até US$ 200 em créditos (15 de julho de 2025). https://aws.amazon.com/blogs/aws/aws-free-tier-update-new-customers-can-get-started-and-explore-aws-with-up-to-200-in-credits/
- FAQ do Free Tier. https://aws.amazon.com/free/free-tier-faqs/
- Preço do Lambda. https://aws.amazon.com/lambda/pricing/
- Instâncias T3 (t3.micro Linux em us-east-1 a US$ 0,0104/hora). https://aws.amazon.com/ec2/instance-types/t3/
- FAQ da rede global (100 GB de saída gratuita por mês e 1 TB de CloudFront). https://aws.amazon.com/about-aws/global-infrastructure/global-network/faqs/
- Savings Plans FAQ. https://aws.amazon.com/savingsplans/faqs/
- AWS Pricing Calculator. https://calculator.aws/

## Exemplos de aplicações

Arquivo: `pesquisa/05-exemplos-de-aplicacoes.md`

Os desenhos genéricos (site, matrícula, aplicativo) saem da seção 25 de `pesquisa/servicos-aws.md`. Os casos com nome de empresa vêm das fontes abaixo.

- ACM Queue, Titus / Netflix. https://queue.acm.org/doi/fullHtml/10.1145/3155112.3158370
- AWS re:Invent 2025, “How Netflix Shapes our Fleet for Efficiency and Reliability” (IND387). Plano de controle ativo-ativo em quatro regiões. https://www.youtube.com/watch?v=K-2u50e0VzA
- Mercado Livre na AWS. https://aws.amazon.com/solutions/case-studies/mercado-livre-summit/
- Mercado Libre, AWS Innovators. https://aws.amazon.com/solutions/case-studies/innovators/mercado-libre/
- iFood, middleware financeiro orientado a eventos. https://aws.amazon.com/blogs/industries/ifood-modernizes-its-financial-middleware-to-event-driven-architecture/
- Nubank, blog de engenharia. https://building.nu.com/managing-cloud-limits/
- Nubank, infraestrutura imutável na AWS. https://aws.amazon.com/blogs/mt/leveraging-immutable-infrastructure-nubank/
- Airbnb. https://aws.amazon.com/solutions/case-studies/airbnb-case-study/
- NASA / Artemis na AWS. https://aws.amazon.com/blogs/media/immersive-viewing-of-the-nasa-artemis-1-launch-with-futuralis-felix-paul-studios-and-aws/
- About Amazon, transmissão 4K da NASA a partir da Lua. https://www.aboutamazon.com/news/aws/how-nasa-streamed-live-4k-video-from-the-moon

## Microsserviços

Arquivo: `pesquisa/06-suporte-a-microservicos.md`

O texto também usa as seções 2, 3, 6, 9 e 10 de `pesquisa/servicos-aws.md` e os cases de Mercado Livre e iFood listados acima.

- AWS Prescriptive Guidance, integração de microsserviços com serviços serverless. https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/welcome.html
- Documentação do Lambda sobre arquitetura orientada a eventos. https://docs.aws.amazon.com/lambda/latest/dg/concepts-event-driven-architectures.html

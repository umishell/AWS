# 1. O que é a AWS

Documento de pesquisa para a pergunta “o que é?”. Não é roteiro de slides.

`servicos-aws.md` abre com uma frase certa e segue para o catálogo. Esta nota completa a definição: o que é computação em nuvem, o que a Amazon Web Services vende, de onde veio e em que escala opera.

## Definição

A Amazon Web Services é a plataforma de computação em nuvem da Amazon. Ela aluga, por API, capacidade de computação, armazenamento, banco de dados, rede, segurança, análise e inteligência artificial. O cliente cria uma conta, escolhe uma região geográfica e passa a provisionar recursos sob demanda. Paga pelo uso. Não compra o data center.

Computação em nuvem, no sentido usado pela indústria e pelo NIST, é um modelo com cinco características:

- **autosserviço sob demanda:** a equipe sobe um servidor ou um bucket sem abrir chamado para um operador do data center;
- **acesso amplo pela rede:** o recurso é alcançado pela internet ou por circuito privado;
- **pool de recursos:** o hardware físico é compartilhado entre clientes, com isolamento lógico;
- **elasticidade rápida:** a capacidade cresce e encolhe;
- **serviço medido:** o uso vira item de fatura.

Há três modelos de serviço. A AWS atravessa os três, com peso diferente em cada um.

| Modelo | Quem administra o quê | Onde aparece na AWS |
| --- | --- | --- |
| **IaaS** (infraestrutura como serviço) | O fornecedor entrega máquina, rede e disco. O cliente instala sistema operacional, middleware e aplicação | Amazon EC2, Amazon VPC, Amazon EBS |
| **PaaS** (plataforma como serviço) | O fornecedor também opera o runtime, o banco ou o orquestrador. O cliente entrega código ou configuração | AWS Lambda, Amazon RDS, Amazon ECS/Fargate, AWS Elastic Beanstalk |
| **SaaS** (software como serviço) | O fornecedor entrega o aplicativo pronto | Amazon Connect (contact center), Amazon WorkMail, Amazon QuickSight. São a minoria do catálogo |

A maior parte do que se chama “AWS” no dia a dia é IaaS e PaaS: blocos para construir o sistema de outra empresa, não um produto único com tela de usuário final. O catálogo passa de 200 serviços. Uma aplicação real usa um punhado deles juntos. A combinação típica está no início de `servicos-aws.md`: computação, armazenamento, banco, rede e identidade.

## De onde veio

A Amazon operava a loja Amazon.com e esbarrou no custo e na lentidão de provisionar infraestrutura para os próprios times. A oferta pública que a empresa trata como lançamento da nuvem é de 2006: o Amazon S3 em 14 de março de 2006 (armazenamento de objetos) e o Amazon EC2 em agosto de 2006 (servidor virtual). A partir daí a plataforma deixou de ser “um lugar para guardar arquivo e ligar máquina” e passou a vender banco gerenciado, fila, DNS, função sem servidor (Lambda, 2014), contêiner, analytics e, mais recentemente, modelos de IA generativa pelo Amazon Bedrock.

Há um marco anterior, de julho de 2002, em que a Amazon abriu APIs do catálogo de produtos para desenvolvedores externos. Algumas narrativas contam a idade da AWS a partir daí. O marco que define a nuvem de infraestrutura é 2006.

A motivação cabe numa frase útil para a abertura da fala: a Amazon transformou em produto a infraestrutura que ela mesma precisava para operar em escala, e passou a vendê-la para qualquer um, no tamanho que a carga pedir.

## O que o cliente escolhe na prática

Três conceitos já estão explicados na seção 1 de `servicos-aws.md` e bastam para a definição:

- **Conta.** Fronteira de identidade, permissão e fatura.
- **Região.** Um conjunto isolado de data centers numa geografia. Para o Brasil, a região de São Paulo é `sa-east-1`. A escolha pesa latência, residência do dado, preço e quais serviços existem ali.
- **Zona de disponibilidade (AZ).** Data centers independentes dentro da região, com energia e rede próprias. Alta disponibilidade, no desenho padrão, significa colocar a carga em mais de uma AZ.

A infraestrutura publicada pela AWS na página de infraestrutura global, consultada nesta pesquisa, é de **39 regiões geográficas e 124 zonas de disponibilidade**, com mais regiões anunciadas (entre elas Arábia Saudita e Chile). A mesma página fala em mais de 750 pontos de presença do CloudFront, dezenas de Local Zones e Wavelength Zones, e numa rede própria da ordem de 20 milhões de quilômetros de fibra. Esses números mudam quando uma região entra no ar. Servem para mostrar escala, não para serem decorados.

Cada região tem, no desenho da AWS, no mínimo três zonas de disponibilidade. Nem todo serviço existe em toda região. Subir um desenho em São Paulo e descobrir que um serviço de IA só está em Virginia do Norte é um limite real de projeto, não um detalhe.

## Quem mais oferece o mesmo tipo de coisa

AWS, Microsoft Azure e Google Cloud são os três provedores grandes de infraestrutura de nuvem. Números de participação dependem de quem mede e do trimestre. A Synergy Research Group, em divulgação de julho de 2026 sobre o segundo trimestre de 2026, colocou a AWS com cerca de **28%** do gasto global em infraestrutura de nuvem, a Microsoft perto de **20%** e o Google perto de **15%**. A receita anual da AWS em 2025 ficou na casa de **US$ 129 bilhões**, segundo o acompanhamento de resultados da Amazon reproduzido na imprensa especializada. A liderança em fatia convive com concorrentes que crescem mais rápido em alguns períodos. Oracle e outros provedores existem e são menores nessa medida.

Para a apresentação, a comparação útil é qualitativa: os três vendem máquina virtual, armazenamento de objeto, banco gerenciado, função sem servidor e API de modelo de IA. Os nomes mudam. O problema que resolvem é o mesmo. A AWS é a mais antiga nessa geração e ainda a de maior fatia nessa métrica de gasto.

## Por que uma aplicação vai para a AWS

O argumento econômico é trocar gasto fixo (comprar servidor, montar sala, pagar energia e gente de data center o ano inteiro) por gasto variável (pagar a hora da máquina e o gigabyte guardado). O argumento operacional é o tempo: uma instância nasce em minutos, uma fila ou um banco gerenciado também, e a capacidade acompanha um pico sem compra antecipada de hardware.

Isso não apaga o trabalho. No modelo de responsabilidade compartilhada, já descrito em `servicos-aws.md`, a AWS cuida da segurança **da** nuvem (instalação física, hardware, hypervisor). Quem sobe a aplicação cuida da segurança **na** nuvem: sistema operacional no EC2, dados, políticas do IAM, bucket aberto, patch da aplicação. A fatura também é do cliente, inclusive a do recurso esquecido ligado.

## O que dizer em uma frase

A AWS é a nuvem pública da Amazon: um catálogo de serviços de infraestrutura e plataforma, cobrados pelo uso, distribuídos em regiões e zonas de disponibilidade, usados para hospedar de um site estático a um sistema de milhares de microsserviços.

## Fontes

- AWS, “Our Origins”: https://aws.amazon.com/about-aws/our-origins/
- AWS, “Global Infrastructure” (39 regiões, 124 zonas de disponibilidade, pontos de presença): https://aws.amazon.com/about-aws/global-infrastructure/
- GeekWire, “AWS at 20” (lançamento em 14 de março de 2006, receita na casa de US$ 129 bilhões): https://www.geekwire.com/2026/aws-at-20-inside-the-rise-of-amazons-cloud-empire-and-whats-at-stake-in-the-ai-era/
- Synergy Research Group, participação no gasto de infraestrutura de nuvem no 2º trimestre de 2026, reproduzida em levantamentos de 2026 (AWS cerca de 28%, Microsoft cerca de 20%, Google cerca de 15%)
- NIST SP 800-145, definição de cloud computing (características e modelos de serviço)
- Modelo de responsabilidade compartilhada: https://aws.amazon.com/compliance/shared-responsibility-model/

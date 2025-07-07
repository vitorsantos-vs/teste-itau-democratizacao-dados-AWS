###### teste-itau-democratizacao-dados-AWS foi criado para o propósito de entregar ao ITAÚ para o processo seletivo de estágio.

Projeto de Democratização de Dados na AWS
Bem-vindo! Este repositório documenta a jornada de construção de um pipeline de dados completo na nuvem da AWS, do zero.

🎯 Objetivo do Projeto
O objetivo principal era criar uma solução de ponta a ponta para democratizar o acesso aos dados. A ideia era tirar os dados de um banco de dados transacional (onde eles ficam "presos" para o uso do dia a dia) e disponibilizá-los de forma fácil, rápida e otimizada para que qualquer pessoa da área de negócio pudesse fazer análises e criar relatórios, sem precisar de conhecimento técnico em banco de dados.

Em resumo: transformar dados brutos em insights de negócio de forma automatizada e escalável.

✅ O que era esperado?
O resultado esperado era um pipeline de dados 100% serverless e automatizado. Isso significa que, uma vez configurado, o fluxo deveria rodar sozinho, sem intervenção manual.

Diariamente, o sistema deveria:

Extrair novos dados da fonte (um banco de dados MySQL no RDS).

Processar e converter esses dados para um formato otimizado para análise (Parquet).

Armazená-los de forma organizada e particionada em um Data Lake no S3.

Catalogar os novos dados para que pudessem ser "descobertos".

Permitir que os dados fossem consultados via SQL padrão.

Atualizar um dashboard no QuickSight com as informações mais recentes.

Tudo isso usando as melhores práticas da AWS, com foco em segurança, baixo custo e escalabilidade.

🌊 Fluxo de Dados
O projeto segue um fluxo clássico e robusto de engenharia de dados na AWS:

RDS (MySQL) → Lambda (.NET) → S3 (Data Lake) → Glue (Catálogo) → Athena (SQL) → QuickSight (Dashboard) → EventBrige (Lambda diáriamente)

![demoratizacao_dados_itau drawio](https://github.com/user-attachments/assets/27653f0d-92c0-432d-9aaf-e73cebfe95ef)

RDS: Nossa fonte de dados original.

Lambda: O "motor" que extrai, transforma e carrega os dados (ETL).

S3: Nosso lago de dados, onde tudo fica armazenado de forma barata e segura.

Glue: O "bibliotecário" que organiza e cataloga nossos dados.

Athena: A ferramenta que nos permite "pescar" informações no lago de dados usando SQL.

QuickSight: A vitrine, onde os dados viram gráficos e relatórios.

📂 Estrutura do Projeto
Este projeto foi dividido em etapas, e cada pasta representa uma peça do quebra-cabeça. Dentro de cada uma, você encontrará um README.md detalhado com o passo a passo e prints de tela.
```
aws-democratizacao-dados/
├── README.md (Você está aqui!)
├── 00-infraestrutura-iam/  (A base: nossa rede e permissões)
├── 01-rds-mysql/          (Onde criamos e populamos nosso banco de dados)
├── 02-lambda-net-parquet/ (O código .NET que faz a mágica acontecer)
├── 03-glue-crawler/       (O nosso detetive/bibliotecário de dados)
├── 04-athena-queries/     (As consultas SQL para validar os dados)
├── 05-quicksight-dashboard/ (Onde criamos o dashboard e automatizamos tudo)
└── 06 06-eventbrige-lambda/ (Onde criação excução diária)
```

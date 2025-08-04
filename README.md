# nyc-yellow-trip-pipeline

![Python](https://img.shields.io/badge/Python-3.10-blue)
![Spark](https://img.shields.io/badge/Apache%20Spark-Dataframe-orange)
![Databricks](https://img.shields.io/badge/Databricks-Platform-red)
![DeltaLake](https://img.shields.io/badge/Delta%20Lake-Acid%20Tables-green)

## 📌 Visão Geral

Este projeto implementa um pipeline de dados completo utilizando o Databricks e o formato Delta Lake para processar e analisar dados de corridas de táxis amarelos (Yellow Taxi) da cidade de Nova York no ano de 2025.

A arquitetura do projeto segue o padrão de camadas do Medallion Architecture:

- **Bronze**: ingestão bruta de dados
- **Silver**: limpeza, tratamento e enriquecimento
- **Gold**: agregações e tabelas analíticas

O objetivo é demonstrar boas práticas de engenharia de dados com automação, padronização e geração de insights.

---

## 🧱 Estrutura do Projeto

```
nyc-yellow-trip-pipeline/
│
├── 01_bronze_ingestion/
│   └── ingestion_2025.py
│
├── 02_silver_transformation/
│   └── cleaning_transformation.py
│
├── 03_gold_analysis/
│   ├── analytical_tables.py
│   └── docs/
│       ├── monthly_revenue_table.png
│       ├── payment_method_table.png
│       └── revenue_by_day_table.png
│
├── pipeline_en.py
└── README.md
```

---

## 🧪 Tecnologias Utilizadas

- **Databricks**
- **PySpark**
- **Delta Lake**
- **Python**
- **Lakehouse (Unity Catalog)**
- **Cloud Storage (via dbutils.fs.cp)**

---

## ⚙️ Etapas do Pipeline

### 1. Camada Bronze – Ingestão Bruta
- Download dos arquivos mensais (`yellow_tripdata_2025-MM.parquet`) via URL pública.
- Lógica de retry para garantir robustez.
- Salvamento em volume `bronze`.

### 2. Camada Silver – Limpeza e Enriquecimento
- Leitura da tabela bronze.
- Limpeza de dados:
  - Padronização para `snake_case`
  - Remoção de registros inválidos ou nulos
  - Eliminação de duplicatas
- Enriquecimento com:
  - Nomes descritivos dos vendors
  - Tipos de pagamento detalhados
- Resultado salvo em `lakehouse.silver.yellow_trip`.

### 3. Camada Gold – Tabelas Analíticas
- Geração de múltiplas tabelas agregadas em Delta:
  - **Faturamento mensal por empresa**
  - **Corridas por forma de pagamento**
  - **Total faturado por vendor**
  - **Dias mais lucrativos**
  - **Horários de maior faturamento**

---

## 📊 Insights Gerados

- **Empresas mais lucrativas**
- **Dias com maior volume e rentabilidade**
- **Distribuição por forma de pagamento**
- **Horários mais rentáveis**
- **Comparativo de distância média x faturamento**

---

## 🖼️ Exemplos de Saídas Visuais

### Faturamento Mensal por Empresa
![Faturamento Mensal](https://raw.githubusercontent.com/seuusuario/nyc-yellow-trip-pipeline/main/docs/monthly_revenue_table.png)

### Corridas por Tipo de Pagamento
![Pagamento](https://raw.githubusercontent.com/seuusuario/nyc-yellow-trip-pipeline/main/docs/payment_method_table.png)

### Faturamento por Dia
![Por Dia](https://raw.githubusercontent.com/seuusuario/nyc-yellow-trip-pipeline/main/docs/revenue_by_day_table.png)

> ℹ️ *As imagens são apenas exemplos. Substitua pelos prints reais salvos na pasta `/docs/` do repositório.*

---

### 🧠 Descrição das Decisões Tomadas

1. **Arquitetura em Camadas (Medallion Architecture):**
   - Foi adotado o modelo de camadas **Bronze → Silver → Gold** para garantir a organização do pipeline, facilitar a manutenção e permitir reaproveitamento dos dados em diferentes níveis de qualidade.

2. **Formato Delta Lake:**
   - Todas as tabelas foram salvas como Delta Tables para garantir performance, versionamento, controle de esquema e operações ACID.

3. **Transformações com PySpark:**
   - Todo o tratamento de dados foi feito com PySpark, aproveitando o ambiente distribuído do Databricks para maior escalabilidade.

4. **Padronização das Colunas:**
   - Os nomes das colunas foram convertidos para o formato `snake_case` por questões de legibilidade e boas práticas de engenharia de dados.

5. **Enriquecimento dos Dados:**
   - Foram adicionadas colunas descritivas para identificar:
     - **Nome das empresas (vendors)** a partir do `vendor_id`
     - **Tipo de pagamento** a partir do `payment_type`

6. **Validação dos Dados:**
   - Registros inválidos foram removidos com filtros simples como:
     - `passenger_count > 0`
     - `trip_distance > 0`
     - `fare_amount > 0`
   - Também foram removidas duplicatas com `.dropDuplicates()`.

7. **Orquestração Automatizada:**
   - Criado um notebook orquestrador para executar as três etapas em ordem (Bronze → Silver → Gold), com tratamento de erro em cada etapa (`try/except`).

---


## 📎 Próximos Passos (Sugeridos)

- Conectar com ferramentas de dashboard (Power BI, Tableau, etc.)
- Aplicar modelos de previsão de demanda

---

## 👨‍💻 Autor

> Projeto desenvolvido como exercício de Engenharia de Dados, com foco em boas práticas, organização e padronização. Finalidade educacional e demonstrativa.

# Catálogo de Dados

## 1. Visão Geral

Este catálogo descreve os conjuntos de dados utilizados no MVP, incluindo suas estruturas, domínios, tipos de dados e linhagem.

Os dados foram obtidos a partir do **[Yelp Open Dataset](https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset/data)**, disponibilizado publicamente na plataforma Kaggle, e processados utilizando a plataforma **Databricks Community Edition**. Sendo considerandos os jsons: 
- `yelp_academic_dataset_business.json` (118.86 MB)
- `yelp_academic_dataset_review.json`(5.34 GB)

Devido a limitações de upload da versão Community (5 GB), foi utilizada uma amostra do arquivo de avaliações (`review.json`) sendo selecionadas 3 milhoes de linhas / registros.

---

## 2. Linhagem dos Dados

**Origem dos dados**
Fonte: Kaggle — Yelp Open Dataset

URL: https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset

Arquivos utilizados:
  - `business.json` (completo)
  - `review.json` (amostra de 3.000.000 registros)

**Pipeline de dados**

`Kaggle Dataset -> Upload em Volume (Unity Catalog) -> Bronze (dados brutos) -> Silver (flat por conceito) -> Gold (modelo estrela)
`

**Técnicas utilizadas**
- Ingestão de arquivos json Lines
- Amostragem por número de registros
- Transformações com PySpark
- Persistência em formato Delta Lake

---

## 3. Tabelas da Camada Silver

### 3.1 `silver_yelp_business`

Tabela contendo informações cadastrais e agregadas dos estabelecimentos.

| Campo | Tipo | Descrição | Domínio / Observações |
|------|------|-----------|----------------------|
| business_id | string | Identificador único do estabelecimento | Não nulo |
| name | string | Nome do estabelecimento | Texto |
| city | string | Cidade | Texto |
| state | string | Estado | Texto |
| postal_code | string | Código postal | Texto |
| latitude | double | Latitude geográfica | -90 a 90 |
| longitude | double | Longitude geográfica | -180 a 180 |
| stars | double | Nota média do estabelecimento | 1.0 a 5.0 |
| review_count | int | Quantidade de avaliações | ≥ 0 |
| is_open | int | Indica se está aberto | 0 (fechado) / 1 (aberto) |
| categories | string | Categorias do estabelecimento | Lista separada por vírgula |

---

### 3.2 `silver_yelp_review`

Tabela contendo as avaliações individuais dos usuários.

| Campo | Tipo | Descrição | Domínio / Observações |
|------|------|-----------|----------------------|
| review_id | string | Identificador único da avaliação | Não nulo |
| business_id | string | Identificador do estabelecimento | FK lógica |
| user_id | string | Identificador do usuário | Texto |
| review_stars | double | Nota atribuída na avaliação | 1.0 a 5.0 |
| review_ts | timestamp | Data e hora da avaliação | Timestamp válido |
| review_date | date | Data da avaliação | Data válida |
| useful | int | Contagem de votos "useful" | ≥ 0 |
| funny | int | Contagem de votos "funny" | ≥ 0 |
| cool | int | Contagem de votos "cool" | ≥ 0 |
| text | string | Texto da avaliação | Texto livre |

---

## 4. Tabelas da Camada Gold (Modelo Estrela)

### 4.1 `gold_dim_business`

Dimensão contendo informações dos estabelecimentos.

| Campo | Tipo | Descrição |
|------|------|-----------|
| business_id | string | Chave primária da dimensão |
| name | string | Nome do estabelecimento |
| city | string | Cidade |
| state | string | Estado |
| postal_code | string | Código postal |
| latitude | double | Latitude |
| longitude | double | Longitude |
| categories | string | Categorias |
| stars | double | Nota média |
| review_count | int | Quantidade de avaliações |
| is_open | int | Indicador de funcionamento |

---

### 4.2 `gold_fact_review`

Tabela fato contendo as avaliações dos estabelecimentos.

**Granularidade:** 1 linha por avaliação.

| Campo | Tipo | Descrição |
|------|------|-----------|
| review_id | string | Chave primária da avaliação |
| business_id | string | Chave estrangeira para dim_business |
| user_id | string | Identificador do usuário |
| review_stars | double | Nota da avaliação |
| review_ts | timestamp | Data/hora da avaliação |
| review_date | date | Data da avaliação |
| useful | int | Votos useful |
| funny | int | Votos funny |
| cool | int | Votos cool |

**Observação:** o texto da avaliação não foi incluído na tabela fato
por motivos de performance e volume, podendo ser tratado em tabelas
analíticas específicas em trabalhos futuros.

---

## 5. Qualidade dos Dados

Foi realizada uma análise da qualidade dos dados nas camadas Silver e Gold, com foco nos atributos críticos para a análise do problema proposto.

Para os atributos numéricos, verificou-se que as notas dos estabelecimentos (`stars`) e das avaliações (`review_stars`) encontram-se dentro do domínio esperado, variando entre 1.0 e 5.0.

Em relação à completude, não foram identificados valores nulos nos campos identificadores (`business_id`, `review_id`) nem nos campos temporais (`review_date`).

Também foi avaliada a integridade referencial entre as tabelas de avaliações e estabelecimentos, tendo sido observada uma correspondência de 100% entre os registros, indicando consistência total entre as entidades.

Conforme evidenciado no resultado das consultas apresentadas abaixo, os dados utilizados apresentam boa qualidade para a realização das análises propostas, não sendo necessária a aplicação de técnicas adicionais de correção ou imputação.

- Não foram identificados valores nulos em chaves primárias ou estrangeiras.
- O domínio das notas (`stars` e `review_stars`) está restrito ao intervalo esperado (1 a 5).
- Houve 100% de correspondência entre avaliações e estabelecimentos no join entre Silver Review e Silver Business.


![Qualidade](https://raw.githubusercontent.com/AnaClaraAlmeida/mvp_eng_dados/main/prints/evidencia_qualidade.png)

![Match](https://raw.githubusercontent.com/AnaClaraAlmeida/mvp_eng_dados/main/prints/match.png)

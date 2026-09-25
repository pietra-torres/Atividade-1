# 📊 Machine Learning Aplicado à Administração — Avaliação 1

## Análise Estratégica da Concentração no Comércio Global de Bens Criativos

Este repositório apresenta a **Avaliação 1 (A1) da disciplina de Machine Learning Aplicado à Administração**, desenvolvida a partir do dataset **OpenFCS**, com foco na exploração e análise de dados de comércio internacional de bens criativos.

O projeto aplica conceitos de **Python, pandas, NumPy, SQL e DuckDB**, além de fundamentos de análise de dados e modelagem dimensional para investigar padrões de comércio entre economias, parceiros comerciais e domínios de bens criativos.

---

## 🎯 Objetivos

O projeto tem como principais objetivos:

- Explorar uma base real de comércio internacional;
- Identificar os domínios de bens criativos presentes nos dados;
- Analisar a quantidade de fluxos comerciais por domínio;
- Avaliar as exportações brasileiras por domínio;
- Comparar resultados obtidos com **pandas** e **SQL/DuckDB**;
- Utilizar `JOIN` para combinar a tabela de comércio com informações de mapeamento de produtos;
- Aplicar conceitos de **modelagem dimensional e esquema em estrela** a uma questão de negócio.

---

## 🗂️ Fonte dos dados

Os dados utilizados são provenientes do **OpenFCS Dataset**, disponibilizado por Monteiro & Dubeux (2026), com licença **CC-BY-4.0**.

- **Dataset:** OpenFCS
- **Fonte:** UNCTAD / UNESCO Framework for Cultural Statistics
- **DOI:** https://doi.org/10.5281/zenodo.21211053

O notebook realiza o download do acervo diretamente do Zenodo e utiliza a resolução de **sete domínios (`cer7`)** para as análises.

---

## 🧰 Tecnologias utilizadas

- **Python**
- **Pandas**
- **NumPy**
- **DuckDB**
- **SQL**
- **Google Colab / Jupyter Notebook**
- **GitHub**

---

## 📁 Estrutura dos dados

O projeto trabalha principalmente com três tabelas:

### `edges7`

Tabela fato contendo os fluxos de comércio após o filtro para a resolução de sete domínios.

Entre as principais colunas utilizadas estão:

- `economy` — economia exportadora;
- `partner` — parceiro comercial;
- `year` — ano do fluxo;
- `fcs_domain` — domínio de bens criativos;
- `value_usd_millions` — valor do fluxo comercial em milhões de dólares;
- `cer_code` — código do produto.

Após o filtro de resolução, a tabela possui **1.026.400 linhas**.

### `entities`

Tabela de dimensão com informações sobre as entidades/economias presentes no acervo.

### `crosswalk`

Tabela utilizada para relacionar os códigos de produtos aos respectivos mapeamentos e informações de classificação.

A chave de produto utilizada no relacionamento com `edges7` é `cer`.

---

## 🔎 Principais análises realizadas

### 1. Exploração dos domínios

Foi construída uma lista com os domínios únicos da base e um dicionário contendo a quantidade de fluxos registrados em cada domínio.

No notebook, o domínio com maior quantidade de fluxos registrados foi:

> **C. Visual arts (crafts) / F. Design**

com **316.124 fluxos**.

---

### 2. Exportações do Brasil

Os dados foram filtrados para considerar o **Brasil como economia exportadora**.

Em seguida, os valores foram agrupados por domínio para calcular o valor total exportado ao longo dos anos disponíveis na base.

O domínio com maior valor total de exportações brasileiras identificado no notebook foi:

> **C. Visual arts (crafts) / F. Design**

com aproximadamente **US$ 31.724,509 milhões**.

---

### 3. SQL com DuckDB × pandas

Foi realizada uma consulta SQL utilizando `duckdb.sql(...)` para calcular o valor total exportado por domínio no ano de **2023**.

O mesmo cálculo foi realizado utilizando `pandas.groupby()`.

Os dois resultados foram então combinados para verificar as diferenças entre as abordagens. O notebook inclui uma verificação automática para confirmar se as diferenças são inferiores a `1e-6`, considerando possíveis diferenças numéricas de ponto flutuante.

Essa comparação demonstra como uma mesma operação analítica pode ser realizada tanto com ferramentas de manipulação de dados em Python quanto com SQL.

---

### 4. JOIN e modelagem dimensional

Foi utilizado um `LEFT JOIN` entre `edges7` e `crosswalk`, relacionando:

```text
edges7.cer_code = crosswalk.cer
```

A consulta busca os **10 maiores fluxos de exportação de 2024**, acrescentando informações relacionadas ao mapeamento dos produtos.

Além disso, o projeto propõe um **esquema em estrela** para responder à pergunta de negócio:

> **"Qual é o parceiro comercial mais importante de cada economia, em cada domínio?"**

A estrutura proposta possui uma tabela fato de comércio e dimensões relacionadas a:

- Economia;
- Parceiro comercial;
- Domínio/produto;
- Tempo.

---

## ⭐ Esquema em estrela proposto

Uma possível representação conceitual é:

```text
                    ┌─────────────────┐
                    │   Dim_Tempo     │
                    ├─────────────────┤
                    │ id_tempo        │
                    │ ano             │
                    └────────┬────────┘
                             │
                             │
┌─────────────────┐    ┌─────▼─────────────┐    ┌─────────────────┐
│  Dim_Economia   │    │   Fato_Comercio   │    │  Dim_Parceiro   │
├─────────────────┤    ├───────────────────┤    ├─────────────────┤
│ id_economia     │◄───┤ id_economia       │    │ id_parceiro     │
│ nome_economia   │    │ id_parceiro       ├───►│ nome_parceiro   │
└─────────────────┘    │ id_dominio        │    └─────────────────┘
                       │ id_tempo          │
                       │ valor_exportado   │
                       └───────┬───────────┘
                               │
                               │
                       ┌───────▼─────────────┐
                       │ Dim_Dominio_Produto │
                       ├─────────────────────┤
                       │ id_dominio          │
                       │ fcs_domain          │
                       │ cer_code            │
                       │ product             │
                       │ mapping_status      │
                       └─────────────────────┘
```

Essa estrutura centraliza os valores de comércio na tabela fato e permite análises por economia, parceiro, domínio/produto e período.

---

## 📓 Notebook

O arquivo principal do projeto é:

```text
avaliacao_1_ml_administracao_pietratorres_kaylarodrigues.ipynb
```

O notebook contém todas as etapas da atividade, incluindo:

1. Download e carregamento dos dados;
2. Filtragem da resolução `cer7`;
3. Exploração dos domínios;
4. Análise das exportações brasileiras;
5. Consultas SQL com DuckDB;
6. Comparação entre SQL e pandas;
7. `LEFT JOIN` com a tabela `crosswalk`;
8. Proposta de modelagem em estrela.

---

## 👥 Autores

**Pietra Torres**  
**Kayla Rodrigues**

Projeto desenvolvido para a disciplina de **Machine Learning Aplicado à Administração**.

---

## 📚 Referências

- Monteiro & Dubeux (2026). **OpenFCS Dataset**. Zenodo.  
  https://doi.org/10.5281/zenodo.21211053

- Material didático da disciplina de Machine Learning Aplicado à Administração.

---

## 📌 Observação

Este repositório foi desenvolvido como atividade acadêmica, com finalidade de aplicação prática dos conteúdos de **Python, pandas, NumPy, SQL, DuckDB e fundamentos de Ciência de Dados** em um problema relacionado à inteligência de mercado.

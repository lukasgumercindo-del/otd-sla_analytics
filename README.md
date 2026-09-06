# 🛵 OTD & SLA Analytics: Otimização de Logística Last-Mile

[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Plotly](https://img.shields.io/badge/Plotly-3F4F75?style=for-the-badge&logo=plotly&logoColor=white)](https://plotly.com/)

Projeto *end-to-end* de engenharia e análise de dados focado na investigação de gargalos operacionais de entregas *last-mile* no município de São Paulo. O projeto abrange desde a modelagem dimensional OLAP no PostgreSQL e integração de dados de clima via API REST até consultas analíticas complexas, visualização de dados executiva e simulação estatística de planos de ação.

---

## 🎯 Hard Skills em Destaque

Neste projeto, o foco foi desenvolver e demonstrar competências técnicas avançadas ao longo de toda a esteira de dados:

* **Modelagem Dimensional de Dados (Star Schema):** Desenho e implementação de tabelas fato e dimensão em SGBD relacional para suporte a consultas OLAP eficientes.
* **SQL Avançado para Analytics:** Uso intensivo de *Common Table Expressions* (CTEs), *Window Functions* (`DENSE_RANK`, `LAG`, `LEAD`), agregações condicionais e otimização de JOINs com integridade referencial.
* **Engenharia de Pipelines & ETL (Python & SQLAlchemy):** Consumo de dados meteorológicos via API REST (Open-Meteo), higienização de datasets volumosos (~300k registros), deduplicação e *bulk load* programático no PostgreSQL.
* **Data Visualization & Storytelling (Plotly):** Construção de dashboards interativos com hierarquia visual, customização manual de `hovertemplate`, mapas de cores semânticos e benchmarks operacionais.
* **Simulação de Cenários & Métodos Quantitativos:** Modelagem probabilística em Python (Pandas/NumPy) para estimar o impacto de decisões de negócio no SLA, preservando a variância real e evitando viés determinístico.

---

## 📖 Storytelling dos Dados (A Jornada do Diagnóstico)

[Visão Macro: SLA Estourado em 37,14%]
│
├──► [O Onde?] ──► Concentração Crítica no Centro (42,8%) e Zona Sul (39,1%)
│
├──► [O Como?] ──► Gargalo Estrutural: Bicicletas têm >43% de atraso em TODAS as zonas
│
├──► [O Quando?] ──► Agravante Climático: Chuva eleva o atraso de ciclistas no Centro a 58,4%
│
└──► [A Solução] ──► Simulação de Despacho Inteligente (Migração de Frota)


### 1. O Diagnóstico Macro
A operação registrava uma taxa global de quebra de SLA (*On-Time Delivery* - OTD) de **37,14%**, valor muito acima do limite aceitável de **30,0%**. A investigação iniciou mapeando o comportamento por região geográfica.

### 2. Desmontando a Causa Raiz
Ao cruzar dados de modais de transporte com zonas geográficas, descobriu-se que o problema não era homogêneo:
* **Incompatibilidade do Modal Bicicleta:** Entregas feitas por ciclistas apresentavam taxas de atraso superiores a **43% em todas as zonas da cidade**, atingindo o ápice no **Centro (51,7%)**. Isso apontou um raio de cobertura irreal para bicicletas.
* **A Moto como Benchmark Operacional:** O modal Moto apresentou desempenho superior em todas as regiões, porém ainda operava no limite do gargalo (entre 30,6% na Zona Leste e 37,8% no Centro).

### 3. O Fator Climático
A integração com dados meteorológicos históricos revelou que a ocorrência de chuva atua como um multiplicador de risco: durante precipitações, o estouro de SLA para bicicletas no Centro salta para **58,4%**, tornando as entregas ciclistas inviáveis nesses períodos sem intervenção no despacho.

---

## 🏗️ Arquitetura do Banco de Dados

O banco de dados PostgreSQL foi estruturado sob a modelagem **Star Schema (Modelagem Dimensional)**:

* **`fact_orders` (Tabela Fato):** 297.733 transações contendo horários de pedido, tempos de entrega, status de SLA (`sla_breached`) e chave de ligação com clima.
* **`dim_restaurants` (Dimensão):** Dados dos estabelecimentos e localização geográfica/zonas de SP.
* **`dim_couriers` (Dimensão):** Informações dos entregadores e modais cadastrados (Bicicleta, Moto).
* **`fact_weather` (Dimensão Temporal/Climática):** Dados meteorológicos por data/hora obtidos via API (temperatura, precipitação, flag de chuva).

---

## 🧪 Simulação do Plano de Ação & Resultados

**Cenário Testado:** Reorientação de 20% das entregas alocadas para bicicletas durante períodos de chuva para o modal Moto.

Para garantir a **honestidade analítica**, a simulação não utilizou a substituição simplista pela mediana de tempo (que eliminaria artificialmente a variabilidade real dos entregadores). Em vez disso, aplicou-se um **modelo probabilístico baseado na taxa empírica de atraso do modal Moto na respectiva zona**.

### Resultados Mensurados:
* **Total de Pedidos Analisados:** 297.733
* **Volume Migrado (Bicicleta + Chuva):** 2.403 pedidos (0,81% da base total)
* **Taxa de Atraso Original (Baseline):** 37,14%
* **Taxa de Atraso Simulada:** 37,02%
* **Redução Absoluta no SLA Global:** -0,12 p.p. (~357 entregas recuperadas do atraso)

### Conclusão de Negócio:
A redução absoluta na média agregada da empresa é modesta (-0,12 p.p.) devido à representatividade reduzida do nicho (*bicicleta + chuva*) no volume global (0,81%). Contudo, a implementação da regra no motor de despacho é **altamente recomendada** por se tratar de uma ação de baixíssimo custo de software que elimina o pico atrito com o cliente final exatamente no momento de maior vulnerabilidade operacional.

---

## 🛠️ Tecnologias & Ferramentas

* **SGBD Relacional:** PostgreSQL
* **Linguagem:** Python 3.13
* **Manipulação de Dados:** Pandas, NumPy, SQLAlchemy, Psycopg2
* **Data Visualization:** Plotly Express, Plotly Graph Objects
* **Consumo de APIs:** Requests (API Open-Meteo)

---

## 📁 Estrutura do Repositório

```text
├── data/               # Scripts de geração de dados sintéticos e ETL
├── sql/                # Queries SQL (Criação do Schema, CTEs, Window Functions)
├── notebooks/          # Notebooks Jupyter com análises EDA e gráficos Plotly
├── simulation/         # Script Python da simulação estatística do Plano de Ação
├── README.md           # Documentação executiva e técnica do projeto

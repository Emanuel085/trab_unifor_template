# Entendimento de Negócio

## Marcos Negociais

???+ abstract "Fase 1: Preparação do Ambiente"
    - [x] Criar repositório Git / GitHub
    - [x] Instalar ferramentas do projeto (`uv`, Python 3.12, VS Code, MkDocs)

???+ abstract "Fase 2: Entendimento de Negócio"
    - [x] Elaborar projeto inicial e Canvas do problema
    - [x] Produzir relatório e refinamento de metas do negócio

???+ abstract "Fase 3: Entendimento dos Dados"
    - [x] Carregar conjunto de dados AI4I 2020
    - [x] Produzir relatório de qualidade dos dados (0 nulos identificados)
    - [x] Realizar descrição estatística dos atributos
    - [x] Realizar análise exploratória (EDA)
    - [x] Isolar rótulos de falha e IDs (regra estrita do PBL)

???+ abstract "Fase 4: Preparação dos Dados"
    - [x] Gerar script de codificação ordinal (`Type`) e padronização (`StandardScaler`)
    - [x] Gerar conjunto de dados processado em formato Parquet
    - [x] Produzir relatório de preparação dos dados

???+ abstract "Fase 5: Modelagem"
    - [x] Gerar desenho do experimento não supervisionado (PCA + Clustering)
    - [x] Executar scripts de modelagem (PCA 2D, K-Means $k=3$, DBSCAN)
    - [x] Produzir relatório com os resultados dos agrupamentos

???+ abstract "Fase 6: Avaliação"
    - [x] Cruzar clusters com o gabarito real de falhas (`Machine failure`)
    - [x] Identificar a persona crítica (Cluster 0 como regime de sobreaquecimento)
    - [x] Produzir relatório de avaliação dos resultados e recomendações

???+ abstract "Fase 7: Implementação"
    - [x] Gerar desenho da implementação viva via MkDocs
    - [x] Estruturar repositório e documentação no GitHub
    - [x] Consolidar relatório final abrangendo todas as etapas da CRISP-DM

---

## Avaliar a Situação

A avaliação da situação do projeto de manutenção preditiva é crucial para garantir o mapeamento eficaz dos regimes de operação de máquinas e prevenir paradas não planejadas.

### Recursos

#### Equipe Técnica

| Nome | Cargo / Função | E-mail |
| :--- | :--- | :--- |
| Carlos Emanuel | Cientista de Dados / Eng. de Dados | carlos.emanuel@exemplo.com |
| Higor Rafael | Cientista de Dados / Engenheiro de Dados | higorferreirarn@hotmail.com  |

#### Especialistas de Negócio

Especialistas da área industrial fornecem o contexto operacional dos equipamentos, apoiando na interpretação física de variáveis como rotação (rpm), torque (Nm) e temperatura (K).

| Nome | Cargo / Função | E-mail |
| :--- | :--- | :--- |
| Engenharia de Manutenção | Especialista de Processos Industriais | manutencao@fabrica.com |
| Gestão da Planta | Gerência de Operações | operacoes@fabrica.com |

#### Equipe de Infraestrutura

Garante o suporte ao ambiente local de desenvolvimento, repositório de código e hospedagem da documentação.

| Nome | Cargo / Função | E-mail |
| :--- | :--- | :--- |
| Carlos Emanuel | DevOps / Infraestrutura Local | carlos.emanuel@exemplo.com |
| Higor Rafael | DevOps / Infraestrutura Local | higorferreirarn@hotmail.com |

#### Recursos Financeiros
O projeto utiliza uma pilha tecnológica de código aberto (*open-source*), incluindo o gerenciador de pacotes `uv`, Python, VS Code e repositório gratuito no GitHub, resultando em custo direto zero de licenças de software na fase de P&D.

---

### Requisitos do Projeto

#### Necessidades de Negócio
A fábrica opera sem um mapeamento prévio dos regimes de trabalho de suas máquinas. Desvios térmicos e sobrecargas passam despercebidos, resultando em quebras inesperadas. É necessário segmentar o parque fabril em regimes operacionais claros para atuar preventivamente antes das falhas.

#### Requisitos Funcionais
* Identificar agrupamentos operacionais sem utilizar rótulos prévios de falha durante o treinamento (aprendizado não supervisionado).
* Delimitar o número final de regimes em no máximo 5 grupos para facilitar a gestão no chão de fábrica (definido $k=3$).
* Detectar pontos atípicos/outliers de operação via algoritmos baseados em densidade (DBSCAN).

#### Requisitos Técnicos

| Ferramenta | Descrição |
| :--- | :--- |
| **Git / GitHub** | Gerenciamento e controle de versão do código. |
| **`uv`** | Gerenciamento rápido de dependências e ambiente virtual. |
| **Python 3.12** | Linguagem principal para ETL, EDA e modelagem. |
| **Jupyter Notebook** | Ambiente interativo para prototipagem dos scripts. |
| **Bibliotecas Data Science** | Pandas, NumPy, Scikit-Learn, Matplotlib, Seaborn e Polars. |

#### Requisitos de Dados
* Conjunto com 10.000 ciclos operacionais contendo sensores de temperatura ambiente e de processo, velocidade de rotação, torque e desgaste de ferramenta.
* Isolamento estrito de identificadores (`UDI`, `Product ID`) e variáveis de falha (`Machine failure`, etc.) para cumprir as diretrizes de aprendizado não supervisionado.

---

### Riscos

* **Incoerência com a Documentação Externa:** A página genérica do repositório UCI apresenta discrepâncias na contagem de tipos de falha em relação aos dados reais. *Mitigação:* Análise exploratória direta no dataset real via código Python.
* **Vazamento de Dados (*Data Leakage*):** Utilizar rótulos de falha durante a clusterização invalida o caráter não supervisionado. *Mitigação:* Separação estrita dos rótulos em dataframe auxiliar (`df_labels`) logo na fase de EDA.
* **Distorções por Escala de Sensores:** Variáveis com magnitudes distantes (ex.: 1500 rpm vs 40 Nm) podem distorcer o cálculo da distância euclidiana. *Mitigação:* Aplicação de padronização via `StandardScaler`.

---

### Análise Custo-Benefício

* **Custos:** Horas-homem de desenvolvimento e processamento local.
* **Benefícios:** A identificação do **Cluster 0** (Regime de Sobreaquecimento) permite que a equipe de manutenção atue em um grupo específico de máquinas que responde por **60,47% das falhas reais da fábrica**, reduzindo drasticamente os custos com paradas não planejadas e substituição de peças.

---

## Mineração de Dados

A abordagem não supervisionada extrai os regimes naturais do dataset AI4I 2020. Ao segmentar as máquinas em 3 clusters principais via K-Means e identificar 110 anomalias pelo DBSCAN, disponibiliza-se um conhecimento estruturado para consumo pelas áreas de:

* **Business Intelligence & Analytics:** Acompanhamento dos clusters em painéis operacionais.
* **Manutenção Preditiva:** Aplicação de regras de alívio de torque e resfriamento nas máquinas enquadradas no Cluster 0.
* **Governança dos Dados:** Garantia de rastreabilidade, versionamento de código e documentação viva da metodologia CRISP-DM.

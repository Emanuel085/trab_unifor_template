# Critérios de sucesso

O sucesso do projeto é dado pela completude dos objetivos de negócio definidos a seguir. Cada objetivo está descrito no formato de história de usuário (**Como** / **Quero** / **Para** / **Objetivo**), acompanhado dos respectivos critérios de aceite.

## Visão geral

| # | Objetivo de negócio | Solicitante (papel) | Entrega principal |
|:-:|:---|:---|:---|
| 1 | Mapeamento não supervisionado de regimes operacionais | Engenheiro de Manutenção | Notebook `3.0-modelagem.ipynb` e dataset Parquet processado |
| 2 | Detecção de anomalias e comportamentos atípicos | Analista de Confiabilidade | Experimento DBSCAN e projeção 2D PCA em `3.0-modelagem.ipynb` |
| 3 | Identificação do regime crítico de falhas (Persona) | Gestor de Operações da Planta | Tabela cruzada e perfil operacional em `4.0-avaliacao.ipynb` |

---

## 1. Mapeamento não supervisionado de regimes operacionais

???+ success "Segmentação dos regimes operacionais das máquinas"
    **Como** Engenheiro de Manutenção Preditiva<br>
    **Quero** identificar e agrupar automaticamente os diferentes regimes de trabalho das máquinas da planta a partir de sensores de temperatura, rotação, torque e desgaste<br>
    **Para** compreender os padrões operacionais sem depender de rotulagem manual prévia<br>
    **Objetivo** segmentar a frota de equipamentos em até 5 regimes operacionais bem definidos para direcionar planos de manutenção diferenciados

    **Critérios de aceite:**

    - [x] Isolar estritamente os identificadores (`UDI`, `Product ID`) e os rótulos de falha (`Machine failure`, etc.) do conjunto de treino durante a modelagem não supervisionada.
    - [x] Padronizar as variáveis numéricas com `StandardScaler` para remover distorções de magnitude entre sensores.
    - [x] Aplicar algoritmo K-Means e definir o número ideal de clusters $k \le 5$ fundamentado no Método do Cotovelo e Coeficiente de Silhueta (definido $k=3$).

    **Entrega:** Notebooks `2.0-preparacao.ipynb` e `3.0-modelagem.ipynb`, acompanhados do artefato processado em `data/processed/ai4i2020_processed.parquet`.

## 2. Detecção de anomalias e comportamentos atípicos

???+ success "Identificação de ciclos de operação anômalos"
    **Como** Analista de Confiabilidade Industrial<br>
    **Quero** detectar automaticamente ciclos operacionais atípicos e ruídos nas variáveis de sensores<br>
    **Para** alertar a equipe técnica sobre máquinas operando fora das condições padrão de projeto<br>
    **Objetivo** isolar anomalias operacionais extremas antes que resultem em desgaste severo do equipamento

    **Critérios de aceite:**

    - [x] Projetar os dados multidimensionais em um espaço 2D utilizando Análise de Componentes Principais (PCA) mantendo variância explicada acumulada relevante ($>60\%$, atingido 62,53%).
    - [x] Treinar o algoritmo DBSCAN (`eps=1.2`, `min_samples=15`) para identificar pontos de ruído e densidade baixa.
    - [x] Quantificar os pontos anômalos detectados (`Cluster -1`) e visualizar sua distribuição espacial nas extremidades do plano PCA (110 anomalias isoladas).

    **Entrega:** Células de código de PCA e DBSCAN com gráficos de dispersão salvos no notebook `3.0-modelagem.ipynb` e documentados em `docs/modelagem.md`.

## 3. Identificação do regime crítico de falhas (Persona)

???+ success "Validação de negócio e priorização preventiva"
    **Como** Gestor de Operações da Planta<br>
    **Quero** cruzar os clusters não supervisionados obtidos com o histórico real de falhas das máquinas<br>
    **Para** validar a utilidade prática da modelagem e identificar qual grupo apresenta o maior risco operacional<br>
    **Objetivo** focar a atuação preventiva e regras de resfriamento/alívio de torque no regime de maior impacto econômico para a fábrica

    **Critérios de aceite:**

    - [x] Gerar tabela cruzada (*crosstab*) entre os clusters do K-Means e a variável de validação `Machine failure`.
    - [x] Comprovar estatisticamente a concentração de falhas em um regime crítico (Cluster 0 concentrou 60,47% das falhas totais, com taxa de falha de 5,22%).
    - [x] Mapear o perfil médio em escala física das variáveis de cada cluster para definir as personas operacionais do chão de fábrica.

    **Entrega:** Tabela de perfil operacional e cruzamento de validação salvos no notebook `4.0-avaliacao.ipynb` e documentados em `docs/avaliacao.md`.

---

## Consolidado dos critérios de aceite

| Objetivo | Critérios de aceite | Concluídos | Observação |
|:---|:-:|:-:|:---|
| 1. Mapeamento de regimes operacionais | 3 | 3 | Concluído via K-Means ($k=3$) e padronização |
| 2. Detecção de anomalias | 3 | 3 | Concluído via DBSCAN (110 outliers) e PCA (62,53% variância) |
| 3. Identificação do regime crítico | 3 | 3 | Concluído (Cluster 0 identificado como sobreaquecimento térmico) |
| **Total** | **9** | **9** | **100% dos critérios de aceite atingidos** |

## Pendências de dados

Não há pendências de dados abertas para o escopo não supervisionado. Todas as variáveis operacionais necessárias de sensores (temperaturas, rotação, torque e desgaste) vieram completas sem valores ausentes no dataset AI4I 2020.

| # | Pendência | Destrava |
|:-:|:---|:---|
| — | Nenhuma pendência identificada | Todos os critérios de aceite foram validados no dataset atual |

!!! info "Fonte"
    Critérios estabelecidos com base no Termo de Abertura do Projeto — Tema 10 (Indústria, Energia e Meio Ambiente) e nas especificações da metodologia CRISP-DM / PBL da disciplina (Versão 1.0, Outubro/2026).

# Manutenção Preditiva Industrial (AI4I 2020) — Post-MBA UNIFOR

Este projeto desenvolve uma solução de inteligência de dados voltada para a **Manutenção Preditiva Industrial**, utilizando o conjunto de dados *AI4I 2020 Predictive Maintenance* (UCI Repository). A proposta visa mapear e categorizar autonomamente os regimes de operação de máquinas industriais e detectar anomalias severas de sensores antes que falhas catastróficas ocorram no chão de fábrica.

Seguindo estritamente as diretrizes de **Aprendizado Não Supervisionado (PBL)** e a metodologia **CRISP-DM**, todos os rótulos históricos de falha foram isolados durante a fase de treino. O agrupamento combina redução de dimensionalidade (PCA) com algoritmos de clusterização por partição (K-Means) e por densidade (DBSCAN).

---

## Objetivos e resultados chave

### 1. Mapear Regimes Operacionais e Isolamento de Falhas
* **Resultados-Chave:**
  * Isolar 100% das colunas de identificação e rótulos de falha (`Machine failure`, `TWF`, `HDF`, etc.) em dataframe separado.
  * Tratar e padronizar 6 variáveis contínuas de sensores (`Air temp`, `Process temp`, `Rotational speed`, `Torque`, `Tool wear`) e a variável categórica de qualidade (`Type`).
  * Aplicar o Método do Cotovelo e o Coeficiente de Silhueta para determinar o número ideal de agrupamentos ($k=3$).

### 2. Redução Dimensional e Detecção de Anomalias
* **Resultados-Chave:**
  * Projetar as variáveis contínuas em espaço 2D via PCA retendo 62,53% da variância explicada acumulada.
  * Ajustar o algoritmo DBSCAN (`eps=1.2`, `min_samples=15`) sobre a base padronizada.
  * Mapear e isolar 110 pontos discrepantes de baixa densidade (`Cluster -1`) operando em limites críticos.

### 3. Validação do Regime Crítico e Personas Operacionais
* **Resultados-Chave:**
  * Gerar tabela cruzada (*crosstab*) com a variável de validação `Machine failure`.
  * Comprovar que o **Cluster 0 (Regime de Sobreaquecimento Térmico)** concentrou 60,47% de todas as falhas reais registradas na fábrica.
  * Traduzir os centróides dos clusters para unidades físicas ($K$, $rpm$, $Nm$, $min$), definindo 3 personas operacionais claras para a equipe de manutenção.

---

## Conteúdo

### Notebooks (`notebooks/`)
* **`1.0-eda.ipynb`**: Análise exploratória de dados, diagnóstico de distribuições, matriz de correlação ($r=0,88$ térmico e $r=-0,88$ mecânico) e isolamento dos rótulos de falha.
* **`2.0-preparacao.ipynb`**: Pipeline de tratamento, codificação ordinal (`Type`), padronização de escala com `StandardScaler` e exportação do Parquet curado.
* **`3.0-modelagem.ipynb`**: Execução da redução de dimensionalidade com PCA 2D, clusterização K-Means ($k=3$) e detecção de anomalias por densidade com DBSCAN.
* **`4.0-avaliacao.ipynb`**: Avaliação dos clusters contra os rótulos de validação, cálculo do percentual de captura de falhas e mapeamento do perfil médio dos equipamentos.

### Documentação Viva (`docs/`)
Páginas navegáveis em Markdown publicadas via MkDocs detalhando cada etapa do CRISP-DM: `index.md`, `metodologia.md`, `entendimento-negocio.md`, `criterios-sucesso.md`, `fonte-dados.md`, `entendimento-dados.md`, `analise-exploratoria.md`, `preparacao.md`, `modelagem.md`, `avaliacao.md`, `implementacao.md` e `status-projeto.md`.

---

## Utilização

O projeto utiliza o **`uv`** como gerenciador de dependências e ambiente virtual.

```bash
# 1. Criar o ambiente virtual e instalar todas as dependências (usa o uv.lock)
uv sync

# 2. Servir a documentação viva localmente (MkDocs)
uv run mkdocs serve

# 3. Executar os notebooks sequenciais da pipeline
uv run jupyter execute notebooks/1.0-eda.ipynb
uv run jupyter execute notebooks/2.0-preparacao.ipynb
uv run jupyter execute notebooks/3.0-modelagem.ipynb
uv run jupyter execute notebooks/4.0-avaliacao.ipynb
```

A versão do Python utilizada está fixada em [`.python-version`](.python-version); o `uv` instala e usa essa versão automaticamente.

## Desenvolvedores
 - [Carlos Emanuel — Cientista de Dados / DevOps](https://github.com/Emanuel085)
 - [Higor Rafael — Engenheiro de Dados / Governança de Pipeline](https://github.com/higorferreirarn)

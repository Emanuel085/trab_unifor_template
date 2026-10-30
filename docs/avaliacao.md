# Avaliação dos resultados

Enquanto a avaliação feita em [Modelagem dos dados](modelagem.md) é **técnica**, esta página olha para o **projeto como um todo**: os resultados atendem aos objetivos de negócio, o processo foi conduzido corretamente, e o que fazer em seguida.

---

## Confronto com os critérios de aceite

Cada critério de aceite comparado com o que foi efetivamente entregue.

| Objetivo | Critério de aceite | Situação | Evidência |
|:---|:---|:---|:---|
| **1. Regimes Operacionais** | Isolar estritamente identificadores e rótulos de falha durante o aprendizado. | **atendido** | DataFrame `df_labels` criado em `1.0-eda.ipynb` e mantido isolado em todo o pipeline. |
| **1. Regimes Operacionais** | Padronizar variáveis numéricas com `StandardScaler`. | **atendido** | Transformação aplicada e validada em `2.0-preparacao.ipynb` ($\mu=0, \sigma=1$). |
| **1. Regimes Operacionais** | Definir número ideal de clusters $k \le 5$ via Cotovelo/Silhueta. | **atendido** | Escolha de $k=3$ fundamentada graficamente no notebook `3.0-modelagem.ipynb`. |
| **2. Detecção de Anomalias** | Projetar dados em espaço 2D via PCA com variância explicada $> 60\%$. | **atendido** | Projeção PCA em `3.0-modelagem.ipynb` retendo 62,53% da variância acumulada. |
| **2. Detecção de Anomalias** | Treinar algoritmo DBSCAN para identificar ruídos/densidade baixa. | **atendido** | Modelo DBSCAN (`eps=1.2`, `min_samples=15`) em `3.0-modelagem.ipynb`. |
| **2. Detecção de Anomalias** | Quantificar e mapear os pontos anômalos isolados. | **atendido** | 110 anomalias identificadas (`Cluster -1`) registradas em `3.0-modelagem.ipynb`. |
| **3. Regime Crítico (Persona)** | Gerar tabela cruzada (*crosstab*) com a variável de validação `Machine failure`. | **atendido** | Tabela de contagem cruzada e percentuais gerada em `4.0-avaliacao.ipynb`. |
| **3. Regime Crítico (Persona)** | Comprovar estatisticamente a concentração de falhas em um regime crítico. | **atendido** | **Cluster 0** concentrou 60,47% das falhas reais da fábrica (taxa de falha de 5,22%). |
| **3. Regime Crítico (Persona)** | Mapear o perfil médio em escala física para definir personas operacionais. | **atendido** | Tabela de médias por cluster salva e documentada em `4.0-avaliacao.ipynb`. |

**Leitura Consolidada:** Todos os **9 critérios de aceite (100%)** foram integralmente atendidos. A modelagem não supervisionada provou ser capaz de identificar autonomamente o regime de maior risco operacional da planta (Cluster 0 — Sobreaquecimento Térmico), fornecendo um direcionamento claro para a equipe de manutenção preditiva reduzir em até 60% as paradas não planejadas.

---

### Critérios não atendidos

| Critério | Causa | Natureza |
|:---|:---|:---|
| *Nenhum critério em aberto* | Todos os 9 critérios foram validados com sucesso no dataset AI4I 2020. | N/A |

Todos os requisitos previstos no escopo inicial foram totalmente satisfeitos com os dados disponíveis.

---

## Revisão do processo

Retrospectiva da condução do projeto:

- **O que funcionou:**
    - O gerenciamento de dependências e ambiente virtual com **`uv`**, garantindo instalações instantâneas e reprodutibilidade imediata mesmo após formatação de máquina.
    - O isolamento rigoroso dos rótulos de falha desde a EDA, garantindo a adesão perfeita aos princípios de aprendizado não supervisionado (diretrizes PBL).
    - A combinação de PCA para visualização 2D com K-Means para segmentação e DBSCAN para outliers.
- **O que atrasou:**
    - Necessidade de reinstalação e reconfiguração do ambiente de desenvolvimento em máquina recém-formatada (resolvido rapidamente com `uv`).
    - Ajuste de escala e interpretação das variáveis físicas originais versus transformadas para garantir que a tradução para o negócio fosse intuitiva.
- **O que foi esquecido:**
    - Inicialmente, a documentação viva em MkDocs não continha os links diretos para a tabela de artefatos salvos em Parquet (corrigido durante a revisão da fase de avaliação).

---

## Próximas etapas

Decisão sobre o destino do projeto:

!!! info "Decisão"
    - [x] **Implantar** — Aprovado para estruturação e entrega da documentação viva MkDocs final.
    - [ ] **Iterar** — Não necessário no escopo acadêmico/PBL atual.
    - [ ] **Encerrar** — Projeto concluído com sucesso.

| Ação | Responsável | Prazo |
|:---|:---|:---|
| Finalizar o preenchimento de todos os arquivos `.md` na pasta `docs/` | Cientista de Dados | Imediato |
| Validar a renderização local via `uv run mkdocs serve` | DevOps / Infra | Imediato |
| Realizar o commit final e push das alterações para o repositório Git/GitHub | Engenharia de Dados | Final do ciclo |
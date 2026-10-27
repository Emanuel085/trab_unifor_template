# Entendimento dos dados

Esta página responde a três perguntas sobre o conjunto de dados: **o que existe**, **em que qualidade** e **o que isso impede ou permite** dentro dos objetivos do projeto.

## Dados iniciais

Os dados analisados foram extraídos diretamente do arquivo `data/raw/ai4i2020.csv` Toda a apuração e geração de estatísticas descritivas, gráficos de distribuição e matrizes de correlação estão documentadas no notebook **`notebooks/1.0-eda.ipynb`**

## Descrição dos dados

### Grão

Cada linha é um **ciclo operacional individual de usinagem de máquina** (`UDI`).

A tabela encontra-se achatada (desnormalizada), embutindo características físicas do produto, medições contínuas de sensores de processo e registros históricos de falhas industriais no mesmo registro.

### Grupos de colunas

| Dimensão | Exemplos de colunas |
|:---|:---|
| **Identificação do Registro** | `UDI`, `Product ID` |
| **Qualidade do Produto** | `Type` (`L`, `M`, `H`) |
| **Sensores Térmicos** | `Air temperature [K]`, `Process temperature [K]` |
| **Sensores Mecânicos** | `Rotational speed [rpm]`, `Torque [Nm]` |
| **Desgaste do Componente** | `Tool wear [min]` |
| **Validação / Falhas (Isoladas)** | `Machine failure`, `TWF`, `HDF`, `PWF`, `OSF`, `RNF` |

### Cobertura frente aos criteria de sucesso

| Requisito | Situação |
|:---|:---|
| Mapeamento de regimes sem rótulos | **atendida** — Variáveis de sensores (`Air temp`, `Process temp`, `Rotational speed`, `Torque`, `Tool wear`) estão completas e foram isoladas das colunas de falha. |
| Delimitação em até 5 clusters ($k \le 5$) | **atendida** — Métricas de inércia e silhueta aplicadas sobre os dados numéricos padronizados confirmaram $k=3$ como partição ideal. |
| Detecção de anomalias operacionais | **atendida** — Presença de desvios extremos identificados nas variáveis mecânicas permite isolamento via DBSCAN. |

---

## Qualidade dos dados

### Valores sentinela

A análise via pandas/code apurou **0 valores nulos/ausentes** em todas as 14 colunas. Também não foram identificados códigos negativos ou textos genéricos usados como sentinelas de ausência.

| Sentinela | Significado |
|:---|:---|
| *Nenhuma sentinela encontrada* | Todos os registros possuem valores numéricos ou categóricos válidos no domínio da variável. |

As regras de validação foram aplicadas no notebook `1.0-eda.ipynb`. Como não há valores nulos ou sentinelas, nenhuma linha precisou ser descartada por inconsistência de preenchimento.

### Achados que mudam a análise

1. **Redundância Térmica ($r = 0,88$):** As temperaturas do ar (`Air temperature`) e do processo (`Process temperature`) possuem forte correlação positiva. *O que muda:* A redução de dimensionalidade via PCA é fundamental para condensar a variância térmica em uma única dimensão principal sem duplicar peso no clustering.
2. **Inversão de Potência Torque/Rotação ($r = -0,88$):** Forte correlação negativa refletindo a física de motores elétricos. *O que muda:* Confirma que agrupamentos baseados em distância euclidiana capturam regimes operacionais reais (ex.: alta rotação/baixo torque vs. baixa rotação/alto torque).
3. **Assimetria e Outliers em Torque e Rotação:** A velocidade de rotação apresenta cauda longa à direita (outliers de alta rotação). *O que muda:* Exige obrigatoriamente a padronização via `StandardScaler` para impedir que a amplitude de rotação (1100–2800 rpm) domine indevidamente a variável de torque (3–76 Nm).

---

## Dimensões-chave

Uma dimensão derivada criada na fase de preparação foi a codificação ordinal da categoria de qualidade do produto (`Type`).

A coluna original armazena texto indicando a variante do produto (`L`, `M`, `H`). Como essas variantes representam níveis hierárquicos de qualidade e tolerância de fabricação, aplicou-se a ordenação numérica.

| Valor | Descrição | Regra de derivação |
|:---|:---|:---|
| `0` | Low (`L`) | Produto variante Low (responde por 60% da produção) |
| `1` | Medium (`M`) | Produto variante Medium (responde por 30% da produção) |
| `2` | High (`H`) | Produto variante High (responde por 10% da produção) |

---

## Limitações

1. **Ausência de Sequência Temporal Contínua:** O dataset não fornece marcações de data/hora (*timestamps*) reais entre os ciclos de usinagem. *Impacto:* Impede a modelagem de séries temporais para prever o momento exato de uma quebra futura, restringindo o escopo ao agrupamento de regimes operacionais e detecção de anomalias pontuais.
2. **Dados Sintéticos Normalizados:** Embora realista, a base provém de um gerador sintético de processos (UCI AI4I 2020). *Impacto:* Os clusters identificados representam os regimes do modelo de simulação e devem ser revalidados com sensores de telemetria física real antes de implantar automação direta na planta.

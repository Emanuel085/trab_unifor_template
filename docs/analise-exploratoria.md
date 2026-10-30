# Análise exploratória

Esta página resume o desenho da análise exploratória: quais notebooks existem, o que cada um responde e as decisões metodológicas que valem para todos. Os resultados visuais e numéricos ficam salvos na pasta de gráficos do projeto e nos arquivos `.parquet` processados.


## Mapa dos notebooks

| Notebook | Objetivo | Recorte | Saída principal |
|:---|:-:|:---|:---|
| `notebooks/1.0-eda.ipynb` | 1, 2, 3 | Base integral (10.000 registros, 14 colunas) | Diagnóstico de distribuições, matriz de correlação, detecção de outliers e separação de rótulos de falha

A coluna **Objetivo** referencia a numeração dos [critérios de sucesso](criterios-sucesso.md).

## Desenho da EDA

| Seção | Técnica | O que responde |
|:---|:---|:---|
| **Valores ausentes** | Mapeamento por contagem de nulos (`df.isna().sum()`) | A ausência é aleatória ou estrutural? *(Confirmada ausência total de nulos na base)* |
| **Univariada** | Histogramas, Boxplots e estatística descritiva ($\mu, \sigma$, assimetria) | Como cada variável de sensor se distribui individualmente? |
| **Outliers** | Intervalo Interquartil (IQR) e limites z-score | Há valores implausíveis ou extremos nas medições de torque e rotação? |
| **Bivariada** | Matriz de correlação de Pearson e gráficos de dispersão (*scatter plots*) | Quais variáveis operacionais variam juntas? *(Confirmada forte relação $r=0,88$ entre temperaturas e $r=-0,88$ entre torque e rotação)* |
| **Multivariada** | Projeção linear via Análise de Componentes Principais (PCA) | Que estrutura e separabilidade existem no conjunto multidimensional sem uso de rótulos? |
| **Temporal / Sequencial** | Análise de variabilidade por ordem de registros (`UDI`) | O comportamento dos sensores oscila ou apresenta tendências ao longo do tempo de coleta? |

---

![](imagens/eda-steps.png)

### Escolhas metodológicas

Cada item declara **a decisão** e **o motivo** — o motivo é o que evita que a decisão seja
revertida por engano depois.

??? note "Decisões que costumam entrar aqui"
    - **percentual × volume** ao comparar grupos de tamanhos muito diferentes: valor
      absoluto mede tamanho, não comportamento;
    - **como tratar a ausência**: calcular sobre a base com informação e reportar o
      percentual ausente, em vez de descartar linhas ou tratar a sentinela como categoria;
    - **associação em vez de contagem cruzada**: a tabela de contingência bruta é dominada
      pelas categorias grandes;
    - **média × mediana** quando existe cauda longa legítima;
    - **amostragem** em cálculos de custo quadrático, quando a leitura comparativa não muda.

* **Isolamento estrito dos rótulos de falha:** O dataset inclui colunas indicadoras de falha (`Machine failure`, `TWF`, `HDF`, etc.). Para respeitar a regra do PBL e garantir o aprendizado não supervisionado, todos os rótulos foram isolados no dataframe auxiliar `df_labels` antes de qualquer análise de agrupamento.
* **Padronização sobre normalização min-max:** Optou-se pelo uso de `StandardScaler` ($\mu=0, \sigma=1$) em vez de escalonamento min-max. Motivo: a presença de caudas longas e outliers em rotação e torque distorceria o intervalo $[0,1]$, enquanto a padronização preserva a variância real para o cálculo de distâncias euclidianas.
* **Codificação Ordinal da Variável Categórica `Type`:** A variável `Type` (`L`, `M`, `H`) foi convertida para $(0, 1, 2)$ via `OrdinalEncoder`. Motivo: preservar a hierarquia física das variantes de produto sem criar escarsidade via *One-Hot Encoding*.
* **Avaliação via Médias e Desvios Padrão Físicos:** Os agrupamentos finais foram interpretados convertendo os centróides de volta para as unidades originais ($K$, $rpm$, $Nm$, $min$). Motivo: permitir a tradução direta dos clusters para a linguagem operacional do chão de fábrica.

## Reprodução

```bash
# Executa o notebook de EDA e atualiza o fluxo
uv run jupyter execute notebooks/1.0-eda.ipynb
```

**Nota de Versionamento: Os notebooks do repositório são limpos antes do commit para evitar sobrecarga de arquivos binários. As métricas e transformações estruturais são salvas em formato tabular durante a preparação.**

## Dados por trás de cada gráfico


Diretório de Exportação do Dataset Processado: data/processed/

Formato de Saída: Apache Parquet (ai4i2020_processed.parquet)

Função e Módulo de Gravação: O salvamento é realizado via pandas.DataFrame.to_parquet() no final da pipeline de preparação.

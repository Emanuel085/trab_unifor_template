# Status do projeto

???+ success "Fase 1: Preparação do ambiente"
    - [x] Criar repositório Git / GitHub
    - [x] Instalar e configurar ferramentas do projeto (`uv`, Python 3.12, VS Code, MkDocs)

???+ success "Fase 2: Entendimento de negócio"
    - [x] Elaborar projeto inicial e Canvas do problema
    - [x] Produzir relatório de mineração de dados e alinhamento das metas do negócio

???+ success "Fase 3: Entendimento dos dados"    
    - [x] Carregar e validar conjunto de dados AI4I 2020 Predictive Maintenance (UCI)
    - [x] Produzir relatório de qualidade dos dados (0 nulos identificados)
    - [x] Realizar descrição das variáveis de sensores e variantes de produto
    - [x] Realizar análise exploratória (EDA) completa no notebook `1.0-eda.ipynb`
    - [x] Produzir relatório do entendimento dos dados e isolar rótulos de falha (`df_labels`)

???+ success "Fase 4: Preparação dos dados"
    - [x] Gerar script de tratamento, codificação ordinal (`Type`) e padronização (`StandardScaler`)
    - [x] Gerar conjunto de dados processado e persistido em `data/processed/ai4i2020_processed.parquet`
    - [x] Produzir relatório de preparação dos dados no notebook `2.0-preparacao.ipynb`

???+ success "Fase 5: Modelagem"
    - [x] Gerar desenho do experimento não supervisionado (PCA 2D + K-Means + DBSCAN)
    - [x] Desenvolver scripts de treinamento e identificação de anomalias no notebook `3.0-modelagem.ipynb`
    - [x] Produzir relatório com os resultados dos agrupamentos e gráficos de dispersão salvos

???+ success "Fase 6: Avaliação"
    - [x] Cruzar clusters com o gabarito real de falhas e comprovar que o Cluster 0 retém 60,47% das quebras
    - [x] Produzir relatório da avaliação dos resultados e validação de 100% dos critérios de aceite no notebook `4.0-avaliacao.ipynb`

???+ success "Fase 7: Implementação"
    - [x] Gerar desenho da implementação e hospedagem estática da documentação viva em MkDocs
    - [x] Desenvolver estrutura de scripts e reprodutibilidade de pipeline via `uv`
    - [x] Produzir relatório e plano de monitoramento e manutenção dos modelos
    - [x] Consolidar relatório final e documentação viva abrangendo todas as fases do CRISP-DM
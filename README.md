# Optimal PitStop (2022-2024)

Este projeto oferece uma análise detalhada do desgaste dos pneus e do desempenho dos pilotos durante as corridas de Fórmula 1 dos anos de 2022, 2023 e 2024. Utiliza a biblioteca `FastF1` para coletar e processar os dados, gerando insights sobre a degradação dos pneus, o impacto do combustível e as condições da pista no tempo de volta.

## Funcionalidades

- **Coleta de Dados Automática**: Baixa dados de corridas específicas usando a API do `FastF1`, incluindo telemetria, dados de carros e informações meteorológicas.
- **Limpeza e Cache**: Gerencia o cache do `FastF1` para otimizar o tempo de carregamento dos dados.
- **Cálculo de Métricas de Desgaste**:
    - `s_lap` (volta_do_stint): Volta atual dentro de um stint.
    - `s_pct` (%_stint): Proporção do stint já rodada (0-1).
    - `delta_best` (delta_para_melhor_volta_stint): Diferença de tempo entre a volta atual e a melhor volta do stint.
    - `delta_var` (variação_volta_anterior): Derivada do delta, indicando queda repentina no desempenho.
    - `fuel_kg` (peso_estimado_combustivel): Estimativa do peso de combustível no carro.
    - `delta_adjusted_fuel` (delta_stint_ajustado_combustivel): `delta_best` ajustado pelo peso do combustível, visando isolar o desgaste do pneu.
    - `avg_delta_best_circuit_tyre` (media_stints_pista_composto): `delta_best` médio para cada tipo de pneu por circuito, como uma expectativa média de stint.
    - `is_stint_fastest_lap`: Indicador se a volta foi a mais rápida do stint.
    - `track_condition`: Condição da pista baseada na temperatura e tipo de pneu.
- **Análise de Distância para o Carro da Frente (`delta_S`)**: Calcula a distância média para o carro da frente por volta, fornecendo insights sobre a influência aerodinâmica e o tráfego enfrentado pelos pilotos.
- **Geração de Dataset Consolidado**: Combina dados de múltiplos anos e eventos em um único arquivo CSV para facilitar a análise.
- **Tratamento de Outliers/Anomalias**: Implementa a detecção de outliers usando métodos estatísticos (Z-score e IQR) e baseados em modelo (Isolation Forest e Local Outlier Factor - LOF). O tratamento inclui a criação de *flags* binárias (ex: `is_outlier_delta_stint`, `driver_error_flag`) ou imputação estratégica (interpolação linear/spline, média móvel/mediana).
- **Refinamento do Peso Estimado de Combustível**: A metodologia de estimativa do peso de combustível por volta é refinada, considerando o consumo médio por volta e a capacidade do tanque (aproximadamente 110 kg), com cálculo iterativo por stint.
- **Visualizações de Desgaste**: Gera gráficos para cada ano analisado, permitindo visualizar:
    - **Tempo de Volta por Stint**: Desempenho do piloto em diferentes stints e tipos de pneu.
    - **Degradação do Pneu**: Como o desempenho do pneu se deteriora ao longo de um stint.
    - **Tempo Médio por Stint**: Comparação do desempenho médio entre stints.
    - **Tempo de Volta vs. Combustível**: O impacto do peso do combustível no tempo de volta.
    - **Tempo de Volta vs. Porcentagem do Stint**: Como o tempo de volta varia conforme o stint avança.
- **Interpretabilidade do Modelo**: Inclui métodos para entender o "porquê" das previsões, como a importância de *features* (global, via modelos substitutos como XGBoost/Random Forest e Permutation Importance) e a interpretabilidade local (SHAP, LIME).

## Saída

Ao final da execução, um arquivo CSV com os dados analisados será salvo na raiz do projeto (ex: `analyzed_fastf1_data_ALL_2022_2023_2024.csv`). Além disso, os gráficos de desgaste por ano e por piloto serão exibidos.

## Futuro do Projeto

Atualmente, o projeto está focado no desenvolvimento do algoritmo de Machine Learning para prever o desgaste dos pneus, com base na previsão do `delta_para_melhor_volta_stint` da próxima volta ou de uma curva de múltiplos `deltas` futuros.

O próximo passo ambicioso é expandir o modelo para criar uma rede neural capaz de prever o melhor momento para um pit stop em uma corrida. Isso envolverá a utilização de modelos como LSTMs, GRUs ou Transformers, que são adequados para capturar a memória de sequências longas e padrões de desgaste ao longo de um stint. O *target* para essa nova funcionalidade será a `probabilidade de pitstop nas próximas N voltas` (classificação binária ou multiclasses) ou a `estimativa de voltas_restantes_utilizáveis` do pneu (regressão).

A interpretabilidade do modelo será fundamental, utilizando técnicas como SHAP e LIME para entender as razões por trás das previsões do pit stop, bem como a visualização de *heatmaps* de atenção em modelos Transformer para identificar os fatores de entrada mais importantes.

## Licença

Este projeto está licenciado sob a licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.

---

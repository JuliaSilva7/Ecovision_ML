# Relatorio de Inteligencia Artificial e Machine Learning — EcoVision V8

## 1. Problema e objetivo
O EcoVision realiza uma classificacao binaria da precipitacao do dia seguinte (D+1). A classe **Baixa** representa acumulado inferior a 10 mm; a classe **Alta** representa acumulado igual ou superior a 10 mm. O objetivo e apoiar o planejamento preventivo e a comunicacao de risco, sem substituir alertas oficiais ou a Defesa Civil.

## 2. Dados e preparacao
- Fonte dos dados: INMET (informar link oficial, nome/codigo da estacao e municipio no README do repositorio).
- Periodo de alvos: 2016 a 2025.
- Amostras de treinamento final: 3247.
- Amostras de teste independente: 364.
- Variaveis preditoras: 47.
- Alvo: precipitacao observada em D+1, convertida em classes pelo limite de 10 mm.
- Valores invalidos/sentinelas sao convertidos em ausentes; imputacao pela mediana ocorre dentro do Pipeline.
- Features temporais, acumulados, medias moveis e diferencas sao calculados com dados do dia D e anteriores.

## 3. Modelo e estrategia de validacao
Foi utilizado Random Forest por ser um ensemble de arvores apropriado para dados tabulares, capaz de capturar relacoes nao lineares e interacoes entre variaveis, sem exigir padronizacao. Foram testadas **12 combinacoes** de tres hiperparametros (n_estimators, max_depth e min_samples_leaf). A selecao ocorreu por GridSearchCV com TimeSeriesSplit de 3 folds, usando somente dados com data_alvo anterior a 2024. O ano de 2024 foi reservado para escolher o threshold; 2025 foi mantido como teste independente.

Melhores hiperparametros: `{"modelo__max_depth": 12, "modelo__min_samples_leaf": 5, "modelo__n_estimators": 200}`. Melhor F1 medio na validacao cruzada: 0.2968. Threshold selecionado em 2024: 0.16.

## 4. Resultados: treinamento x teste
|                         |    n |   accuracy |   balanced_accuracy |   precision_alta |   recall_alta |   f1_alta |   f2_alta |   f1_macro |   brier |   roc_auc |   pr_auc |   verdadeiros_positivos |   falsos_positivos |   falsos_negativos |   verdadeiros_negativos |
|:------------------------|-----:|-----------:|--------------------:|-----------------:|--------------:|----------:|----------:|-----------:|--------:|----------:|---------:|------------------------:|-------------------:|-------------------:|------------------------:|
| Treinamento 2016-2024   | 3247 |     0.6344 |              0.7892 |           0.2668 |        1      |    0.4213 |    0.6454 |     0.577  |  0.0588 |    0.9941 |   0.9609 |                     432 |               1187 |                  0 |                    1628 |
| Teste independente 2025 |  364 |     0.5604 |              0.6685 |           0.2094 |        0.8163 |    0.3333 |    0.5168 |     0.5027 |  0.1198 |    0.7515 |   0.2656 |                      40 |                151 |                  9 |                     164 |

### Diagnostico de overfitting
Diferenca entre treino e teste nao ultrapassa 0,15; avaliar junto das demais metricas. A metrica de treinamento e calculada sobre os mesmos exemplos usados no ajuste (in-sample), portanto tende a ser otimista. A avaliacao mais representativa da generalizacao e o teste temporal de 2025.

### Matriz de confusao — teste 2025
`[[VN, FP], [FN, VP]] = [[164, 151], [9, 40]]`.

## 5. Interpretacao para o cidadao
O modelo tenta sinalizar com antecedencia de um dia a possibilidade de acumulado de chuva de pelo menos 10 mm. Se apresentar recall baixo para a classe Alta, podera deixar de sinalizar dias chuvosos; se a precisao for baixa, podera gerar alertas desnecessarios. Por isso, a escolha entre reduzir falsos negativos e evitar falsos positivos deve considerar o uso publico pretendido. O resultado e experimental e depende da cobertura e qualidade da estacao meteorologica utilizada. Nao deve ser tratado como previsao oficial, alerta de inundacao ou orientacao de emergencia.

## 6. Limitacoes e proximos passos
A avaliacao usa dados de uma base/estacao especifica e nao garante desempenho em outros municipios. E necessario registrar no repositorio a identificacao da estacao, localidade, URL de origem, data de coleta e eventuais lacunas. Proximos passos incluem testar outras estacoes, avaliar calibracao das probabilidades e validar o modelo em periodos futuros.

## 7. Arquivos gerados
- `resultados_gridsearch_v8.csv`: resultados das combinacoes e folds.
- `metricas_treino_vs_teste_v8.csv` e `.png`: comparacao de metricas.
- `matriz_confusao_2025_v8.png`: erros e acertos no teste.
- `importancia_features_v8.csv` e `.png`: importancia das variaveis.
- `previsoes_2025_v8.csv`: previsoes individuais do teste.
- `modelo_ecovision_v8.joblib`: modelo final treinado.

> Antes da entrega, complete no README a URL oficial do conjunto INMET, codigo/nome da estacao, municipio/UF, periodo efetivamente disponivel e quantidade de registros validos. Nao invente esses metadados.

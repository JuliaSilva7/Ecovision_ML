# 🌧️ EcoVision — Inteligência Artificial e Machine Learning

### Classificação de precipitação no dia seguinte (D+1) em São Paulo — 2016 a 2025

## 1. Sobre o projeto

O **EcoVision** é um projeto de Inteligência Artificial e Machine Learning desenvolvido para analisar dados meteorológicos históricos e classificar a ocorrência de chuva significativa no dia seguinte.

A solução utiliza dados reais do Instituto Nacional de Meteorologia (INMET), provenientes da estação automática A701 — Mirante, localizada em São Paulo/SP.

Por meio do algoritmo **Random Forest Classifier**, o modelo identifica padrões meteorológicos e estima se a precipitação acumulada no dia seguinte será inferior ou igual/superior ao limiar estabelecido.

**Objetivo:** desenvolver um classificador supervisionado capaz de apoiar a análise e o monitoramento de condições de precipitação.

> **Limitação:** o EcoVision classifica a precipitação, mas não prevê diretamente alagamentos ou enchentes. Para isso, seriam necessários dados adicionais de drenagem urbana, topografia, hidrografia e ocorrências georreferenciadas.

## 2. Fonte e dados utilizados

- **Fonte:** Instituto Nacional de Meteorologia (INMET).
- **Estação:** A701 — Mirante, São Paulo/SP.
- **Período:** 2016 a 2025.
- **Dados originais:** registros meteorológicos horários em arquivos CSV.
- **Base processada:** dados consolidados em frequência diária.

Os arquivos originais são utilizados para construir a base meteorológica diária que alimenta o modelo.

## 3. Definição do problema

O projeto utiliza classificação binária para identificar duas categorias:

| Classe | Critério |
|---|---|
| 0 — Baixa | Precipitação D+1 inferior a 10 mm |
| 1 — Chuva significativa | Precipitação D+1 igual ou superior a 10 mm |

A variável-alvo representa a precipitação acumulada no dia seguinte, considerando o dia atual como referência para a previsão.

O limiar de 10 mm é um critério metodológico definido para este estudo, não correspondendo necessariamente a um alerta oficial.

## 4. Funcionamento do modelo

O EcoVision utiliza o algoritmo **Random Forest Classifier**, que combina diversas árvores de decisão para realizar a classificação.

A versão utiliza 47 variáveis preditoras, organizadas em:

| Grupo de variáveis | Quantidade |
|---|---:|
| Variáveis meteorológicas | 15 |
| Variáveis temporais | 6 |
| Acumulados de chuva | 3 |
| Médias móveis | 8 |
| Variações de 1 dia | 15 |
| **Total** | **47** |

### Etapas da metodologia

1. **Leitura dos dados:** carregamento dos arquivos históricos do INMET.
2. **Processamento:** tratamento e consolidação dos registros horários em uma base diária.
3. **Engenharia de atributos:** criação das variáveis meteorológicas e temporais.
4. **Treinamento:** aplicação do Random Forest e busca de hiperparâmetros.
5. **Seleção do threshold:** definição do limiar de decisão utilizando os dados de validação.
6. **Avaliação:** análise do desempenho do classificador em dados posteriores ao período de treinamento.

### Estratégia de validação temporal

| Período | Finalidade |
|---|---|
| 2016–2023 | Busca de hiperparâmetros |
| 2024 | Seleção do threshold |
| 2025 | Avaliação temporal de referência |

A validação respeita a ordem cronológica dos dados, evitando utilizar informações futuras no treinamento.

**Cuidados metodológicos:**
- A separação dos conjuntos considera `data_alvo`.
- A variável `classe_alvo` não é utilizada como feature.
- As variáveis preditoras não devem utilizar informações do dia seguinte.
- A base original é preservada durante o processamento.

## 5. Tecnologias utilizadas

- Python
- Pandas
- NumPy
- Scikit-learn
- Matplotlib
- Seaborn
- Joblib
- Jupyter Notebook

## 6. Estrutura do projeto

A organização esperada dos arquivos é:

```text
EcoVision_GitHub/
│
├── dados/
│   ├── inmet/
│   └── README.md
│
├── bases_processadas/
│   └── saida_ecovision/
│       └── base_diaria_ecovision.csv
│
├── resultados/
│
├── ml/
│   └── ecovision_model.ipynb
│
├── requirements.txt
├── .gitignore
└── README.md
```

A estrutura pode variar conforme a organização dos arquivos disponibilizados no repositório.

## 7. Como executar o projeto

### Pré-requisitos

- Python instalado.
- Visual Studio Code.
- Extensões Python e Jupyter instaladas no VS Code.
- Arquivos históricos do INMET necessários para o processamento.

### 7.1. Clonar o repositório

```bash
git clone https://github.com/JuliaSilva7/Ecovision_ML.git
```

Entre na pasta:

```bash
cd Ecovision_ML
```

### 7.2. Criar o ambiente virtual

No terminal do VS Code:

```bash
python -m venv .venv
```

Ative o ambiente no Windows:

```powershell
.venv\Scripts\activate
```

### 7.3. Instalar as dependências

```bash
python -m pip install --upgrade pip
```

```bash
pip install -r requirements.txt
```

### 7.4. Preparar os dados

Disponibilize os arquivos CSV anuais do INMET na pasta:

```text
dados/inmet/
```

A execução do processamento deve gerar a base diária esperada pelo notebook:

```text
bases_processadas/saida_ecovision/base_diaria_ecovision.csv
```

Se a base processada já estiver disponível, confira se o caminho corresponde ao utilizado no código.

### 7.5. Executar o notebook

1. Abra o arquivo `ecovision_model.ipynb` no VS Code.
2. Clique em **Select Kernel**.
3. Selecione o interpretador Python do ambiente `.venv`.
4. Execute as células sequencialmente ou utilize **Run All**.

## 8. Avaliação dos resultados

O desempenho do modelo deve ser analisado por meio das métricas de classificação, incluindo:

- Acurácia (*Accuracy*).
- Precisão (*Precision*).
- Revocação (*Recall*).
- F1-score.
- Matriz de confusão.

Também devem ser observados o desempenho por classe, a capacidade de identificação de chuva significativa e possíveis sinais de overfitting.

## 9. Considerações finais

O EcoVision demonstra a aplicação de técnicas de Machine Learning na análise de dados meteorológicos históricos, utilizando validação temporal e engenharia de atributos para classificar a precipitação do dia seguinte.

O projeto também evidencia desafios importantes da previsão meteorológica, especialmente o desequilíbrio entre classes e a dificuldade de identificar eventos de chuva significativa.

Os resultados devem ser interpretados considerando as limitações dos dados, da estação utilizada e do próprio modelo.

## 10. Autoria

Projeto acadêmico desenvolvido no curso de Engenharia de Software da FIAP.

**GitHub:** [JuliaSilva7](https://github.com/JuliaSilva7)

**Repositório:** [EcoVision_ML](https://github.com/JuliaSilva7/Ecovision_ML)

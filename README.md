# Geração da Base Reduzida – Projeto PAR 2025

Este documento descreve o processo utilizado para **montagem, limpeza, alinhamento e preparação** da base reduzida utilizada nos experimentos do projeto PAR 2025. Toda a execução foi realizada em Google Colab, com integração ao Google Drive.

---

## 1. Download da Base Original

A primeira etapa consiste em baixar os arquivos disponibilizados por um dos integrantes do grupo via Google Drive.

São baixados:

* O arquivo ZIP com as imagens da base de treino (`training_set.zip`)
* O arquivo `.txt` contendo as anotações (`training_set.txt`)

O download foi realizado com `gdown` apontando para os IDs dos arquivos no Drive.

---

## 2. Extração do ZIP

Após o download, o arquivo ZIP é extraído para um diretório específico dentro do `/content`, permitindo acesso às imagens originais.

Essa extração é feita em duas etapas:

1. Extração inicial para `training_set/`
2. Extração organizada para `training_set_extracted/`, utilizada como base final

---

## 3. Carregamento e Construção do DataFrame de Labels

O arquivo `training_set.txt` contém as colunas:

* `filename`
* `top_color`
* `bottom_color`
* `gender`
* `hat`
* `bag`

Ele é lido e associado a caminhos completos de arquivo, permitindo verificação e processamento das imagens.

---

## 4. Correção de Imagens Problemáticas

Foram identificadas imagens com canais incorretos ou formato inconsistente. Essas imagens foram corrigidas manualmente utilizando Pillow, garantindo que todas passem a estar em RGB.

As imagens corrigidas foram sobrescritas diretamente no dataset.

---

## 5. Corte da Base até a Última Imagem Válida

Para evitar divergências entre quantidade de imagens e linhas do CSV, o DataFrame final é cortado até a **última imagem existente na pasta**.

O resultado é salvo como:

* `training_set_final.txt`

Este arquivo é a versão oficial utilizada para formar a base reduzida.

---

## Balanceamento da Base com Augmentation

### 6. Identificação de Classes Minoritárias
- As colunas `top_color` e `bottom_color` foram analisadas.
- Foram calculados percentis baixos para identificar:
  - **Classes extremamente baixas**
  - **Classes baixas**
- Apenas essas classes receberiam augmentation.

### 7. Definição dos Fatores de Augmentation
- Classes extremamente baixas: até **16×**
- Classes baixas: até **10×**
- Fatores calculados de forma conservadora para evitar oversampling pesado.

### 8. Técnicas de Augmentation Utilizadas
- Flip horizontal  
- Gaussian Noise  
- ISO Noise  
- Motion Blur  
- Combinação (Blur + Noise)

Cada técnica gera novas imagens artificiais nomeadas automaticamente.

### 9. Geração da Nova Base Balanceada
- Para cada imagem minoritária, diversas versões são geradas.
- As entradas correspondentes são adicionadas a um novo dataframe.

### 10. Visualização das Distribuições
Foram gerados gráficos de barras para:

- `top_color`
- `bottom_color`
- `gender`
- `hat`
- `bag`



# Projeto PAR 2025 – Ensemble de Modelos (Colab)

Este repositório apresenta a implementação de uma prova de conceito para o desafio PAR 2025, realizada em Google Colab. O objetivo principal do trabalho foi testar a eficácia de um ensemble composto por modelos distintos, avaliando métricas e explorando caminhos possíveis para trabalhos futuros, mesmo sob fortes limitações de hardware e tempo.

---

## Objetivo do Projeto

Devido ao tamanho da base original (94.293 imagens) e restrições de processamento, realizamos:

* Redução da base de treino, priorizando classes menos representadas.
* Uso da base completa original de validação para testes finais.
* Criação e avaliação de modelos independentes + ensemble.

---

## Estrutura Geral do Colab

As células do notebook foram organizadas de forma a facilitar a execução sequencial do projeto. A divisão geral é:

### 1. **Carregamento da Base (Google Drive)**

Antes de rodar qualquer modelo, é necessário executar a célula que monta o Google Drive e carrega a base reduzida para o diretório `/content`.

### 2. **Treinamento dos Modelos**

Cada modelo possui seu próprio bloco de código, incluindo:

* Importação das bibliotecas necessárias.
* Pré-processamento e data loaders.
* Treinamento + salvamento de pesos.

Os modelos utilizados foram:

* **MobileNetV2** (TensorFlow/PyTorch) - 2 versões
* **EfficientNetB0** (TensorFlow/PyTorch) - 2 versões
* **YOLO** (PyTorch/Ultralytics) - 1 modelo separado por tarefa (task-specific)

Os arquivos gerados:

* `.keras` e `.h5` para modelos Keras
* `.pt` para modelos PyTorch

Recomenda-se o uso de GPU no Colab.

---

## Validação

A etapa de validação é dividida em duas partes:

### 1. **Carregar Base de Validação**

Assim como na base de treino, a validação é baixada do Google Drive de um integrante do grupo.

### 2. **Validação Individual**

Há células específicas para carregar cada modelo já treinado e rodar suas métricas separadamente.

### 3. **Validação por Ensemble**

O ensemble recebe todos os `.pt` (modelos com Pytorch) e combina suas saídas para gerar previsões finais.

O ensemble está localizado na seção de

Validação, enquanto as análises individuais estão separadas em Validação Individual.

---

##Fluxo de Execução do Colab

Montar o Google Drive.

Baixar a base reduzida de treino para o diretório /content.

Visualizar a base e conferir organização das pastas.

Executar o bloco da MobileNetV2 (importações, preparação e treino).

Executar o bloco dos modelos YOLO (um bloco por tarefa).

Executar o bloco da EfficientNetB0.

Baixar a base de validação original.

Rodar a validação do ensemble.

Rodar validações individuais dos modelos.

---

## Considerações Finais

Dado o limite de tempo e hardware, o foco foi demonstrar:

* A viabilidade técnica da montagem de um ensemble
* O comportamento de modelos distintos sobre a mesma base
* Um caminho inicial para pesquisa e melhorias futuras

Mesmo com resultados modestos, o trabalho evidencia que com mais tempo de treinamento e infraestrutura seria possível alcançar métricas superiores.

---

## Autores

Arthur Przygocki

Carlos Eduardo Rodrigues Mello

Gabriel Yuichi Suzaki

Henrique Tetilha Golias

Maria Julia Prado Lazaroto

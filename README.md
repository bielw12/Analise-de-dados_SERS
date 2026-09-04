# Análise de Dados — SERS

Integrantes:

•	Gabriel Pedro – RM:571995

•	Sammy Sato – RM:569182

•	João Vitor Belchior – RM:572478

•	Thiago Coelho – RM:568783

•	João Pedro Teixeira – RM:569937


Atividades de análise de dados da disciplina **Soluções em Energias Renováveis e Sustentáveis (SERS)** — Ciência da Computação.

O fio condutor de todos os notebooks é o mesmo: partir de uma base de consumo ou geração de energia, tratar os atributos, localizar os **períodos de alta demanda / alta geração** e depois cruzar esse recorte com uma **segunda condição** (ambiental, elétrica ou temporal), comparando os dois conjuntos resultantes.

## Estrutura

| Pasta | Base | Tema |
|---|---|---|
| raiz | API pública do ONS | Carga elétrica verificada de São Paulo (01–07/08/2025) |
| `Dataset 1/` | `EX01_AMOSTRA.csv` | Consumo de eletrodomésticos vs. temperatura e umidade |
| `Dataset 2/` | `steel industry energy.csv` | Consumo de uma indústria siderúrgica e fator de potência (UCI) |
| `Dataset 3/` | `EX03_AMOSTRA_2.csv` | Consumo por zona da cidade de Tétouan (UCI) |
| `Dataset 4/` | `DataSet-SolarPower-CP03.csv` | Geração de usina fotovoltaica por inversor |
| `Dataset 5/` | `energy_production_filter.csv` | Produção eólica vs. solar por estação do ano (Kaggle) |
| `Dataset 6/` | `house-power_TRATADO_CP4SERS.csv` | Consumo elétrico residencial (potência ativa e corrente) |

Cada pasta contém o notebook da análise e o CSV correspondente.

## Os notebooks

### `CP4_AnaliseDados_API.ipynb` (raiz) — Desafio Final
Único notebook que consome dados ao vivo, via **API pública de Carga Verificada do ONS** ([dados.ons.org.br](https://dados.ons.org.br/dataset/carga-energia-verificada)). Percorre 5 desafios: construção e inspeção do DataFrame a partir do JSON, organização e renomeação dos atributos, indicadores da carga (mínimo, máximo, média, mediana, amplitude), recorte dos períodos acima de 90% da carga máxima e um segundo critério definido pela equipe. Inclui gráficos e as respostas interpretativas de cada etapa.

### `Dataset 1` — Eletrodomésticos
Consumo máximo, limiar de 70% do máximo e cruzamento com temperatura da cozinha acima da média. Compara o recorte de critério único com o de critério duplo.

### `Dataset 2` — Indústria siderúrgica
Limiar de 75% do consumo máximo, checagem de quantos desses registros são `Maximum_Load` e cruzamento com **fator de potência baixo**. O limite de "FP baixo" é definido dentro do subconjunto de alta carga, não no dataset inteiro — nos picos o FP já é naturalmente alto, então o percentil global não serviria.

### `Dataset 3` — Tétouan
Identifica qual das três zonas tem o maior pico de consumo, aplica o limiar de 70% sobre ela e adiciona a condição de temperatura acima da média. A queda de 2.388 → 1.597 registros é analisada como relação de subconjunto.

### `Dataset 4` — Usina fotovoltaica
Limiar de 70% da potência CA máxima e `value_counts()` sobre `SOURCE_KEY` para ver quais inversores aparecem com mais frequência nos períodos de alta geração — sem concluir desempenho ou falha só com esse recorte.

### `Dataset 5` — Eólica vs. solar
Compara com que frequência cada fonte opera acima de 70% do **próprio** máximo. A eólica aparece em 245 registros contra 45 da solar; o percentual é calculado de duas formas porque a base de comparação muda bastante o resultado.

### `Dataset 6` — Residencial
Potência ativa máxima, limite de 75% e segundo filtro por corrente acima da média. Caso em que a segunda condição **não altera o resultado** (os mesmos 58 registros) — explicado pela relação física entre potência ativa e corrente sob tensão aproximadamente constante.

## Ferramentas

- **Python** com **pandas** (análise em todos os notebooks)
- **matplotlib** e **seaborn** (gráficos, no notebook da API)
- **requests** (consulta à API do ONS)
- **Orange Data Mining** — usado na *Etapa A* dos Datasets 2, 3 e 5 para filtragem e amostragem de 20%; os CSVs neste repositório são o resultado exportado de lá

## Como executar

```bash
pip install pandas matplotlib seaborn requests jupyter
jupyter notebook
```

Abra o notebook desejado e execute as células na ordem. Os caminhos dos CSVs são relativos à pasta de cada notebook — rode cada um a partir do seu próprio diretório.

# Detecção de Incêndios Florestais no Brasil (2025) — Análise Estatística

**Disciplina:** Data Science — *Estatística Descritiva aplicada à Economia Espacial*
**FIAP — Global Solution 2026 | Tema macro: Economia Espacial**

Análise estatística descritiva dos **focos de calor** detectados por satélite no Brasil em 2025
(dados reais do **INPE / BDQueimadas**), aplicando estatística descritiva, análise de
distribuição, detecção de outliers, visualizações e perguntas de negócio.

---

## Grupo Stratfy — Turma 2ESPH (Engenharia de Software)

| Integrante | RM |
|---|---|
| Anthony Sforzin | RM562096 |
| Luigi Mendes Cabrini | RM563552 |
| Rogério Cruz Arroyo | RM563517 |
| Bruno Koeke | RM561309 |

---

## O problema e a relação com a Economia Espacial

Em 2025, o INPE registrou **3.466.399 focos de calor** no Brasil. Detectar incêndios em escala
continental só é possível graças à **infraestrutura espacial**: uma rede de **13 satélites**
(NOAA-20/21, GOES-16/19, NPP-375, METOP-B/C, MSG-03, TERRA e a série AQUA) cujos sensores no
infravermelho termal identificam anomalias de temperatura na superfície. O **AQUA_M-T** é o
satélite de **referência** do INPE para séries temporais comparáveis.

A **Economia Espacial** entra como habilitadora de uma cadeia de valor: dados orbitais →
produtos de informação (focos, risco de fogo, FRP) → serviços de decisão (alerta precoce,
alocação de brigadas, fiscalização). É um caso concreto de ativos espaciais gerando valor
econômico e social mensurável.

---

## Dados

- **Arquivo:** `dados/focos_amostra.csv.gz` — amostra aleatória (`seed=42`) de **201.284 focos**
  reais, já limpa (sentinela `-999` do INPE convertido em ausente).
- **Fonte:** INPE — BDQueimadas (arquivo completo de 3,46 milhões de focos de 2025).
- **Colunas:** `latitude, longitude, data_pas, satelite, estado, municipio, bioma,`
  `numero_dias_sem_chuva, precipitacao_mm, risco_fogo, frp_mw`.

O notebook lê os dados de forma **robusta**: tenta o caminho local `dados/focos_amostra.csv.gz`
(em variações de diretório) e, se nenhum existir (ex.: Google Colab), baixa de uma **URL RAW do
GitHub**. O `pandas` descompacta o `.gz` de forma transparente.

---

## Estrutura do repositório

```
data-science/
├── notebooks/
│   └── analise_incendios_florestais.ipynb   <- ENTREGÁVEL PRINCIPAL (executa de ponta a ponta)
├── dados/
│   └── focos_amostra.csv.gz                  <- 201.284 focos reais (amostra limpa)
├── requirements.txt
└── README.md
```

---

## Como abrir e executar

### Opção A — Google Colab (recomendado)

1. Abra o notebook no Colab: **[link do Colab — adicionar após o push]**
   `https://colab.research.google.com/github/Stratfy/gs2026-data-science-incendios-florestais/blob/main/notebooks/analise_incendios_florestais.ipynb`
2. Menu **Ambiente de execução → Executar tudo**. A célula de carga baixa os dados
   automaticamente da URL RAW do GitHub (o link passa a valer após o push do repositório).

### Opção B — Localmente (Jupyter)

```bash
pip install -r requirements.txt
jupyter notebook notebooks/analise_incendios_florestais.ipynb
# Execute todas as células (Run All).
```

### Opção C — Execução headless (validação automática)

```bash
jupyter nbconvert --to notebook --execute --inplace \
  notebooks/analise_incendios_florestais.ipynb
```

> Testado com **pandas 3.0.3**, **numpy 2.4.6**, **matplotlib 3.10.9**, **scipy** e
> **Python 3.13** — executa de ponta a ponta **sem erros** (30 células de código, 6 figuras inline).

---

## Conteúdo do notebook (na ordem do enunciado)

1. **Contextualização** — problema, relevância, relação com a Economia Espacial, objetivo.
2. **Preparação dos dados** — leitura robusta, inspeção (`shape`/`info`/`head`), tipos,
   tratamento de ausentes, remoção de duplicidades, padronização de colunas, conversão de
   `data_pas` para `datetime` e criação da coluna `mes`.
3. **Estatística descritiva** — tendência central (média, mediana, moda); dispersão (amplitude,
   variância, desvio padrão, coeficiente de variação); posicionais (Q1, Q2, Q3, percentis);
   **outliers por IQR e Z-Score**.
4. **Análise de distribuição** — assimetria (skewness), curtose, concentração e anomalias.
5. **Cinco visualizações** — histograma (risco de fogo), boxplot (FRP por bioma), barras
   (focos por bioma/estado), scatterplot (risco × dias sem chuva), heatmap de correlação.
6. **Sete perguntas de negócio** — respondidas com código e evidência estatística.
7. **Conclusão executiva** — achados, insights, aplicações práticas e limitações.

---

## Principais achados

- **Concentração espacial:** o **Cerrado** lidera (≈51% dos focos da amostra), seguido da
  **Amazônia**; os estados de **MATOPIBA** (Maranhão, Tocantins, Piauí, Bahia) + **Mato Grosso**
  e **Pará** formam o núcleo crítico (arco do desmatamento).
- **Sazonalidade marcada:** pico inequívoco na **estação seca**, com máximo em **setembro**
  (seguido de outubro e agosto).
- **Seca prediz risco:** `numero_dias_sem_chuva` correlaciona-se positivamente com `risco_fogo`
  (r ≈ 0,38), com risco médio crescente por faixa de seca; a precipitação atua no sentido oposto.
- **Intensidade em cauda longa:** a **FRP** é fortemente assimétrica à direita e leptocúrtica —
  poucos **grandes incêndios** concentram a energia liberada (FRP máximo > 3.000 MW).
- **Variabilidade relativa:** `precipitacao_mm` e `frp_mw` têm os maiores coeficientes de
  variação; `risco_fogo` é a variável mais estável (concentrada em 1,0).

### Aplicações práticas

- **Órgãos ambientais (IBAMA/ICMBio):** priorizar Cerrado/Amazônia e estados do MATOPIBA,
  antecipando-se ao trimestre ago–out.
- **Defesa Civil e bombeiros:** usar **dias sem chuva** e **FRP** como gatilhos de alerta e de
  priorização — pré-posicionar brigadas antes do pico de setembro.

### Limitações

Amostra (~200 mil de 3,46 mi de focos — ler contagens como proporções); correlações **não
implicam causalidade**; valores ausentes tratados sem imputação; recorte de apenas 2025.

---

*Notebook desenvolvido pelo grupo **Stratfy** para a FIAP Global Solution 2026 — Economia Espacial.*

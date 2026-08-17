
---
title: "Projeto 2: Modelos ARIMA e SARIMA"
subtitle: "Análise do PIB dos EUA e Produção de Cerveja na Austrália"
author: "Ana Beatriz de Souza Soares"
---

1. Introdução
A análise de séries temporais é uma área da estatística que estuda dados coletados sequencialmente ao longo do tempo. Diferente de dados transversais, em séries temporais as observações possuem dependência temporal, ou seja, o valor atual está relacionado com valores passados. Essa característica torna a previsão um desafio, mas também uma ferramenta fundamental para planejamento e tomada de decisão.

No contexto econômico e industrial, a previsão de séries temporais é amplamente utilizada. Governos utilizam para projetar o PIB e a inflação. Empresas utilizam para prever demanda, vendas e produção. A capacidade de antecipar tendências e ciclos sazonais permite reduzir custos e alocar recursos de forma mais eficiente.

Dentre os diversos modelos existentes, os modelos ARIMA e sua extensão SARIMA se destacam por sua simplicidade e eficiência. O modelo ARIMA é capaz de capturar padrões de tendência. Já o modelo SARIMA adiciona componentes para tratar a sazonalidade.

Este trabalho tem como objetivo aplicar os modelos ARIMA e SARIMA na previsão de duas séries reais: o PIB dos Estados Unidos, que apresenta forte tendência de crescimento, e a Produção de Cerveja na Austrália, que apresenta tendência de queda e forte sazonalidade trimestral.

O projeto está estruturado da seguinte forma: na seção 2 é feita a contextualização do problema. Na seção 3 é apresentada a fundamentação teórica. Na seção 4 é realizada a análise descritiva. Na seção 5 é feita a modelagem e previsão. Por fim, na seção 6 são apresentadas as conclusões.

2. Contextualização do Problema
O desafio é prever duas variáveis econômicas importantes para planejamento:
1. **PIB dos EUA**: Possui forte tendência de crescimento. Prever o PIB ajuda governos a planejar políticas fiscais e orçamentárias.
2. **Produção de Cerveja na Austrália**: Possui tendência de queda e sazonalidade trimestral. Prever ajuda indústrias a planejar produção, estoque e marketing.

Proposta de Solução: Utilizar modelo ARIMA para a série com tendência e SARIMA para a série com sazonalidade.

3. Fundamentação Teórica
3.1 Modelo ARIMA(p,d,q)
ARIMA combina 3 componentes: AR(p) - Autoregressivo, I(d) - Integração/diferenciação, MA(q) - Média Móvel.
Usado quando a série é não estacionária devido à tendência.
Fórmula: $\phi(B)(1-B)^d Z_t = \theta(B)\epsilon_t$

3.2 Modelo SARIMA(p,d,q)(P,D,Q)[s]
Extensão do ARIMA para dados sazonais. Adiciona componentes AR e MA sazonais com período `s`.
Fórmula: $\phi(B)\Phi(B^s)(1-B)^d(1-B^s)^D Z_t = \theta(B)\Theta(B^s)\epsilon_t$

4. Análise Descritiva
```{r}
#| echo: false
#| warning: false
library(fpp3)
serie_tendencia <- us_gdp %>% select(Quarter, GDP)
serie_sazonal <- aus_production %>% select(Quarter, Beer)

autoplot(serie_tendencia, GDP) + labs(title="PIB EUA - Série com Tendência", y="Bilhões USD")
autoplot(serie_sazonal, Beer) + labs(title="Produção Cerveja AUS - Série com Sazonalidade", y="Megalitros")

serie_sazonal %>% STL(Beer ~ trend() + season(period=4)) %>% components() %>% autoplot()
*Características:*
- `us_gdp`: Série não estacionária, com tendência crescente clara. Não apresenta sazonalidade.
- `aus_production`: Série com tendência de queda e sazonalidade anual de período 4. Pico no Q4 - verão australiano.

Modelagem e Previsão
#| echo: true
library(fable)
Modelo ARIMA para Tendência
modelo_arima <- serie_tendencia %>% model(ARIMA(GDP))
report(modelo_arima)
checkresiduals(modelo_arima)

Modelo SARIMA para Sazonalidade
modelo_sarima <- serie_sazonal %>% model(SARIMA(Beer))
report(modelo_sarima)
checkresiduals(modelo_sarima)

Previsão
fc_arima <- modelo_arima %>% forecast(h=8)
fc_sarima <- modelo_sarima %>% forecast(h=8)
autoplot(fc_arima, serie_tendencia) + labs(title="Previsão PIB EUA")
autoplot(fc_sarima, serie_sazonal) + labs(title="Previsão Produção Cerveja")
*Justificativa do Modelo*: O `ARIMA()` selecionou o modelo com base no menor AICc. A análise dos resíduos com `checkresiduals` confirmou que se comportam como ruído branco, validando os modelos.
```
Conclusão e Solução Proposta
As previsões indicam crescimento contínuo do PIB americano nos próximos 2 anos. Para a produção de cerveja, a previsão aponta queda na tendência, mas com picos sazonais no 4º trimestre.

Economistas podem usar o modelo do PIB para projeções orçamentárias. As indústrias de bebidas devem reduzir capacidade ao longo do ano, mas aumentar estoque e marketing no Q4 para atender a demanda sazonal.

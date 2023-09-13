# Hackdays - Hotel Chain Cancellation Rating

Este projeto foi resultado da 4ª edição do Hackdays da Comunidade DS onde liderei a equipe, Only Outliers, campeã da competição.

A competição que tinha como objetivo desenvolver um modelo de classificação para prever o cancelamento de reservas em uma rede hoteleira e gerar insights de negócio para o time de marketing.

A competição foi dividida em duas etapas: na primeira, que ocorreu nos dias 25 e 26/03 e era eliminatória, desenvolvemos o modelo maximizando o f1-score e conseguimos nos classificar entre as três melhores equipes. Na segunda etapa, que ocorreu no dia 29/03 e era classificatória, apresentamos nossa solução e fomos avaliados em critérios como organização do código, insights acionáveis e storytelling.

Abaixo descreverei o problema de negócio e os principais passos da solução. Vale ressaltar que muito outros ciclos foram realizados antes da solução que apresentaremos aqui.

# 1. Problema de Negócio

A rede hoteleira espanhola Costa del Data, está com uma alta demanda de cancelamentos de reservas.

A suspeita da diretoria é de que houve uma mudança no comportamento de cancelamentos por parte do consumidor após a pandemia, que ainda não foi compreendida pela rede.

Para compreender esse fenômeno, nós "Only Outliers" fomos contratamos para desenvolver um modelo de previsão de cancelamentos afim de auxiliar o time de marketing tomar decisões mais assertivas.

## 1.1. Questão de Negócio

- Entre os clientes que fizeram reservas, quais irão cancelar?

- Relatório com melhores insights de negócio.

## 1.2. Tópicos mais Relevantes

- A importância da EDA durante a produção projeto.

- Insights de negócio.

- Utilização de Pipelines para organizar a solução.

- Avaliação do modelo.

- Os cincos melhores modelos para gerar o modelo final.

- Próximos passos.

# 2. Coleta/Carregamento dos Dados

Os dados foram disponibilizados por meio de três csv's, portanto não foi necessário coletar os dados, apenas carregá-los. Abaixo estão os atributos dos dados que utilizamos:

|Nome                                  | Tipo     |
|--------------------------------------|----------|
|id | integer |
|Classificação do hotel | string |
|Meses da reserva até o check-in| integer |   
|Número de pernoites reservadas| integer |
|Número de hospedes| float |
|Regime de alimentação| string |
|Nacionalidade| string |
|Forma de Reserva| string |
|Já se hospedou anterioremente| string |
|Tipo do quarto reservado| string |
|Reserva feita por agência de turismo| string |
|Reserva feita por empresa| string |
|Reserva com Estacionamento| string |
|Reserva com Observações|string |
|Reserva Cancelada| integer |

## 2.1 Dimensões dos dados

- Linhas: 72159.
- Colunas: 15.

## 2.2 Checagem de Valores Nulos

Haviam 3 linhas sem o número de hospédes e 1093 linhas sem a nacionalidade.

# 3. Análise Exploratória dos Dados

Nessa etapa utilizamos a biblioteca SweetViz para gerar um relatório que nos guiou para algumas tomadas de decisão e insights.

As principais conclusões foram:

- ID está altamente correlacionado com: Classificação do Hotel, Regime de Alimentação, Reserva Cancelada, Tipo de Quarto Reservado.
- Outliers nas variáveis: Meses da reserva até check-in, Número de pernoites reservadas, Número de hóspedes
- Nacionalidade: 1,5% de dados Nulos e 48% de Espanhois.
- Algumas colunas estão desbalanceadas: Reserva com estacionamento, Reserva feita por empresa, Reserva feita por agência de turismo.
- 'Forma de Reserva = Agência' não indica que 'Reserva feita por agência de turismo = Sim'
- 'Forma de Reserva = B2B' não indica que 'Reserva feita por empresa = Sim'

Este relatório também nos motivou a pensar em algumas hipóteses de negócio.

## 3.1. Hipóteses de Negócio

### 3.1.1. H1: Quanto maior os meses de reserva até o check-in, maior é a chance da reserva ser cancelada.

![image](https://github.com/jonasbarletta/hackdays_hotel_cancellation/assets/102927918/150f83c2-f9fb-4e9f-889b-edb9ea5db98b)

O primeiro gráfico nos informa a quantidade de cancelamentos para cada intervalo de meses. Já o segungo representa a porcentagem de cancelados no intervalo de meses.

**Conclusão**: Verdadeira

**Sugestão**: 
- Limitar a quantidade de meses que se pode fazer a reserva antecipada.

### 3.1.2. H2: Há mais cancelamentos entre clientes que não contratam estacionamento.

![image](https://github.com/jonasbarletta/hackdays_hotel_cancellation/assets/102927918/bfb1ecc5-eb75-487b-9783-db07f1147c24)

O primeiro gráfico representa a 'Quantidade de Cancelamentos' x 'Reserva com Estacionamento'. Já o segundo representa a 'Porcentagem de Reservas Canceladas' x 'Reserva com Estacionamento'. 

**Conclusão**: Verdadeira.

**Sugestões**: 
- Oferecer estacionamento.
- Parceria com estacionamentos próximos.

### 3.1.3. H3: Quanto mais pernoites reservadas, menor a chance de cancelamento.

![image](https://github.com/jonasbarletta/hackdays_hotel_cancellation/assets/102927918/88ff853f-a384-49f9-b3a1-0d1bcac2ab43)

O primeiro gráfico representa a 'Quantidade de Cancelamentos' x 'Intervalo de Pernoites Reservadas'. Já o segundo representa a 'Porcentagem de Reservas Canceladas' x 'Intervalo de Pernoites Reservadas'. 

**Conclusão**: Falsa

**Sugestão**:
- Não permitir cancelamento gratuito para número de pernoites maiores que 14 dias.

# 4. Modelagem dos Dados

## 4.1. Preparamento dos Dados

Nessa definimos dois Pipelines: um para as variáveis numéricas e outra para as categóricas.

No Pipeline das variáveis númericas utilizamos as seguintes transformações: troca dos valores nulos pela mediana, RobustScaler e StandarScaler (fizemos diversas tentativas e mesmo que possa não fazer sentido a utilização de dois 'scalers' essa configuração nos deu o melhor resultado)

No Pipeline das variáveis categóricas utilizamos: troca dos valores nulos pelo mais frequente e One Hot Encoder.

## 4.2. Seleção dos Atributos

Optamos por uitlizar todos atributos, inclusive o ID. Na maioria dos problema o ID, pela natureza da sua construção, não é utilizado como atributo nos modelos de ML, porém nessa competição o ID fez MUITA diferença no resultado final, por esse motivo optamos por mante-lo. Para os próximos ciclo pretendemos conversar com o time de Engenharia de Dados para entendermos como esse ID é gerado em busca de respostas para esse dilema.

## 4.3. Modelo de Machine Learning

Após diversas tentativas o XGBoost se mostrou melhor e optamos por trabalhar com ele.

## 4.4. Otimização dos Hiperparâmetros

Utilizamos a técnica de RandomizedScearchCV para a escolha dos hiperparâmetros do modelo e o nosso melhor resultado de 'f1-score' foi atingido com os seguintes:
- 'min_child_weight': 1
- 'max_depth': 15
- 'learning_rate': 0.2
- 'gamma': 0.4
- 'xcolsample_bytree': 0.7

Essa combinação atingiu o f1-score de 0.970591.

Abaixo segue os valores que os cinco melhores hiperparâmetros atingiram para f1-score, precision, recall e accuracy.

|rank| mean_test_f1 | std_test_f1 | mean_test_precision | std_test__precision | mean_test_recall | std_test_recall | mean_test_accuracy | std_test_accuracy |
|-|---------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|1|	0.970591|	0.001127|	0.966141|	0.002320|	0.959845|	0.002155|	0.972560|	0.001053|
|2|	0.969630|	0.001061|	0.964146|	0.001296|	0.959448|	0.002967|	0.971655|	0.000976|
|3|	0.969604|	0.000836|	0.964615|	0.002092|	0.958901|	0.003104|	0.971637|	0.000766|
|4|	0.969445|	0.000903|	0.963023|	0.002413|	0.960143|	0.001813|	0.971470|	0.000850|
|5|	0.968002|	0.000865|	0.962430|	0.002054|	0.957062|	0.003706|	0.970140|	0.000782|

Lembrando aqui um pouco sobre o significado de cada métrica:

**Precisão**: Entre os clientes que detectamos que irão cancelar, quantos de fato irão cancelar?

**Recall**: Entre os clientes que irão cancelar, quantos nos detectamos que irão cancelar?

**F1-score**: Média Harmônica entre a Precisão e o Recall.

$$F1score = 2.\frac{Precisão . Recall}{Precisão + Recall}$$

## 4.5. Modelo para os dados de Teste

Utilizando dados nunca visto pelo modelo atingimos as seguintes métricas:

|Métrica  |Score   |
|-        |-       |
|f1-score | 0.96344|
|precisão | 0.96351|
|recall   | 0.96337|
|acurácia | 0.97322|

E matriz de confusão:

![image](https://github.com/jonasbarletta/hackdays_hotel_cancellation/assets/102927918/4e61fe51-d0a8-4f0b-be78-2d8291b1794e)

# 5. Submissão

Para submissão utlizamos nossos cinco melhores modelos com um sistema de votação que funciona assim:

**Exemplo:**

|id         | modelo 1 | modelo 2 | modelo 3 | modelo 4 | modelo 5 | média | modelo final |
|-----------|----------|----------|----------|----------|----------|-------|--------------|
|118345     | 0        | 0        | 0        | 0        | 0        | 0     | 0            |
|9500       | 1        | 1        | 1        | 1        | 1        | 1     | 1            |
|15492	    | 0        | 0        | 0        | 1        | 1        | 0.4   | 0            |
|6443	      | 0        | 0        | 1        | 0        | 0        | 0.2   | 0            |
|49092      | 0        | 1        | 1        | 1        | 1        | 0.8   | 1            |
|48307      | 0        | 1        | 0        | 1        | 1        | 0.6   | 1            |

# 6. Próximos passos

- Entender o que está por trás do 'id', possibilitando extrair a features que compõem o 'id'.
- Implementar o modelo na etapa de cadastro do cliente na plataforma de reserva, dessa forma quando o cliente finalizar o cadastro já saberemos se ele tem mais chance de cancelar ou não, assim podemos limitar as possibilidades de cancelamento. Exemplo: multa para cancelamento; período de cancelamento gratuito menor do que o padrão.
- Configurar disparos de e-mails automáticos direcionados aos clientes com maior probabilidade de cancelamento.
- Solicitar dados monetários para monitorar as ações tomadas e retomar os projetos de expansão da rede.

<!-- antes de enviar a versão final, solicitamos que todos os comentários, colocados para orientação ao aluno, sejam removidos do arquivo -->
# Previsão Séries Temporais para Industria Utilizando Modelo CatBoost

#### Aluno: [Matheus Henrique Barbosa Dib Amorim](https://github.com/MatheusDib)
#### Orientadora: [Manuela Kholler](https://github.com/manoelakohler)

---

Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

---

### Resumo

A análise de séries temporais é fundamental para todas as empresas. Os departamentos de logísticas utilizam-se de técnicas para previsões de vendas afim de 
atingir seus objetivos centrais de otimização de estoques. O presente estudo tem como objetivo colaborar com o avanço de técnicas para previsões de vendas em indústrias.
Neste trabalho utilizou-se uma base de dados de uma industria de cosméticos do Brasil com dados desde 2017 de vendas na granularidade de produto e mês.
A base de dados foi separada em quatro dataframes diferentes (clusters) baseando-se em similiaridade dos produtos de mesmas categorias com alta correlação. Em seguida, 
criaram-se as features nos dataframes para futura entrada nos modelos preditivos. O modelo de machine learning escolhido para previsão foi o Catboost. O modelo obteve resultados 
melhores que os balizadores propostos para os clusters 2, 3 e 4 e resultado inferior para o cluster 1.



### 1. Introdução

As séries temporais são objetos de análise e estudo constantes nas pequenas, médias e grandes industria/empresas.
Tal tema possui tamanha importância, que é comum encontrar áreas e departamentos focados somente em lidar com o assunto.
O estudo das séries temporais ajuda a empresa a entender comportamentos passados de seus consumidores, verificar elasticidades de sua demanda
e até mesmo projetar demandas futuras para seus produtos.

Quando estas análises são realizadas, empresas conseguem entender quais investimentos passados tiveram êxíto comercial, quais campanhas publicitárias foram eficientes, 
em que momento erraram na estratégia, e até mesmo ajustar planos de produções para se tornarem condizentes com as necessidades de curto a longo prazo.

Departamentos de logística comumente são protagonistas em tal tema dentro das companhias. Isto porque a lógistica tem como um de seus principais objetivos 
a otimização de estoques, ou seja, evitar excessos e rupturas. O excesso de estoque é ruim para uma indústria, pois estoque é um capital da empresa, e as companhias querem
sempre maximizar o retorno de capital empregado. Ter um grande volume de estoques parados é uma alocação ineficiênte que poderá acarretar em prejuízos financeiros.
Por outro lado, as rupturas, são quando os estoques não são suficientes para suprir uma demanda existente no momento, fazendo com que a empresa perca uma venda. 
Também levando a um prejuizo financeiro.

Visto este objetivo central da logística, a previsão de séries temporais se torna uma ferramenta crucial para alcançar tais metas.
 
O presente estudo visa utilizar um modelo de machine learning em conjunto com os dados das séries temporais de uma idustria de cométicos do Brasil 
para prever o ponto seguinte da série.
O modelo será abastecido com dados históricos de venda do país na granularidade de mês e produto.


### 2. Modelagem

A base de dados deste trabalho contêm os dados históricos de venda desde janeiro de 2017 ao nível de granularidade de produto mês a mês.

Inicialmente, como pré-processamento dos dados, foi realizada uma divisão desta base em quatro clusters que se tornarão quatro dataframes diferentes.
Cada cluster separa um grupo de produtos de mesmas categorias, que possuem alta correlação entre suas séries temporais.
De forma que temos o seguinte cenário:

* CLUSTER 1: produtos das categorias 'Solar Rosto' e 'Corpo Outros';
* CLUSTER 2: produtos das categorias 'Acne Limpeza', 'Antirrugas Total', 'Capilar Total', 'Hidratação Corpo', 'Hidratação Rosto', 'Hidratação Outros' e 'Cicatrizante';
* CLUSTER 3: produtos das categorias 'Acne Tratamento', 'Limpeza Corpo' e 'Rosto Outros';
* CLUSTER 4: todos os demais produtos que não estão nos clusters acima.

Além disso, foram desconsiderados todos os produtos "Promo". Estes são os produtos promocionados em kit's que possuem dois ou mais produtos em um mesmo código. 
Eles foram desconsiderados, pois suas vendas são realizadas de acordo com a diretriz da industria e não possuem relação direta com demanda dos consumidores.

Realizada esta separação do dataframe foi realizada a criação das features que ajudarão o modelo de machine learning a encontrar padrões preditivos nas curvas.
Serão as variáveis de entrada do modelo.

Foram criadas as seguintes features em cada dataframe:

* LAG's de Vendas (M-1, M-2, M-3, ... , M-24);
* Médias Móveis de Vendas (2, 3 e 6 meses);
* Atributos temporais: mês e ano;
* Diferenças entre LAG's;
* Features categóricas (Marca, Código do produto e Categoria).

O modelo de machine learning escolhido foi o CatBoost, que trata-se de um modelo de árvore de decisões com a técnica de gradiente boosting.
Este modelo foi escolhido, pois consegue trabalhar de forma nativa com variáveis categórias, questão fundamental para o problema.
Os parâmetros definidos para o modelo foram os seguintes:

* iterations = 1000;
* depth = 5;
* learning_rate = 0.01;
* loss_function = 'RMSE';
* use_best_model = True;
* eval_metric = 'RMSE'.


### 3. Resultados

Dada a incerteza existente para afirmar se um modelo de previsão de vendas é satisfatório ou não, foram pensados dois balizadores para comparar os resultados.

1. Assumisse como venda futura a mesma venda realizada no mês anterior e então verifica-se a acurácia.
2. Obtem os números do processo de previsão de vendas atualmente implementado na companhia e então verifica-se a acurácia.

Com ambos os balizadores, tornou-se possível mensurar a diferença de acurácia entre os balizadores 1 e 2 versus os modelos de machine learning propostos neste estudo para prever a demanda do ponto seguinte.

Como métrica de acurácia, escolheu-se o RMSE (Raiz do Erro Quadrático Médio). Quanto menor o RMSE, melhor a previsão.

Dado que o modelo prevê somente um ponto a frente, o RMSE final foi formulado a partir da média dos últimos quatro RMSE de cada cluster.
Dessa forma, tenta-se minimizar ocilações pontuais e testa-se de forma mais sólida 
os modelos e técnicas empregadas para as previsões de vendas. 

Abaixo temos uma tabela resumo dos quatro clusters com seus resultados de RMSE finais.

| Clusters | Balizador 1  (M-1 = M) | Balizador 2  (Previsão Empresa) | Modelo  CatBoost |
|:---:|:---:|:---:|:---:|
| Cluster 1 | 2.009 | 1.362 | 1.623 |
| Cluster 2 | 2.689 | 3.076 | 2.605 |
| Cluster 3 | 395 | 596 | 338 |
| Cluster 4 | 714 | 609 | 593 |

### 4. Conclusões

Analisando os resultados, verifica-se que o modelo proposto conseguiu atingir resultados melhores de RMSE para 
os clusters 2, 3 e 4. Para o cluster 1 o modelo tem resultado inferior ao processo atual de previsão de vendas da empresa.

Interessante analisar também, que para os clusters 2 e 3, a técnica de utilizar o periodo anterior como previsão se mostrou melhor que a atual previsão da empresa, e 
com resultados bem próximos ao modelo do catboost.

Como sugestão de trabalhos futuros novos modelos de machine learning poderiam ser usados para verificar suas acurácias e também novas features poderiam ser desenvolvidas 
para incrementar os modelos preditivos.




---

Matrícula: 192.110.095

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*
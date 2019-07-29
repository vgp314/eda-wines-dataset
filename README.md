# eda-wines-dataset

-Vinicius Gomes Pereira
- viniciusgomespe@gmail.com

Análise Exploratória de Dados do dataset de vinhos

# a. Como foi a definição da sua estratégia de modelagem?
- Após a Exploração de Dados, selecionamos alguns algoritmos de natureza diferente
	1) Regressão
		Regressão Logística (usando One vs The Rest para classificação multiclasse)
	2) Bagging
		Random Forest (uma extensão de Bagging)
	3) Boosting
		xgBoost
	4) Árvores de Decisão
		Decision Tree
	5) Support Vector Machines
		SVM (usando One vs The Rest para classificação multiclasse)
	6) Algoritmos de outras naturezas
		K - Nearest Neighbors

	7) Gaussian Naive Bayers como baseline


- O Objetivo, por meio do GridSearch foi seliconar os melhores parâmetros de cada modelo, como o tipo de regularização (no caso de regressão logística), e a penalidade do fator de regularização,
 de modo a selecionar o modelo que mais se ajusta aos dados, de maneira a também combater o overfitting. 

 - GridSearch foi usado com StratifiedKfold, com n_folds = 3 (um maior número de folds implicaria em conjuntos com classes minoritárias vazias), de modo que a proporção de cada classe seja mantida em cada fold.


# b. Como foi definida a função de custo utilizada?
- Cada algoritmo tem uma função de custo diferente.

- O algoritmo de regressão logística com one vs the rest utiliza n_classes estimadores de regressão logística. Basicamente, ele prevê uma classe, e agrupa as outras classes em outra classe (por isso One vs the rest).
- Após isso, é selecionado a classe com valor máximo que foi obtido para cada estimador. Cada estimador utiliza função de custo cross entropy loss.. A função de custo aumentada, ou seja, explicitando o tipo de regularização e sua penalidade depende dos parâmetros inseridos na modelagem (avaliamos no gridSearch regularizaçaõ do tipo l1 e l2, com peso nas penalidades diferentes); 

- Para o algoritmo de Random Forest, o primeiro passo é selecionar uma amostra (bootstrap) dos dados, selecionar randomicamente m features (basicamente random forest performa feature selection), selecionar o melhor split dos dados (baseado em alguma métrica de melhor split), utilizando somente essas m features, e repetir esse processo até um nó final. Repetir todo esse processo novamente para outras árvore. O número de Árvores é escolhida no GridSearch (em que foi testato diferentes números); Por fim, por meio de um voting, é escolhido a predição correta (para os casos de regressão é feito o valor esperado). Consideramos que o critério de melhor split, minima o erro a função custo. O número de features para serem usadas no split foi escolhido por meio do GridSearch também (auto ou sqrt)

- A função de custo de árvores de decisão é parecida com a da random forest, em que o melhor split minimizaria o erro de predição.


- O xgBoost é uma implementação do GradientBoosting. Basicamente, as técnicas de Boosting aplica n preditores fracos (um pouco melhores que uma escolha randômica), de modo que, cada passo do aprendizado, a distribuição dos dados é ajustada de modo a privilegiar regiões dos dados que o preditor fraco performou errado (essas regiões recebem pesos maiores). Iterativamente, o próximo preditor fraco vai aprender melhor nos dados que o preditor da iteração anterior previu erroneamente. Em seguida, combinamos cada um dos n preditores, em que separadamente são fracos, mas cada um performa melhor em uma região dos dados diferente. A função erro é aplicada em cada iteração. A função erro de xGBoost utilizada foi a cross entropy loss.

- A função erro de SVM é o inverso da distância do melhor hiperplano que separa os dados, em relação ao ponto mais próximo ao hiperplano. A função de custo aumentada inclui um fator penalidade dos erros de predição e fator de regularização dos parâmetros do plano.

- Para o algoritmo de K - nearest Neighbors, foram utilzadas diversas métricas de distância (euclidiana por exemplo), escolhidas pelo GridSearch. A classificação é computada pelo voting dos k vizinhos mais próximos. Presume-se que escolher o vizinho mais próximo minimiza o erro de predição.



# c. Qual foi o critério utilizado na seleção do modelo final?
O modelo final é escolhido pelo menor erro utilizando o conjunto de validação. O conjunto de validação foi escolhido mantendo a proporção das classes. Assumindo que os dados passados são uma amostra aleatória do conjunto universo, inferimos que o erro no conjunto de validação é uma boa estimativa do erro fora da amostra. Obviamente, o conjunto de validação não deve ser usada para treino.


# d. Qual foi o critério utilizado para validação do modelo? Por que escolheu utilizar este método?

- Utilizamos GridSearch para a escolha dos melhores parâmetros, utilizando StratifiedKfold com o número de folds = 3, de modo a preservar a proporção das classes, e assim, não inserir nenhum viés.

# e. Quais evidências você possui de que seu modelo é suficientemente bom?
	
- Comparamos com Gaussian Naive Bayes (baseline), que assume que as features são independentes (naive) e obtivemos melhor resultado.
- Assumindo que nosso conjunto de dados é uma amostra randômica do conjunto universo, avaliamos nossos modelos em um conjunto não utilizado para aprendizagem, e por isso, podemos utilizar essa métrica do erro como generalização do erro fora da amostra
- Evitamos overfitting, utilizando diferentes técnicas de regularização, e penalidade para esses erros.
- Utilizamos modelos de diferentes naturezas, de modo a conseguir captar de diferentes modos o comportamento dos dados.
- O split de dados foi utilizado mantendo a proporção de classes

% la page web au format texte+formules en syntaxe latex

# Introduction (lucas)
% qu'est-ce que RL ?
% distinction on-line/off-line
% distinction model-based / model-free

% définir le pure-online, et donc le batch par opposition
% exemples où le batch est nécessaire

# Classification des batchs (leo)
% selon interaction ou pas (IE), la quantité d'expérience utilisée (QE)
% IE=non QE=tout "pure batch"
% IE=oui QE=tout growing batch
% IE=oui QE=partielle semi-batch

# Le modèle général (alexandre)
TODO: mettre les liens wiki et références biblio

## Processus de décision de Markov
Les *processus de décision de Markov* (Markov Decision Processes (MDP) en anglais) forment le modèle sous-jacent à tout algorithme d'apprentissage par renforcement, qu'il soit en ligne ou hors ligne.

Dans ce modèle, l'environnement d'apprentissage est modélisé par:
* un *ensemble d'états* <math>S</math>, qui peut être fini, dénombrable ou continu; cet ensemble définit l'environnement tel que perçu par l'apprenant (dans le cas d'un robot, on peut voir cela comme l'ensemble produit des valeurs de ses différents capteurs);
* un *ensemble d'actions* <math>A</math>, qui peut être fini, dénombre ou continu et dans lequel l'apprenant choisi les interactions qu'il effectue avec l'environnement (dans le cas d'un robot on peut voir cela comme l'ensemble produit des paramètres de ses différentes commandes);
* une *fonction de transition* <math>T:S\times A\times S\to[0;1]</math>; cette fonction définit l'effet des actions de l'apprenant sur l'environnement: <math>T(s, a, s')</math> représente la probabilité de se retrouver sans l'état <math>s'</math> en effectuant l'action <math>a</math>, sachant que l'on était l'instant d'avant dans l'état <math>s</math>.
<math>T</math> ainsi définie représente le cas le plus général; dans un environnement déterministe, on aura plutôt <math>T:S\times A\to S</math>.
* une *fonction de récompense* <math>R:S\times A\times S\times\RR\to[0;1]</math>; elle définit la récompense (positive ou négative) reçue par l'agent: <math>R(s,a,s',v)</math> est la probabilité d'obtenir une récompense <math>v</math> pour être passé de l'état <math>s</math> à <math>s'</math> en ayant effectué l'action <math>a</math>.
Ici encore cette définition est très générale, bien souvent on se contentera par exemple des cas particuliers suivants:
..* <math>R:S\times A\times S\to\RR</math> (récompense déterministe, c'est le choix que nous adopterons dans la suite);
..* <math>R:S\times A\to\RR</math> (récompense déterministe rattachée à l'action en ignorant son résultat);
..* <math>R:S\to\RR</math> (récompense déterministe rattachée à un état donné).
 
NB: nous ne considérons ici que les modèles dans lesquels le temps est discrétisé, c'est-à-dire que la «trajectoire» d'un apprenant dans l'environnement est décrivable par *une suite* d'états <math>s_t</math> (<math>t\in\NN</math>), et non par *une fonction* <math>s(t)</math> avec <math>t\in\RR</math>.
De même on notera <math>a_t</math> la suite des actions prises par l'agent.
On pourra consulter [ref]() pour une description des MDP à temps continu.

## Politiques, fonctions de valeur et équations de Bellman
La politique d'un agent est la manière avec laquelle il choisit l'action qu'il va effectuer lorsqu'il se trouve dans un état donné.
Formellement il s'agit donc d'une fonction <math>\pi: S\to A</math> dans le cas d'une politique déterministe (ce que nous supposerons dans la suite), ou <math>\pi:S\times A\to[0;1]</math> dans le cas stochastique: on a donc <math>a_t=\pi(s_t)</math>.
Il est possible de généraliser la notion de politique en la faisant dépendre non uniquement de l'état présent, mais de l'ensemble de la trajectoire passée de l'agent dans l'environnement, et donc en particulier du temps qui passe [ref à trouver]().

Afin que l'agent puisse choisir une politique, il faut lui fournir un critère de choix.
Ce critère est lié à la fonction de récompense <math>R</math>. 
Notons <math>r_t=R(s_t, \pi(s_t), s_{t+1})</math> la récompense effective obtenue au temps <math>t</math> (après avoir effectué son action) par l'agent qui suit la politique <math>\pi</math>.
Voici plusieurs choix possibles de critères pour l'agent:
* maximiser <math>E\left(\sum_{t=0}^h r_t\right)</math>: espérance de la somme des récompenses à un horizon fini;
* maximiser <math>\lim_{h\to+\infty} E\left({1\over h}\sum_{t=0}^h r_t\right)</math>: récompense moyenne à long terme;
* maximiser <math>E\left(\sum_{t=0}^\infty \gamma^t r_t\right)</math> (<math>0\leq \gamma<1</math>): récompense amortie à horizon infini.
Le dernier critère est courant et c'est celui que nous adoptons dans la suite.
La valeur de <math>\gamma</math> permet de définir l'importance que l'on donne au futur.
Quand <math>\gamma=0</math> nous sommes face à un agent «pessimiste» qui ne cherche qu'à optimiser son gain immédiat.
À l'opposé si <math>\gamma\to 1</math>, l'agent est «optimiste» puisqu'il tient de plus en plus sérieusement compte du futur lointain.

Lorsqu'une politique et un critère sont déterminés, deux fonctions peuvent être définies:
* <math>V^\pi: S\to\RR</math>: c'est la fonction de valeur des états; <math>V\pi(s)</math> représente le gain (selon le critère adopté) engrengé par l'agent s'il démarre à l'état <math>s</math> et applique ensuite la politique <math>\pi</math> ad infinitum.
* <math>Q^\pi: S\times A\to\RR</math>: c'est la fonction de valeur des états-actions; <math>Q^\pi(s,a)</math> représente le gain engrengé par l'agent s'il démarre à l'état <math>s</math> et commence par effectuer l'action <math>a</math>, avant d'appliquer ensuite la politique <math>\pi</math> ad infinitum.
Les deux fonctions sont intimement liées. 
On a toujours <math>V^\pi(s) = Q^\pi(s, \pi(s))</math> et, dans le cas du gain amorti à horizon infini:
:<math> Q^pi(s, a) &= \sum_{s'\in S} [R(s,a,s') + \gamma V^\pi(s')]T(s,a,s'). </math>
Cette dernière relation montre que la fonction <math>V^{\pi}</math> vérifie une relation de récurrence appelée équation de Bellman:
:<math> V^pi(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^\pi(s')]T(s,\pi(s),s'). </math>

### Résolution de l'équation de Bellman
Une politique étant fixée, l'équation de Bellman peut résoudre d'au moins deux manières, permettant donc de déterminer les valeurs de <math>V^\pi</math>, et par suite, celles de <math>Q^\pi</math> également.
* on peut déjà remarquer que, dans le cas où le nombre d'états <math>n</math> est fini, l'équation de Bellman cache en fait un système linéaire de <math>n</math> équations à <math>n</math> inconnues. 
On peut donc le résoudre, une fois traduit en une équation matricielle, par une technique telle que le pivot de Gauss.
* on peut également remarquer qu'en posant
:<math>K(f)(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma f(s)]T(s,\pi(s),s'),</math>
on définit un opérateur <math>K</math> pour lequel <math>V^\pi</math> est un point fixe.
On peut montrer que <math>K</math> est une contraction, ce qui garantit d'une part l'existence d'un unique point fixe, et d'autre part que la suite récurrence <math>V_{n+1} = K(V_n)</math> converge vers ce point fixe exponentiellement vite.

### Equations d'optimalité de Bellman
Le but de l'agent est de trouver la politique optimale <math>\pi^*</math> qui lui permet de maximiser son gain, c'est-à-dire celle qui vérifie, pour tout état <math>s\in S</math>, <math>V^{\pi^*}(s)\geq V^{\pi}(s)</math> quelle que soit l'autre politique <math>\pi</math>.
On peut montrer que la fonction de valeurs optimale <math>V^*</math> vérifie l'équation d'optimalité de Bellman:
:<math>V^*(s) = \max_{a\in A}\sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^*(s')]T(s,a,s').</math>

De manière analogue, la fonction <math>Q</math> vérifie elle aussi une équation d'optimalité:
:<math>Q^*(s,a) = \sum_{s'\in S} [R(s,a,s') + \gamma \max_{a'\in A} Q^*(s',a')]T(s,a,s').</math>

#### Résolution des équations d'optimalité de Bellman
Les équations d'optimalité de Bellman ne sont pas linéaires, il faut donc abandonner l'idée de les résoudre algébriquement.
En revanche, l'opérateur <math>K</math> défini par
:<math>K(f)(s) = \max_{a\in A}\sum_{s'\in S} [R(s,a,s') + \gamma f(s)]T(s,a,s'),</math>
définit encore une contraction dont <math>V^*</math> est un point fixe.
La fonction de valeurs optimale peut donc à nouveau s'approcher par un processus itératif à convergence exponentielle.

#### Algorithmes de détermination de la politique optimale.
La méthode itérative que nous venons de voir pour les équations d'optimalité de Bellman fournit un premier algorithme, appelé *itération de la valeur* (value-iteration) permettant de déterminer <math>\pi^*</math>.
Il suffit en effet de déterminer <math>V^*</math> avec une précision donnée, et on peut en déduire la politique optimale par:
:<math>\pi(s) 
= \argmax_{a\in A} Q^*(s,a)  
= \argmax_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^*(s')]T(s,a,s').</math>
Une difficulté dans cet algorithme est de déterminer la précision avec laquelle calculer <math>V^*</math> de manière à être sûr d'en déduire effectivement la politique optimale.

Un autre algorithme, appelé *itération de la politique* (policy-iteration) essaye d'obtenir la politique optimale sans nécessairement calculer "jusqu'au bout" les valeurs de <math>V^*</math>.
L'idée est de partir d'une politique quelconque <math>\pi_0</math>, puis d'alterner une phase d'évaluation, dans laquelle la fonction <math>V^{\pi_n}</math> est déterminée (avec une des techniques vues plus haut), avec une phase d'amélioriation, où l'on définit la politique suivante <math>\pi_{n+1}</math> par:
:<math>\pi_{n+1}(s) = \argmax_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^{\pi_n}(s')]T(s,a,s').</math>.
Cet algorithme prend fin lorsqu'aucune évolution de la politique n'est observée, ie, lorsque <math>\pi_{n+1}(s)=\pi_n(s)</math> pour tout <math>s</math>.

Si dans l'algorithme précédent l'on utilise une méthode itérative pour évaluer <math>V^\pi</math>, alors se pose la question de savoir à quelle précision s'arrêter.
Ce problème n'en est en réalité pas un, car on peut montrer que même si l'on tronque l'évaluation de <math>V^\pi</math>, l'algorithme converge tout de même vers l'optimal.
À l'extrême, c'est-à-dire lorsqu'une seule itération est utilisée pour évaluer <math>V^\pi</math>, et après avoir réuni en une seule itération la phase d'amélioration et la phase d'évaluation, on retombe sur l'algorithme d'itération de la valeur.

TODO: faut-il ici présenter des algos en ligne qui traite du cas où le MDP n'est pas connu ? ou alors ce qui vient d'être dit est suffisant pour notre section sur les algos batch ?

# Exemples (lucas)
% présenter la modélisation sur deux trois exemples:
% dire quels états, quelles actions...

# Les techniques batch

## généralités (leo)
% principe experience replay
% principe fitting

##les algos principaux
% à chaque fois, expliquer le principe, donner un pseudo-code grossier
% pointer article(s) qui l'utilisent... le plus le mieux
% si possible: pointer au max notre classification
###kernel based (leo)
###fitted Q iteration (lucas)
###least-squares policy iteration (alex)
###monte-carlo ??


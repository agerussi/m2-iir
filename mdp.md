{{ébauche|informatique théorique}}

Un '''processus de décision markovien''' (MDP) est un [[modèle]] [[stochastique]] issu de la [[théorie de la décision]] et de la [[théorie des probabilités]] et (partiellement) sous le contrôle d'un preneur de décision. Les MDPs sont utilisés pour étudier un grand nombre de [[problèmes d'optimisation]] avec l'aide d'algorithmes de [[Programmation dynamique|programmation dynamique]] ou d'[[apprentissage par renforcement]]. Les MDPs sont connus depuis 1950s (cf. Bellman 1957). Une grande contribution provient du travail de [[Ronald A. Howard]] avec son livre de 1960, 'Dynamic Programming and Markov Processes''. Ils sont utilisés dans de nombreuses disciplines, incluant la [[robotique]], l'[[automatisation]], l'[[économie]], et l'[[Industrie manufacturière]].

Plus précisément, un processus de décision markovien est un processus de [[contrôle]] [[stochastique]] [[discret]]. À chaque étape, le processus est dans un certain état <math>s</math>, et le décideur peut choisir une action <math>a</math> accessible dans un état <math>s</math>. L'action fait passer de l'état <math>s</math> à l'état <math>s'</math> récoltant au passage une récompense <math>R(s, a, s')</math> donnée au décideur.

La probabilité que le processus arrive à l'état <math>s'</math> est déterminée par l'action choisie. Plus précisément, elle est décrite par la fonction de transition d'états <math>T(s, a, s')</math>. Donc, l'état <math>s'</math> dépend de l'état actuel <math>s</math> et de l'action <math>a</math> sélectionnée par le décideur. Cependant, pour un <math>s</math> et un <math>a</math>, le prochain état est indépendant des actions et états précédents. On dit alors que le processus satisfait la ''[[propriété de Markov]]''.

Les MDPs sont une extension des [[Chaîne_de_Markov|chaînes de Markov]]. La différence est l'addition des actions (permettant de choisir) et des récompenses (donnant la motivation de ces choix). Dans le cas où une action existe pour toutes les étapes et les récompenses sont les mêmes, le processus de décision Markovien est une [[Chaîne_de_Markov|chaînes de Markov]]. 

== Définition intuitive ==

Afin de comprendre ce qu'est un '''MDP''', supposons que l'on ait un système évoluant dans le temps comme un [[Système de transition d'états|automate]] [[probabilité|probabiliste]]. À chaque instant le système est dans un état donné et il existe une certaine [[probabilité]] pour que le système évolue vers tel ou tel autre état à l'instant suivant en effectuant une transition.

Supposons maintenant que l'on doive contrôler ce système ''[[Boîte noire (informatique)|boite noire]]'' de la meilleure façon possible. L'objectif est de l'amener dans un état considéré comme ''bénéfique'', en évitant de lui faire traverser des états ''néfastes''. Pour cela, on dispose d'un ensemble d'actions possibles sur le système. Pour compliquer la chose, on supposera que l'effet de ces actions sur le système est probabiliste : l'action entreprise peut avoir l'effet escompté ou un tout autre effet. L'efficacité du contrôle est mesurée relativement au gain ou à la pénalité reçue au long de l'expérience.

Ainsi, un [[raisonnement]] à base de '''MDP''' peut se ramener au discours suivant : ''étant dans tel cas et choisissant telle action, il y a tant de chance que je me retrouve dans tel nouveau cas avec tel gain.''

Pour illustrer les MDP, on prend souvent des exemples issus de la [[robotique]] mobile (avec les positions pour états, les commandes comme actions, les mouvements comme transitions et l'accomplissement/échec de tâches comme gains/pénalités).

== Hypothèse de Markov ==

Dans les '''MDP''', l'évolution du système est supposée correspondre à un processus markovien. Autrement dit, le système suit une succession d'états distincts dans le temps et ceci en fonction de probabilités de transitions. L'[[Propriété markovienne|hypothèse de Markov]] consiste à dire que les probabilités de transitions ne dépendent que des '''n''' états précédents. En général, on se place à l'ordre '''n = 1''', ce qui permet de ne considérer que l'état courant et l'état suivant.

== Définition formelle ==

Un MDP est un quadruplet <math>\{ S, A, T, R \}\,</math> définissant:

* un ''ensemble d'états'' <math>S</math>, qui peut être fini, [[Ensemble_dénombrable|dénombrable]] ou [[Puissance_du_continu|continu]]; cet ensemble définit l'environnement tel que perçu par l'apprenant (dans le cas d'un robot, on peut voir cela comme l'ensemble produit des valeurs de ses différents capteurs);
* un ''ensemble d'actions'' <math>A</math>, qui peut être fini, dénombrable ou continu et dans lequel l'apprenant choisit les interactions qu'il effectue avec l'environnement (dans le cas d'un robot on peut voir cela comme l'ensemble produit des paramètres de ses différentes commandes);
* une ''fonction de transition'' <math>T:S\times A\times S\to[0;1]</math>; cette fonction définit l'effet des actions de l'apprenant sur l'environnement: <math>T(s, a, s')</math> représente la probabilité de se retrouver sans l'état <math>s'</math> en effectuant l'action <math>a</math>, sachant que l'on était l'instant d'avant dans l'état <math>s</math>.
<math>T</math> ainsi définie représente le cas le plus général; dans un environnement déterministe, on aura plutôt <math>T:S\times A\to S</math>.
* une ''fonction de récompense'' <math>R:S\times A\times S\times\R\to[0;1]</math>; elle définit la récompense (positive ou négative) reçue par l'agent: <math>R(s,a,s',v)</math> est la probabilité d'obtenir une récompense <math>v</math> pour être passé de l'état <math>s</math> à <math>s'</math> en ayant effectué l'action <math>a</math>. Ici encore cette définition est très générale, bien souvent on se contentera par exemple des cas particuliers suivants:
** <math>R:S\times A\times S\to\R</math> (récompense déterministe, c'est le choix que nous adopterons dans la suite);
** <math>R:S\times A\to\R</math> (récompense déterministe rattachée à l'action en ignorant son résultat);
** <math>R:S\to\R</math> (récompense déterministe rattachée à un état donné).

NB: nous ne considérons ici que les modèles dans lesquels le temps est discrétisé, c'est-à-dire que la «trajectoire» d'un apprenant dans l'environnement est décrivable par ''une suite'' d'états <math>s_t</math> (<math>t\in\N</math>), et non par ''une fonction'' <math>s(t)</math> avec <math>t\in\R</math>.
De même on notera <math>a_t</math> la suite des actions prises par l'agent.
On pourra consulter 
<ref name="CTMDP">{{en}} Xianping Guo, Onesimo Hernandez-Lerma, ''Continuous-Time Markov Decision Processes: Theory and Applications'', Springer-Verlag Berlin Heidelberg, 2009, {{ISBN|978-3-642-02546-4}}</ref>
pour une description des MDP à temps continu.

== Exemple de MDP ==

[[Image:Markov_Decision_Process_example.png|400px|right|thumb|Exemple simple de '''Processus de Décision Markovien''' à trois états et à deux actions.]]

L'exemple donné ci-contre représente un '''Processus de Décision Markovien''' à trois états distincts <math>\{ s_0, s_1, s_2 \}</math> représentés en vert. Depuis chacun des états, on peut effectuer une action de l'ensemble <math>\{ a_0, a_1 \}</math>. Les nœuds rouges représentent donc une décision possible (le choix d'une action dans un état donné). Les nombres indiqués sur les flèches sont les probabilités d'effectuer la transition à partir du nœud de décision. Enfin, les transitions peuvent générer des récompenses (dessinées ici en jaune).

*La matrice de transition associée à l'action <math>a_0</math> est la suivante :

<math>\begin{pmatrix} 0.50 & 0 & 0.50 \\ 0.70 & 0.10 & 0.20 \\ 0.40 & 0 & 0.60 \end{pmatrix}</math>

*La matrice de transition associée à l'action <math>a_1</math> est la suivante :

<math>\begin{pmatrix} 0 & 0 & 1.0 \\ 0 & 0.95 & 0.05 \\ 0.30 & 0.30 & 0.40 \end{pmatrix}</math>

En ce qui concerne les récompenses, 
* on perçoit une récompense de ''+5'' lorsque l'on passe de l'état <math>s_1</math> à l'état <math>s_0</math> en accomplissant l'action <math>a_0</math> 
* on perçoit une récompense de ''-1'' (aussi appelée pénalité) lorsque l'on passe de l'état <math>s_2</math> à l'état <math>s_0</math> en accomplissant l'action <math>a_1</math> 

== Remarques ==

Le modèle '''MDP''' présenté ici est supposé stable dans le temps, c'est-à-dire que les composants du quadruplet sont supposés invariants. Il n'est donc pas applicable en l'état pour un système qui évolue, par exemple pour modéliser un système qui apprend contre un autre agent.

== Politiques, fonctions de valeurs et équations de Bellman ==

La politique d'un agent est la manière avec laquelle il choisit l'action qu'il va effectuer lorsqu'il se trouve dans un état donné.
Formellement il s'agit donc d'une fonction <math>\pi: S\to A</math> dans le cas d'une politique déterministe (ce que nous supposerons dans la suite), ou <math>\pi:S\times A\to[0;1]</math> dans le cas [[Processus_stochastique|stochastique]]: on a donc <math>a_t=\pi(s_t)</math>.

Afin que l'agent puisse choisir une politique, il faut lui fournir un critère de choix.
Ce critère est lié à la fonction de récompense <math>R</math>. 
Notons <math>r_t=R(s_t, \pi(s_t), s_{t+1})</math> la récompense effective obtenue au temps <math>t</math> (après avoir effectué son action) par l'agent qui suit la politique <math>\pi</math>.
Voici plusieurs choix possibles de critères d'intérêts que l'agent peut chercher à maximiser:
* <math>E\left(\sum_{t=0}^h r_t\right)</math>: espérance de la somme des récompenses à un horizon fini;
* <math>\lim_{h\to+\infty} E\left({1\over h}\sum_{t=0}^h r_t\right)</math>: récompense moyenne à long terme;
* <math>E\left(\sum_{t=0}^\infty \gamma^t r_t\right)</math> (<math>0\leq \gamma<1</math>): récompense amortie à horizon infini.
Le dernier critère est courant et c'est celui que nous adoptons dans la suite.
La valeur de <math>\gamma</math> permet de définir l'importance que l'on donne au futur.
Quand <math>\gamma=0</math> nous sommes face à un agent «pessimiste» qui ne cherche qu'à optimiser son gain immédiat.
À l'opposé si <math>\gamma\to 1</math>, l'agent est «optimiste» puisqu'il tient de plus en plus sérieusement compte du futur lointain.

Lorsqu'une politique et un critère sont déterminés, deux fonctions centrales peuvent être définies:
* <math>V^\pi: S\to\R</math>: c'est la fonction de valeur des états; <math>V^\pi(s)</math> représente le gain (selon le critère adopté) engrengé par l'agent s'il démarre à l'état <math>s</math> et applique ensuite la politique <math>\pi</math> ad infinitum.
* <math>Q^\pi: S\times A\to\R</math>: c'est la fonction de valeur des états-actions; <math>Q^\pi(s,a)</math> représente le gain engrengé par l'agent s'il démarre à l'état <math>s</math> et commence par effectuer l'action <math>a</math>, avant d'appliquer ensuite la politique <math>\pi</math> ad infinitum.
Les deux fonctions sont intimement liées. 
On a toujours <math>V^\pi(s) = Q^\pi(s, \pi(s))</math> et, dans le cas du gain amorti à horizon infini, on peut également écrire que:
:<math> Q^\pi(s, a) = \sum_{s'\in S} [R(s,a,s') + \gamma V^\pi(s')]T(s,a,s'). </math>
Cette dernière relation montre que la fonction <math>V^{\pi}</math> vérifie une relation de récurrence appelée équation de [[Richard_Bellman|Bellman]]:
:<math> V^\pi(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^\pi(s')]T(s,\pi(s),s'). </math>

== Problèmes possibles ==

* Planification : il s'agit, étant donné un '''MDP''' <math>\{ S, A, T, R \}</math>, de trouver quelle est la politique <math>\pi</math> qui maximise la récompense.
* Améliorer une politique connue : soit une politique <math>\pi_0</math>, on souhaite trouver une meilleure politique. 
Ce problème est notamment au cœur des algorithmes de recherche de la politique optimale.
* Apprentissage d'une politique sans connaitre le modèle:
** à partir de traces d'exécution: c'est le problème de l'[[apprentissage par renforcement hors ligne]].
** au cours d'expériences sur le modèle, on parle alors d'[[apprentissage par renforcement]] en ligne.

== Algorithmes ==
Une politique étant fixée, l'équation de Bellman peut se résoudre d'au moins deux manières, permettant donc de déterminer les valeurs de <math>V^\pi</math>, et par suite, celles de <math>Q^\pi</math> également.
* on peut déjà remarquer que, dans le cas où le nombre d'états <math>n</math> est fini, l'équation de Bellman cache en fait un système linéaire de <math>n</math> équations à <math>n</math> inconnues. 
On peut donc le résoudre, une fois traduit en une équation matricielle, par une technique telle que le [[Élimination_de_Gauss-Jordan|pivot de Gauss]].
* on peut également remarquer qu'en posant
:<math>K(f)(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma f(s)]T(s,\pi(s),s'),</math>
on définit un opérateur <math>K</math>, appelé opérateur de Bellman, pour lequel <math>V^\pi</math> est un point fixe.
On peut montrer que <math>K</math> est une [[Application_contractante|contraction]], ce qui garantit d'une part l'existence d'un unique [[Point_fixe|point fixe]], et d'autre part que la suite récurrence <math>V_{n+1} = K(V_n)</math> converge vers ce point fixe exponentiellement vite.

=== Équations d'optimalité de Bellman ===
Le but de l'agent est de trouver la politique optimale <math>\pi^*</math> qui lui permet de maximiser son gain, c'est-à-dire celle qui vérifie, pour tout état <math>s\in S</math>, <math>V^{\pi^*}(s)\geq V^{\pi}(s)</math> quelle que soit l'autre politique <math>\pi</math>.
On peut montrer que la fonction de valeurs optimale <math>V^*</math> vérifie l'équation d'optimalité de Bellman:
:<math>V^*(s) = \max_{a\in A}\sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^*(s')]T(s,a,s').</math>

De manière analogue, la fonction <math>Q</math> vérifie elle aussi une équation d'optimalité:
:<math>Q^*(s,a) = \sum_{s'\in S} [R(s,a,s') + \gamma \max_{a'\in A} Q^*(s',a')]T(s,a,s').</math>

=== Résolution des équations d'optimalité de Bellman ===
Les équations d'optimalité de Bellman ne sont pas [[Linéarité|linéaires]], il faut donc abandonner l'idée de les résoudre algébriquement.
En revanche, l'opérateur de Bellman <math>K^*</math> défini par
:<math>K^*(f)(s) = \max_{a\in A}\sum_{s'\in S} [R(s,a,s') + \gamma f(s)]T(s,a,s'),</math>
définit encore une contraction dont <math>V^*</math> est un point fixe.
La fonction de valeurs optimale peut donc à nouveau s'approcher par un processus itératif à convergence exponentielle.

=== Déterminer la politique optimale: algorithme d'Itération sur la valeur (VI) ===
La méthode itérative que nous venons de voir pour les équations d'optimalité de Bellman fournit un premier algorithme, appelé ''itération sur la valeur'' (VI: Value-Iteration) permettant de déterminer <math>\pi^*</math>.
Il suffit en effet de déterminer <math>V^*</math> avec une précision donnée, et on peut en déduire la politique optimale par:
:<math>\pi(s) 
= \arg\max_{a\in A} Q^*(s,a)  
= \arg\max_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^*(s')]T(s,a,s').</math>
Une difficulté dans cet algorithme est de déterminer la précision avec laquelle calculer <math>V^*</math> de manière à être sûr d'en déduire effectivement la politique optimale.

=== Déterminer la politique optimale: algorithme d'Itération sur la politique (PI) ===
[[File:Policy-iteration.svg|right|alt=policy-iteration|Description générale d'un algorithme d'itération de la politique]]
Un autre algorithme, appelé ''itération de la politique'' (PI: Policy-Iteration) essaye d'obtenir la politique optimale sans nécessairement calculer «jusqu'au bout» les valeurs de <math>V^*</math>.
L'idée est de partir d'une politique quelconque <math>\pi_0</math>, puis d'alterner une phase d'évaluation, dans laquelle la fonction <math>V^{\pi_n}</math> est déterminée (avec une des techniques vues plus haut), et une phase d'amélioration, où l'on définit la politique suivante <math>\pi_{n+1}</math> par:
:<math>\pi_{n+1}(s) = \arg\max_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^{\pi_n}(s')]T(s,a,s').</math>.
Cet algorithme prend fin lorsqu'aucune évolution de la politique n'est observée, ie, lorsque <math>\pi_{n+1}(s)=\pi_n(s)</math> pour tout <math>s</math>.

Si dans l'algorithme précédent l'on utilise une méthode itérative pour évaluer <math>V^\pi</math>, alors se pose la question de savoir à quelle précision s'arrêter.
Ce problème n'en est en réalité pas un, car on peut montrer que même si l'on tronque l'évaluation de <math>V^\pi</math>, l'algorithme converge tout de même vers l'optimal.
À l'extrême, c'est-à-dire lorsqu'une seule itération est utilisée pour évaluer <math>V^\pi</math>, et après avoir réuni en une seule étape de calcul la phase d'amélioration et la phase d'évaluation, on retombe sur l'algorithme VI.

L'algorithme PI peut également se formuler dans les termes de la fonction d'états-actions <math>Q</math> plutôt que <math>V</math>.
On voit donc qu'un grand nombre de variantes peuvent être imaginées, mais tournant toutes autour d'un même principe général qui est schématisé à la figure ci-contre.

== Articles connexes ==

Voir aussi :
* Les [[Chaîne de Markov|chaînes de Markov]], dont dérivent les '''MDP''',
* Les [[Processus de décision markovien partiellement observable|processus de décision markoviens partiellement observables]] (POMDP), qui permettent de modéliser l'incertitude sur l'état dans lequel on se trouve
* Le [[calcul stochastique]], qui est à la base les modèles stochastiques
* L'[[apprentissage par renforcement]], méthode permettant de résoudre des processus de décision markoviens.
* Les [[métaheuristique]]s, méthodes utilisant parfois des processus markoviens.

== Bibliographie ==

* (Putterman 1994) Putterman M. L., Markov Decision Processes. Discrete stochastic dynamic programming. Wiley-Interscience, New York 1994, 2005.
* [http://www.cs.ualberta.ca/~sutton/book/the-book.html (Sutton et Barto 1998) Sutton R. S. et Barto A.G. Reinforcement Learning: An introduction. MIT Press, Cambridge, MA, 1998.]

== Références ==
{{...}}
<references />

{{Portail|probabilités et statistiques|informatique théorique}}

[[Catégorie:Décision]]
[[Catégorie:Intelligence artificielle]]
[[Catégorie:Processus stochastique]]

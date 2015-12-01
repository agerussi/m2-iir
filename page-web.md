% distinction model-based / model-free
% exemples où le batch est nécessaire

L''''apprentissage par renforcement hors ligne''' est un cas particulier de l'[[apprentissage par renforcement]], qui est une classe de problèmes d'[[apprentissage automatique]] dont l'objectif est de déterminer à partir d'expériences une stratégie (ou politique) permettant à un agent de maximiser une récompense numérique au cours du temps.

Dans le cadre de l'apprentissage par renforcement purement hors ligne (ou ''batch''), l'agent ne peut pas intéragir avec l'environnement pendant son apprentissage : il commence par explorer l'environnement pour générer une base d'apprentissage, puis il utilise cette base d'apprentissage pour apprendre une politique. Cette approche est particulièrement avantageuse quand il n'est pas possible d'effectuer des expériences ou lorsque ces expériences sont coûteuses. En général, les techniques d'apprentissage par renforcement en mode batch peuvent être utilisées dans un cadre plus large, où la base d'apprentissage peut évoluer au cours du temps. L'agent peut alors alterner entre des phases d'exploration et des phases d'apprentissage.


== Classification des batchs (leo) ==
% selon interaction ou pas (IE), la quantité d'expérience utilisée (QE)
% IE=non QE=tout "pure batch"
% IE=oui QE=tout growing batch
% IE=oui QE=partielle semi-batch

== Le modèle général (alexandre) ==
TODO: mettre références WIKI

=== Processus de décision de Markov ===
Les ''processus de décision de Markov'' (Markov Decision Processes (MDP) en anglais) forment le modèle sous-jacent à tout algorithme d'apprentissage par renforcement, qu'il soit en ligne ou hors ligne.

Dans ce modèle, l'environnement d'apprentissage est modélisé par:
* un ''ensemble d'états'' <math>S</math>, qui peut être fini, dénombrable ou continu; cet ensemble définit l'environnement tel que perçu par l'apprenant (dans le cas d'un robot, on peut voir cela comme l'ensemble produit des valeurs de ses différents capteurs);
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

=== Politiques, fonctions de valeur et équations de Bellman ===
La politique d'un agent est la manière avec laquelle il choisit l'action qu'il va effectuer lorsqu'il se trouve dans un état donné.
Formellement il s'agit donc d'une fonction <math>\pi: S\to A</math> dans le cas d'une politique déterministe (ce que nous supposerons dans la suite), ou <math>\pi:S\times A\to[0;1]</math> dans le cas stochastique: on a donc <math>a_t=\pi(s_t)</math>.

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
* <math>V^\pi: S\to\R</math>: c'est la fonction de valeur des états; <math>V\pi(s)</math> représente le gain (selon le critère adopté) engrengé par l'agent s'il démarre à l'état <math>s</math> et applique ensuite la politique <math>\pi</math> ad infinitum.
* <math>Q^\pi: S\times A\to\R</math>: c'est la fonction de valeur des états-actions; <math>Q^\pi(s,a)</math> représente le gain engrengé par l'agent s'il démarre à l'état <math>s</math> et commence par effectuer l'action <math>a</math>, avant d'appliquer ensuite la politique <math>\pi</math> ad infinitum.
Les deux fonctions sont intimement liées. 
On a toujours <math>V^\pi(s) = Q^\pi(s, \pi(s))</math> et, dans le cas du gain amorti à horizon infini:
:<math> Q^\pi(s, a) = \sum_{s'\in S} [R(s,a,s') + \gamma V^\pi(s')]T(s,a,s'). </math>
Cette dernière relation montre que la fonction <math>V^{\pi}</math> vérifie une relation de récurrence appelée équation de Bellman:
:<math> V^\pi(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^\pi(s')]T(s,\pi(s),s'). </math>

==== Résolution de l'équation de Bellman ====
Une politique étant fixée, l'équation de Bellman peut résoudre d'au moins deux manières, permettant donc de déterminer les valeurs de <math>V^\pi</math>, et par suite, celles de <math>Q^\pi</math> également.
* on peut déjà remarquer que, dans le cas où le nombre d'états <math>n</math> est fini, l'équation de Bellman cache en fait un système linéaire de <math>n</math> équations à <math>n</math> inconnues. 
On peut donc le résoudre, une fois traduit en une équation matricielle, par une technique telle que le pivot de Gauss.
* on peut également remarquer qu'en posant
:<math>K(f)(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma f(s)]T(s,\pi(s),s'),</math>
on définit un opérateur <math>K</math> pour lequel <math>V^\pi</math> est un point fixe.
On peut montrer que <math>K</math> est une contraction, ce qui garantit d'une part l'existence d'un unique point fixe, et d'autre part que la suite récurrence <math>V_{n+1} = K(V_n)</math> converge vers ce point fixe exponentiellement vite.

==== Equations d'optimalité de Bellman ====
Le but de l'agent est de trouver la politique optimale <math>\pi^*</math> qui lui permet de maximiser son gain, c'est-à-dire celle qui vérifie, pour tout état <math>s\in S</math>, <math>V^{\pi^*}(s)\geq V^{\pi}(s)</math> quelle que soit l'autre politique <math>\pi</math>.
On peut montrer que la fonction de valeurs optimale <math>V^*</math> vérifie l'équation d'optimalité de Bellman:
:<math>V^*(s) = \max_{a\in A}\sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^*(s')]T(s,a,s').</math>

De manière analogue, la fonction <math>Q</math> vérifie elle aussi une équation d'optimalité:
:<math>Q^*(s,a) = \sum_{s'\in S} [R(s,a,s') + \gamma \max_{a'\in A} Q^*(s',a')]T(s,a,s').</math>

===== Résolution des équations d'optimalité de Bellman =====
Les équations d'optimalité de Bellman ne sont pas linéaires, il faut donc abandonner l'idée de les résoudre algébriquement.
En revanche, l'opérateur <math>K</math> défini par
:<math>K(f)(s) = \max_{a\in A}\sum_{s'\in S} [R(s,a,s') + \gamma f(s)]T(s,a,s'),</math>
définit encore une contraction dont <math>V^*</math> est un point fixe.
La fonction de valeurs optimale peut donc à nouveau s'approcher par un processus itératif à convergence exponentielle.

===== Algorithmes de détermination de la politique optimale =====

====== Itération sur la valeur (VI) ======
La méthode itérative que nous venons de voir pour les équations d'optimalité de Bellman fournit un premier algorithme, appelé ''itération sur la valeur'' (VI: Value-Iteration) permettant de déterminer <math>\pi^*</math>.
Il suffit en effet de déterminer <math>V^*</math> avec une précision donnée, et on peut en déduire la politique optimale par:
:<math>\pi(s) 
= \arg\max_{a\in A} Q^*(s,a)  
= \arg\max_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^*(s')]T(s,a,s').</math>
Une difficulté dans cet algorithme est de déterminer la précision avec laquelle calculer <math>V^*</math> de manière à être sûr d'en déduire effectivement la politique optimale.

====== Itération sur la politique (PI) ======
Un autre algorithme, appelé *itération de la politique* (PI: Policy-Iteration) essaye d'obtenir la politique optimale sans nécessairement calculer "jusqu'au bout" les valeurs de <math>V^*</math>.
L'idée est de partir d'une politique quelconque <math>\pi_0</math>, puis d'alterner une phase d'évaluation, dans laquelle la fonction <math>V^{\pi_n}</math> est déterminée (avec une des techniques vues plus haut), avec une phase d'amélioriation, où l'on définit la politique suivante <math>\pi_{n+1}</math> par:
:<math>\pi_{n+1}(s) = \arg\max_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^{\pi_n}(s')]T(s,a,s').</math>.
Cet algorithme prend fin lorsqu'aucune évolution de la politique n'est observée, ie, lorsque <math>\pi_{n+1}(s)=\pi_n(s)</math> pour tout <math>s</math>.

Si dans l'algorithme précédent l'on utilise une méthode itérative pour évaluer <math>V^\pi</math>, alors se pose la question de savoir à quelle précision s'arrêter.
Ce problème n'en est en réalité pas un, car on peut montrer que même si l'on tronque l'évaluation de <math>V^\pi</math>, l'algorithme converge tout de même vers l'optimal.
À l'extrême, c'est-à-dire lorsqu'une seule itération est utilisée pour évaluer <math>V^\pi</math>, et après avoir réuni en une seule itération la phase d'amélioration et la phase d'évaluation, on retombe sur l'algorithme VI.

L'algorithme PI peut également se formuler dans les termes de la fonction d'états-actions <math>Q</math> plutôt que <math>V</math>.
On voit donc qu'un grand nombre de variantes peuvent être imaginées, tournant toutes autour d'un même principe général qui est schématisé à la figure ci-contre.
[[Image:IMG/policy-iteration.svg|alt=policy-iteration|Schéma général des algorithmes d'itération sur la politique]]

== Exemples ==

% TODO : trouver un exemple simple et discret?

=== Car on the Hill ===

% TODO : refaire la figure

Dans ce problème, une voiture se déplace sur une colline, l'objectif est d'atteindre le haut de la colline (situé à la position <math>p = 1</math>) aussi vite que possible, en gardant une vitesse entre -3 et 3, et une position supérieure à -1.

* Les états sont décrits par la position de la voiture (<math>p \in [-1, 1]</math>) et sa vitesse (<math>s \in [-3,3]</math>);
* Les actions sont les accélérations possibles de la voiture : <math>a \in \{-4, 4\}</math>;
* La fonction de transition est obtenue en faisant la somme des forces qui s'applique à la voiture;
* La fonction de récompense vaut :
** -1 si <math>p < 1</math> ou <math>s \in [-3, 3]</math>,
** 1 si <math>p \geq 1</math> et <math>s \not\in [-3, 3]</math>,
** 0 sinon.
[[Image:IMG/car-on-the-hill.png|alt=car-on-the-hill|Représentation de <math>Hill(p)</math> (la forme de la colline) et des forces que subie la voiture.]]

=== Pendule inversé? ===

== Les techniques batch ==

=== généralités (leo) ===
% principe experience replay
% principe fitting

=== les algos principaux ===
% à chaque fois, expliquer le principe, donner un pseudo-code grossier
% pointer article(s) qui l'utilisent... le plus le mieux
% si possible: pointer au max notre classification
==== kernel based (leo) ====

==== Fitted Q iteration (FQI) ====

% TODO : Traduction "FQI"

Cet algorithme permet de calculer une approximation de la fonction de valeurs optimale des états-actions <math>Q</math> à partir d'un jeu d'échantillons du MDP. Connaissant <math>Q</math>, il est alors possible de déduire la politique correspondante : <math>\pi(s)=\arg\max_{a\in A}Q(s,a)</math>. 

Étant donné un jeu d'échantillons <math>\mathcal{F}</math> (un ensemble de transitions de la forme <math>(s,a,r,s')</math>) et une approximation initiale <math>\hat{Q}^0</math> (en général <math>\hat{Q}^0(s,a) = 0</math>), l'algorithme procède de la manière suivante :

# Il crée une base de donnée d'apprentissage <math>P</math> constituée des couples entrée-sortie <math>(s,a) \to r+\gamma\max_{a' \in A}\hat{Q}^i(s,a)</math> pour chaque transition <math>(s,a,r,s') \in \mathcal{F}</math>.
# Il déduit une fonction <math>\hat{Q}^{i+1}</math> à partir de l'ensemble <math>P</math> en utilisant une méthode d'apprentissage supervisé. Cette fonction est une approximation de la fonction <math>Q^{i+1}</math> obtenue à la i+1<sup>ème</sup> itération de l'algorithme de programmation dynamique.
# Il incrémente <math>i</math> et retourne à la première étape tant qu'une condition d'arrêt n'est pas vérifiée.

Cet algorithme peut-être utilisé avec n'importe quelle méthode d'apprentissage supervisé, en particulier des méthodes non paramétriques, qui ne font aucune hypothèse sur la forme de <math>Q</math>. Il permet également de traiter des espaces d'états et d'actions continus. Sa convergence n'a cependant été démontrée que pour une certaine classe de méthodes d'apprentissage. Différentes conditions d'arrêt peuvent-être utilisées; en pratique, fixer le nombre d'itérations permet d'assurer la terminaison de l'algorithme, et ce quelque soit la méthode d'apprentissage utilisée.

Dans <ref name="FQI">{{en}} Damien Ernst, Pierre Geurts, Louis Wehenkel, «Tree-Based Batch Mode Reinforcement Learning», Journal of Machine Learning Research 6, 2005, p. 503–556</ref>, plusieurs problèmes ont été utilisés pour évaluer l'algorithme avec différentes méthodes d'apprentissage supervisé, et avec plusieurs types de batch (pure batch et growing batch). Sur ces problèmes, l'algorithme de fitted Q iteration est très performant, en particulier en utilisant des méthodes d'apprentissage non paramétriques (comme la [[méthode des k plus proches voisins]]).

% TODO : exemple d'application?

==== Itération moindres carrés de la politique (LSPI: Least-Squares Policy Iteration) (alex) ====
===== Description de l'algorithme =====
Cet algorithme est une adaptation de l'algorithme d'itération de la politique exprimé sur la fonction <math>Q</math> d'états-actions.
Il se focalise exclusivement sur l'évaluation de <math>Q</math>, et la politique n'y est jamais représentée explicitement, mais simplement déduite à la volée, par le choix glouton classique:
:<math>\pi(s)=\arg\max_{a\in A}Q(s,a)</math>

Par rapport à PI, LSPI opère deux grandes généralisations:

1. la fonction <math>Q</math> est exprimée comme une combinaison linéaire de fonctions <math>\phi_i</math>, <math>i=1,\dots,k</math>:
:<math>Q(s,a) = \sum_{i=1}^k w_i\phi_i(s,a).</math>
Les fonctions <math>\phi_i</math> forment donc une base d'un sous-espace \mathcal{Q} de l'espace des fonctions états-actions, choisie une fois pour toutes au départ.
Ainsi en interne, la fonction <math>Q</math> est simplement représentée par les <math>k</math> coefficients <math>w_i</math>.

Ce modèle possède un avantage: quels que soient le nombre d'états et d'actions, seuls <math>k</math> coefficients sont utilisés pour représenter l'ensemble des valeurs de <math>Q</math>, contre <math>|S|\times |A|</math> si l'on utilisait une représentation tabulaire classique.
Ceci permet de traiter efficacement des MDP possédant un grand nombre d'états et/ou d'actions.
On peut même, dans ce formalisme, considérer des espaces d'états ou d'actions continus.

L'inconvénient, en revanche, est que l'on est maintenant restreint aux fonctions de <math>\mathcal{Q}</math>.
Dans l'algorithme PI avec états-valeurs, la phase d'évaluation de la politique <math>\pi</math> consiste à déterminer le point fixe <math>Q^\pi</math> de l'opérateur
:<math> K(Q)(s, a) = \sum_{s'\in S} [R(s,a,s') + \gamma Q(s', \pi(s'))]T(s,a,s'),</math>
soit par la résolution d'un système linéaire, soit par calcul itératif sur <math>K</math>.

Or même si par définition <math>Q\in \mathcal{Q}</math>, on n'a pas en général <math>K(Q)\in\mathcal{Q}</math>. 
L'idée dans LSPI est alors de projeter (au sens des moindres carrés, d'où le nom de l'algorithme) la mise à jour <math>K(Q)</math> sur <math>\mathcal{Q}</math>:
:<math>K'(Q) = \mathcal{P}^\perp_\mathcal{Q}(K(Q)),</math>
et donc d'obtenir une approximation <math>\hat Q^\pi</math> de <math>Q^\pi</math> qui est stable par la règle de mise à jour suivie de la projection.
Dans 
<ref name="LSPI">{{en}} Michail Lagoudakis, Ronald Parr, «Least-Squares Policy Iteration», Journal of Machine Learning Research 4, 2003, p. 1107-1149</ref>
ce point fixe est obtenu par résolution d'un système linéaire.

2. La deuxième différence est que LSPI ne suppose pas le MDP connu; l'algorithme se base uniquement sur un jeu d'échantillons du MDP, donnés sous la forme de quadruplets <math>(s,a,r,s')</math>.
Ces échantillons peuvent être donnés directement au départ de l'algorithme (pure batch) ou progressivement (semi-batch ou en-ligne).
Concrètement, l'algorithme se contente d'effectuer les mises à jour précédentes sur le jeu d'échantillon.
On peut montrer qu'en fait cela revient à appliquer la mise à jour sur le véritable MDP, mais avec les probabilités de transitions <math>T(s,a,s')</math> obtenues empiriquement selon la distribution des échantillons. 
Ainsi dans l'hypothèse où cette distribution est conforme aux véritables probabilités de transition, le résultat converge asymptotiquement vers la véritable valeur <math>Q^\pi</math>, et donc finalement vers la fonction optimale <math>Q^*</math>.
On peut également remarquer que chaque échantillon contribue linéairement à chaque itération (il ajoute un terme dans la somme définissant <math>K</math> et l'opérateur de projection est linéaire), ce qui permet de mettre en place des optimisations lorsque les échantillons arrivent progressivement.

La figure ci-contre résume schématiquement l'algorithme LSPI.
[[Image:IMG/LS-policy-iteration.svg|alt=least-squares policy-iteration|L'algorithme d'itération moindres carrés sur la politique]]

===== Exemple d'application =====

Dans <ref name="LSPI"/>, plusieurs applications de l'algorithme LSPI sont proposées.
L'une d'elles, dans lequel l'algorithme est particulièrement performant notamment par rapport à un algorithme de [[Q-Learning]], est le problème de l'''équilibre et la conduite d'un vélo''
<ref name="">{{en}} Jette Randløv and Preben Alstrøm, «Learning to Drive a Bicycle Using Reinforcement Learning and Shaping», dans Proceedings of the Fifteenth International Conference on Machine Learning (ICML '98), 1998, Jude W. Shavlik (Ed.). Morgan Kaufmann Publishers Inc., San Francisco, CA, USA, 463-471.</ref>.

Dans ce problème l'apprenant connaît à chaque pas de temps l'angle et la vitesse angulaire du guidon, ainsi que l'angle, la vitesse et accélération angulaire de l'angle que forme le vélo avec la verticale.
Il doit alors agir quelle force rotatoire appliquer au guidon (choisie parmi trois possibilités), ainsi que le déplacement de son centre de masse par rapport au plan du vélo (choisie également parmi 3 possibilités), de manière d'une part à rester en équilibre (c'est-à-dire ici ne pas dépasser un angle vélo/sol de +/- 12°) et d'autre part atteindre une destination cible.

La mise en oeuvre est pûrement hors-ligne et consiste à observer le comportement d'un agent aléatoire pour collecter de l'ordre de quelques dizaines de milliers d'échantillons sous forme de trajectoires.
Les trajectoires collectées sont ensuite coupées après quelques pas de temps, pour ne garder que la partie intéressante avant qu'elles ne rentrent dans un scénario de chute inexorable. 
Les récompenses sont en lien avec la distance atteinte par rapport à l'objectif.
Quelques passes de l'algorithme sont ensuite effectuées sur ces échantillons («experience replay») et une convergence très rapide vers des stratégies viables est observée.

== Références ==
<!-- ne rien écrire ici, la mise en forme est automatique à partir des <ref> du document -->

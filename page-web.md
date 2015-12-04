{{Aide:Comment créer un article/brouillon}}
<!-- Merci d'écrire sous cette ligne  -->
% distinction model-based / model-free
% exemples où le batch est nécessaire

L''''apprentissage par renforcement hors ligne''' (ou '''batch''') est un cas particulier de l'[[apprentissage par renforcement]], qui est une classe de problèmes d'[[apprentissage automatique]] dont l'objectif est de déterminer à partir d'expériences une stratégie (ou politique) permettant à un agent de maximiser une récompense numérique au cours du temps.

Dans le cadre de l'apprentissage par renforcement purement hors ligne, l'agent ne peut pas interagir avec l'environnement : une base d'apprentissage lui est fournie au départ et il l'exploite pour apprendre une politique.
Constrastant avec les algorithmes en-ligne, où l'agent à la possibilité d'interagir comme bon lui semble avec l'environnement, les algorithmes hors-ligne tentent d'exploiter au maximum les exemples d'apprentissage dont ils disposent, sans compter uniquement sur la possibilité d'exploration.
Cette approche est donc particulièrement avantageuse quand il n'est pas possible d'effectuer des expériences ou lorsque ces expériences sont coûteuses (casse de matériel possible, obligation d'avoir recours à une assistance humaine pendant les expériences, etc). 
En général cependant, les techniques d'apprentissage par renforcement batch peuvent être utilisées dans un cadre plus large, où la base d'apprentissage peut évoluer au cours du temps. 
L'agent peut alors alterner entre des phases d'exploration et des phases d'apprentissage.
Les algorithmes hors-ligne sont en général des adaptations d'autres algorithmes comme le [[Q-Learning]], eux-mêmes inspirés par les algorithmes de programmation dynamique résolvant les [[Processus_de_décision_markovien|MDP]]s.


== Classification des batchs (leo) ==
% selon interaction ou pas (IE), la quantité d'expérience utilisée (QE)
% IE=non QE=tout "pure batch"
% IE=oui QE=tout growing batch
% IE=oui QE=partielle semi-batch

== Modèle théorique sous-jacent ==
La modélisation classique de l'apprentissage par renforcement (en ligne ou hors ligne) utilise la notion sous-jacente de [[Processus_de_décision_markovien|processus de décision markovien]] (MDP).
Rappelons-en les principales notations: 
* le MDP est noté <math>\{S, A, T, R\}</math>; 
* la [[Processus_de_décision_markovien#Politiques, fonctions de valeurs et équations de Bellman|fonction de valeurs]] des états <math>V</math>;
* celle des valeurs des états-actions <math>Q</math>;
* les politiques sont notées <math>\pi</math>.

== Exemples ==

=== Gridworld ===

TODO : figure

Gridworld est un exemple classique de problème pouvant être résolu par apprentissage par renforcement. Un agent se déplace dans une grille, et doit atteindre une case cible. Une fois que cette case est atteinte, il est téléporté à son point de départ. L'instance représentée ci-contre peut-être modélisé de la manière suivante :

* Les états sont les 25 cellules de la grille.
* Les actions sont les déplacements possibles de l'agent, c'est à dire aller en haut, en bas, à gauche ou à droite.
* La fonction de transition <math>T(s,a,s')</math> vaut 1 si 
** <math>s</math> est la case cible, et <math>s'</math> est la case d'origine de l'agent, ou
** <math>s'</math> est l'état obtenu en effectuant l'action <math>a</math> (par exemple <math>a</math> est "aller à droite" et <math>s'</math> est la cellule à droite de <math>s</math>).
* La fonction de récompense <math>R(s,a,s')</math> vaut 1 si <math>s</math> est la case cible.

Compte tenu de la simplicité du modèle, ce problème peut être résolu en utilisant des techniques plus simple que l'apprentissage par renforcement hors-ligne (voir [[processus de décision markovien#Algorithmes]]).

=== Mountain Car ===

[[File:Mcar.png|thumb|The Mountain Car Problem]]

Le [[Moutain car]] est un problème classique de la littérature de l'apprentissage par renforcement, dans lequel une voiture doit atteindre le haut d'une colline.

* Les états sont décrits par la position de la voiture (<math>p \in [-1.2, 0.6]</math>) et sa vitesse (<math>s \in [-0.07,0.07]</math>);
* Les actions sont les accélérations possibles de la voiture : accélérer vers l'avant, vers arrière, ou rester en roue libre.
* La fonction de transition est obtenue en effectuant la somme des forces qui s'applique à la voiture;
* La fonction de récompense vaut <math>hauteur - 1</math>

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

Cet algorithme permet de calculer une approximation de la fonction de valeurs optimale des états-actions <math>Q</math> à partir d'un jeu d'échantillons du MDP. Connaissant <math>Q</math>, il est alors possible de déduire la politique correspondante : <math>\pi(s)=\arg\max_{a\in A}Q(s,a)</math>. Il repose sur le même principe que l'algorithme d'[[Processus_de_décision_markovien#D.C3.A9terminer_la_politique_optimale:_algorithme_d.27It.C3.A9ration_sur_la_valeur_.28VI.29|itération sur la valeur]] des états-actions, qui consiste à calculer de manière itérative le point fixe de l'équation de Bellman :

<math>Q^{i+1}(s,a) = \sum_{s'\in S} [R(s,a,s') + \gamma \max_{a'\in A} Q^i(s',a')]T(s,a,s')</math>

On cherche à calculer ce point fixe à partir d'un jeu d'échantillons <math>\mathcal{F}</math> (un ensemble de transitions de la forme <math>(s,a,r,s')</math>). L'algorithme procède de la manière suivante :

# Il crée une base de donnée d'apprentissage <math>P</math> constituée des couples entrée-sortie <math>(s,a) \to r+\gamma\max_{a' \in A}\hat{Q}^i(s,a)</math> pour chaque transition <math>(s,a,r,s') \in \mathcal{F}</math>.
# Il déduit une fonction <math>\hat{Q}^{i+1}</math> à partir de l'ensemble <math>P</math> en utilisant une méthode d'apprentissage supervisé. Cette fonction est une approximation de la fonction <math>Q^{i+1}</math> obtenue à la i+1<sup>ème</sup> itération de l'algorithme d'[[Processus_de_décision_markovien#D.C3.A9terminer_la_politique_optimale:_algorithme_d.27It.C3.A9ration_sur_la_valeur_.28VI.29|itération sur la valeur]] des états-actions.
# Il incrémente <math>i</math> et retourne à la première étape tant qu'une condition d'arrêt n'est pas vérifiée.

Cet algorithme peut-être utilisé avec n'importe quelle méthode d'apprentissage supervisé, en particulier des méthodes non paramétriques, qui ne font aucune hypothèse sur la forme de <math>Q</math>. Cela permet d'obtenir des approximations précises indépendamment de la taille des espaces d'états et d'actions, y compris dans le cas d'espaces continus. Sa convergence n'a été démontrée que pour une certaine classe de méthodes d'apprentissage. Différentes conditions d'arrêt peuvent-être utilisées; en pratique, fixer le nombre d'itérations permet d'assurer la terminaison de l'algorithme, et ce quelque soit la méthode d'apprentissage utilisée.

Dans <ref name="FQI">{{en}} Damien Ernst, Pierre Geurts, Louis Wehenkel, «Tree-Based Batch Mode Reinforcement Learning», Journal of Machine Learning Research 6, 2005, p. 503–556</ref>, plusieurs problèmes ont été utilisés pour évaluer l'algorithme avec différentes méthodes d'apprentissage supervisé, et avec plusieurs types de batch (pure batch et growing batch). Sur ces problèmes, l'algorithme de fitted Q iteration est très performant, en particulier en utilisant des méthodes d'apprentissage non paramétriques (comme la [[méthode des k plus proches voisins]]).

==== Itération moindres carrés de la politique (LSPI: Least-Squares Policy Iteration) ====
===== Description de l'algorithme =====
Cet algorithme est une adaptation de l'algorithme d'[[Processus_de_décision_markovien#Déterminer la politique optimale: algorithme d'Itération sur la politique (PI)|itération de la politique]] exprimé sur la fonction <math>Q</math> d'états-actions.
Il se focalise exclusivement sur l'évaluation de <math>Q</math>, et la politique n'y est jamais représentée explicitement, mais simplement déduite à la volée, par le choix [[Algorithme glouton|glouton]] classique:
:<math>\pi(s)=\arg\max_{a\in A}Q(s,a)</math>

Par rapport à PI, LSPI opère deux grandes généralisations:

1. la fonction <math>Q</math> est exprimée comme une combinaison linéaire de fonctions <math>\phi_i</math>, <math>i=1,\dots,k</math>:
:<math>Q(s,a) = \sum_{i=1}^k w_i\phi_i(s,a).</math>
Les fonctions <math>\phi_i</math> forment donc une base d'un sous-espace <math>\mathcal{Q}</math> de l'espace des fonctions états-actions, choisie une fois pour toutes au départ.
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
[[File:Least Squares Policy Iteration.svg|right|L'algorithme général d'itération de la politique, avec projection moindres carrés.]]

2. La deuxième différence est que LSPI ne suppose pas le MDP connu; l'algorithme se base uniquement sur un jeu d'échantillons du MDP, donnés sous la forme de quadruplets <math>(s,a,r,s')</math>.
Ces échantillons peuvent être donnés directement au départ de l'algorithme (pure batch) ou progressivement (semi-batch ou en-ligne).
Concrètement, l'algorithme se contente d'effectuer les mises à jour précédentes sur le jeu d'échantillon.
On peut montrer qu'en fait cela revient à appliquer la mise à jour sur le véritable MDP, mais avec les probabilités de transitions <math>T(s,a,s')</math> obtenues empiriquement selon la distribution des échantillons. 
Ainsi dans l'hypothèse où cette distribution est conforme aux véritables probabilités de transition, le résultat converge asymptotiquement vers la véritable valeur <math>Q^\pi</math>, et donc finalement vers la fonction optimale <math>Q^*</math>.
On peut également remarquer que chaque échantillon contribue linéairement à chaque itération (il ajoute un terme dans la somme définissant <math>K</math> et l'opérateur de projection est linéaire), ce qui permet de mettre en place des optimisations lorsque les échantillons arrivent progressivement.

La figure ci-contre résume schématiquement l'algorithme LSPI.

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

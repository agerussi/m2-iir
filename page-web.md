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

## Modèle de décision de Markov
Les processus de décision de Markov (Markov Decision Processes (MDP) en anglais) forment le modèle sous-jacent à tout algorithme d'apprentissage par renforcement, qu'il soit en ligne ou hors ligne.

Dans ce modèle, l'environnement d'apprentissage est modélisé par:
- un ensemble d'états $S$, qui peut être fini, dénombrable ou continu; cet ensemble définit l'environnement tel que perçu par l'apprenant (dans le cas d'un robot, on peut voir cela comme l'ensemble produit des valeurs de ses différents capteurs);
- un ensemble d'actions $A$, qui peut être fini, dénombre ou continu et dans lequel l'apprenant choisi les interactions qu'il effectue avec l'environnement (dans le cas d'un robot on peut voir cela comme l'ensemble produit des paramètres de ses différentes commandes);
- une fonction de transition $T:S\times A\times S\to[0;1]$; cette fonction définit l'effet des actions de l'apprenant sur l'environnement: $T(s, a, s')$ représente la probabilité de se retrouver sans l'état $s'$ en effectuant l'action $a$, sachant que l'on était l'instant d'avant dans l'état $s$.
$T$ ainsi définie représente le cas le plus général; dans un environnement déterministe, on aura plutôt $T:S\times A\to S$.
- une fonction de récompense $R:S\times A\times S\times\RR\to[0;1]$; elle définit la récompense (positive ou négative) reçue par l'agent: $R(s,a,s',v)$ est la probabilité d'obtenir une récompense $v$ pour être passé de l'état $s$ à $s'$ en ayant effectué l'action $a$.
Ici encore cette définition est très générale, bien souvent on se contentera par exemple des cas particuliers suivants:
-- $R:S\times A\times S\to\RR$ (récompense déterministe, c'est le choix que nous adopterons dans la suite);
-- $R:S\times A\to\RR$ (récompense déterministe rattachée à l'action en ignorant son résultat);
-- $R:S\to\RR$ (récompense déterministe rattachée à un état donné).
 
NB: nous ne considérons ici que les modèles dans lesquels le temps est discrétisé, c'est-à-dire que la "trajectoire" d'un apprenant dans l'environnement est décrivable par {\em une suite} d'états $s_t$ ($t\in\NN$), et non par {\em une fonction} $s(t)$ avec $t\in\RR$.
De même on notera $a_t$ la suite des actions prises par l'agent.
On pourra consulter [ref] pour une description des MDP à temps continu.

## Politiques, fonctions de valeur et équations de Bellman
La politique d'un agent est la manière avec laquelle il choisit l'action qu'il va effectuer lorsqu'il se trouve dans un état donné.
Formellement il s'agit donc d'une fonction $\pi: S\to A$ dans le cas d'une politique déterministe (ce que nous supposerons dans la suite), ou $\pi:S\times A\to[0;1]$ dans le cas stochastique: on a donc $a_t=\pi(s_t)$.
Il est possible de généraliser la notion de politique en la faisant dépendre non uniquement de l'état présent, mais de l'ensemble de la trajectoire passée de l'agent dans l'environnement, et donc en particulier du temps qui passe [ref à trouver].

Afin que l'agent puisse choisir une politique, il faut lui fournir un critère de choix.
Ce critère est lié à la fonction de récompense $R$. 
Notons $r_t=R(s_t, \pi(s_t), s_{t+1})$ la récompense effective obtenue au temps $t$ (après avoir effectué son action) par l'agent qui suit la politique $\pi$.
Voici plusieurs choix possibles de critères pour l'agent:
- maximiser $E\left(\sum_{t=0}^h r_t\right)$: espérance de la somme des récompenses à un horizon fini;
- maximiser $\lim_{h\to+\infty} E\left({1\over h}\sum_{t=0}^h r_t\right)$: récompense moyenne à long terme;
- maximiser $E\left(\sum_{t=0}^\infty \gamma^t r_t\right)$ ($0\leq \gamma<1$): récompense amortie à horizon infini.
Le dernier critère est courant et c'est celui que nous adoptons dans la suite.
La valeur de $\gamma$ permet de définir l'importance que l'on donne au futur.
Quand $\gamma=0$ nous sommes face à un agent «pessimiste» qui ne cherche qu'à optimiser son gain immédiat.
À l'opposé si $\gamma\to 1$, l'agent est «optimiste» puisqu'il tient de plus en plus sérieusement compte du futur lointain.

Lorsqu'une politique et un critère sont déterminés, deux fonctions peuvent être définies:
- $V^\pi: S\to\RR$: c'est la fonction de valeur des états; $V\pi(s)$ représente le gain (selon le critère adopté) engrengé par l'agent s'il démarre à l'état $s$ et applique ensuite la politique $\pi$ ad infinitum.
- $Q^\pi: S\times A\to\RR$: c'est la fonction de valeur des états-actions; $Q^\pi(s,a)$ représente le gain engrengé par l'agent s'il démarre à l'état $s$ et commence par effectuer l'action $a$, avant d'appliquer ensuite la politique $\pi$ ad infinitum.
Les deux fonctions sont intimement liées. 
On a toujours $V^\pi(s) = Q^\pi(s, \pi(s))$ et, dans le cas du gain amorti à horizon infini:
$$ Q^pi(s, a) &= \sum_{s'\in S} [R(s,a,s') + \gamma V^\pi(s')]T(s,a,s'). $$
Cette dernière relation montre que la fonction $V^{\pi}$ vérifie une relation de récurrence appelée équation de Bellman:
$$ V^pi(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^\pi(s')]T(s,\pi(s),s'). $$

### Résolution de l'équation de Bellman
Une politique étant fixée, l'équation de Bellman peut résoudre d'au moins deux manières, permettant donc de déterminer les valeurs de $V^\pi$, et par suite, celles de $Q^\pi$ également.
- on peut déjà remarquer que, dans le cas où le nombre d'états $n$ est fini, l'équation de Bellman cache en fait un système linéaire de $n$ équations à $n$ inconnues. 
On peut donc le résoudre, une fois traduit en une équation matricielle, par une technique telle que le pivot de Gauss.
- on peut également remarquer qu'en posant
$$K(f)(s) = \sum_{s'\in S} [R(s,\pi(s),s') + \gamma f(s)]T(s,\pi(s),s'),$$
on définit un opérateur $K$ pour lequel $V^\pi$ est un point fixe.
On peut montrer que $K$ est une contraction, ce qui garantit d'une part l'existence d'un unique point fixe, et d'autre part que la suite récurrence $V_{n+1} = K(V_n)$ converge vers ce point fixe exponentiellement vite.

### Equations d'optimalité de Bellman
Le but de l'agent est de trouver la politique optimale $\pi^*$ qui lui permet de maximiser son gain, c'est-à-dire celle qui vérifie, pour tout état $s\in S$, $V^{\pi^*}(s)\geq V^{\pi}(s)$ quelle que soit l'autre politique $\pi$.
On peut montrer que la fonction de valeurs optimale $V^*$ vérifie l'équation d'optimalité de Bellman:
$$V^*(s) = \max_{a\in A}\sum_{s'\in S} [R(s,\pi(s),s') + \gamma V^*(s')]T(s,a,s').$$

De manière analogue, la fonction $Q$ vérifie elle aussi une équation d'optimalité:
$$Q^*(s,a) = \sum_{s'\in S} [R(s,a,s') + \gamma \max_{a'\in A} Q^*(s',a')]T(s,a,s').$$

#### Résolution des équations d'optimalité de Bellman
Les équations d'optimalité de Bellman ne sont pas linéaires, il faut donc abandonner l'idée de les résoudre algébriquement.
En revanche, l'opérateur $K$ défini par
$$K(f)(s) = \max_{a\in A}\sum_{s'\in S} [R(s,a,s') + \gamma f(s)]T(s,a,s'),$$
définit encore une contraction dont $V^*$ est un point fixe.
La fonction de valeurs optimale peut donc à nouveau s'approcher par un processus itératif à convergence exponentielle.

#### Algorithmes de détermination de la politique optimale.
TODO: revoir cette section car il semble y avoir une différence entre le cours de A2DI et le bouquin. 
TODO: De plus il pourrait être intéressant dans cette section de présenter un algo purement en ligne pour ensuite pouvoir se baser dessus dans les cas batchs.

La méthode itérative que nous venons de voir pour les équations d'optimalité de Bellman fournit un premier algorithme permettant de déterminer $\pi^*$.
Il suffit en effet de déterminer $V^*$ avec une précision donnée et suffisante, dont on peut déduire la politique optimale par:
$$\pi(s) 
= \argmax_{a\in A} Q^*(s,a)  
= \argmax_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^*(s')]T(s,a,s').$$
Cet algorithme est appelé {\em algorithme d'itération de la valeur} (value-iteration).

Un autre algorithme, appelé {\em itération de la politique} (policy-iteration) essaye d'obtenir la politique optimale sans nécessairement calculer "jusqu'au bout" les valeurs de $V^*$.
L'idée est de partir d'une politique quelconque $\pi_0$, puis d'alterner une phase d'évaluation, dans laquelle la fonction $V^{\pi_n}$ est déterminée (avec une des techniques vues plus haut), avec une phase d'amélioriation, où l'on définit la politique suivante $\pi_{n+1}$ par:
$$\pi_{n+1}(s) = \argmax_{a\in A} \sum_{s'\in S} [R(s,a,s') + \gamma V^{\pi_n}(s')]T(s,a,s').$$.
Cet algorithme prend fin lorsqu'aucune évolution de la politique n'est observée, ie, lorsque $\pi_{n+1}(s)=\pi_n(s)$ pour tout $s$.



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


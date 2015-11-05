# Introduction (lucas)
## qu'est-ce que RL ?
## distinction on-line/off-line
## distinction model-based / model-free

## définir le pure-online, et donc le batch par opposition
## exemples où le batch est nécessaire

# Classification des batchs (leo)
## selon interaction ou pas (IE), la quantité d'expérience utilisée (QE)
## IE=non QE=tout "pure batch"
## IE=oui QE=tout growing batch
## IE=oui QE=partielle semi-batch

# Le modèle MDP (alexandre)
## états, actions, récompenses, proba de transition...
## fonction V et Q
## équation bellman: V et Q = point fixe d'un opérateur
## méthode de résolution = itération de l'opérateur (soit sur V soit sur Q)... est-ce équivalent ?)

# Exemples (lucas)
## présenter la modélisation sur deux trois exemples:
## dire quels états, quelles actions...

# Les techniques batch

## généralités (leo)
## principe experience replay
## principe fitting

## les algos principaux
## à chaque fois, expliquer le principe, donner un pseudo-code grossier
## pointer article(s) qui l'utilisent... le plus le mieux
## si possible: pointer au max notre classification
### kernel based (leo)
### fitted Q iteration (lucas)
### least-squares policy iteration (alex)
### monte-carlo ??

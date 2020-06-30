R Notebook
================

# Gérer les bibliothèques

``` r
# Installer une bibliothèque (guillemets obligatoires)
# Equivalent python de pip install xxx
# Nécessaire que lors de la 1e utilisation, on peut ensuite s'en passer
install.packages("tidyverse")

# Charger une bibliothèque
# Equivalent python de import xxx
library(tidyverse)
```

# Opérations de base sur les dataframes

``` r
# Exemples de dataframes automatiquement disponibles après chargement de tidyverse
mpg
diamonds

# Nombre de lignes d'un dataframe
nrow(mpg)

# Nombre de colonnes d'un dataframe
ncol(mpg)

# Accéder aux valeurs d'une colonne avec $
#mon_dataframe$ma_colonne
mpg$drv

# Afficher les valeurs uniques d'une colonne
unique(mpg$cyl)
```

# Les types de données

``` r
# Convertir en facteur avec factor()
factor(mpg$drv)
```

# Les collections (ensembles de données)

``` r
# Vecteur déclaré avec c()
# Contient une liste de valeurs de même type (équivalent d'une Series pandas)
# Impossible de palcer un vecteur dans un vecteur
c(10, 30, 50, 12)
c('A', 'D', 'K')

# Générer un vecteur à partir d'une suite de nombres
1:5 # équivalent à c(1, 2, 3, 4, 5)

# Accès une valeur
vecteur <- c('A', 'D', 'K', 'F')
vecteur[1] # renvoie "A"
vecteur[3] # renvoie "K"
vecteur[1:3] # renvoie un vecteur contenant les 3 premiers éléments
vecteur[-2] # renvoie tous les éléments sauf le 2e
vecteur[-c(1, 3)] # renvoie tous les éléments sauf le 1e et le 3e (donc le 2e et 4e)

# Vecteur nommé
vecteur <- c("a" = 7, "b" = 18)
# permet d'accéder aux éléments par position ou par nom
vecteur["b"] # équivalent de vecteur[2]

# Liste déclarée avec list()
# Peut contenir des valerus de différents types
# Possible d'imbriquer des listes dans des listes
ma_liste <- list(10, "abcd", TRUE)

# Liste nommée
ma_liste <- list("a" = 10, "b" = "abcd", "k" = TRUE)

# Accéder aux éléments d'une liste
# même principe qu'avec les vecteur
ma_liste[2] # équivalent de ma_liste["b"]

# L'accès à un élément d'une liste renvoie une liste !
# sauf avec [[]] : 
ma_liste[2] # renvoie une liste avec un seul élément
ma_liste[[2]] # renvoie la valeur seule
ma_liste[["b"]] # renvoie la valeur seule également

# Si la valeur est nommée et qu'on souhaite l'extraire seule on peut plus simplement faire :
ma_liste$b # équivalent à ma_liste[["b"]] ou à ma_liste[[2]]

# Dataframe
# cf plus tard
```

# Opérations sur une liste/vecteur/colonne de dataframe

``` r
# Nombre de valeurs
length(mpg$drv)

# Liste des valeurs uniques
unique(mpg$drv)
unique(mpg$displ)

# Variables quantitatives
min(mpg$displ)
max(mpg$displ)
mean(mpg$displ)
median(mpg$displ)

# S'il y a des valeurs manquantes, le résultat sera NA sauf si na.rm=TRUE
# Valable pour la plupart des fonctions agissant sur une liste/vecteur/colonne
library(nycflights13)
min(flights$dep_time) # renvoie NA
min(flights$dep_time, na.rm=T) # renvoie le minimum

# Statistiques de base pour une variable quantitative
summary(mpg$displ)
# Statistiques de base pour toutes les variables quanti d'un dataframe
summary(mpg)

# Variables qualitatives
# Effectif par catégorie
table(mpg$drv)

# Tableau croisé
table(mpg$drv, mpg$year)
```

# Graphiques avec ggplot

``` r
# Déclaration d'un graphique avec 
ggplot(data = mon_dataframe)

# Puis on ajoute autant de geom que nécessaire
ggplot(data = mon_dataframe) +
  geom_bar(...) +
  geom_point(...)

# Les geoms contiennent un mapping d'éléments esthétiques sur des colonnes du dataframe
ggplot(data = mpg) +
  geom_point(mapping = aes(x=hwy, y=cyl, color=drv))

# La liste des éléments esthétiques d'un geom est dispo
# Dans son aide (rubrique aesthetics)
?geom_point
# Dans la cheatcheet de ggplot (menu Help > Cheatsheets > Datavisualization with ggplot2)
# Et sur la référence en ligne de ggplot : https://ggplot2.tidyverse.org/reference/index.html


# La plupart des geoms précalculent des valeurs
# Ex : geom_bar fait un count de chaque catégrie mise en X
# On accède aux valeurs calculées via ..ma_valeur.. ou stat(ma_valeur)
# Ex avec le count calculé par geom_bar

ggplot(diamonds) +
  geom_bar(aes(x=cut, y=stat(count)))

# La liste des valeurs calculées par un geom est dispo dans son aide, rubrique Computes variables

# Certains geom possèdent des positions
# Ex de geom_bar :

# Identity = toutes les barres commencent à y=0 (pas très intéressant)
ggplot(diamonds) +
  geom_bar(aes(x=cut, color=clarity), alpha=0.1, position="identity")

# stack = barres empilées (par défaut)
ggplot(diamonds) +
  geom_bar(aes(x=cut, fill=clarity), position="stack")

# dodge = barres côte à côte
ggplot(diamonds) +
  geom_bar(aes(x=cut, fill=clarity), position="dodge")

# Ex avec geom_point et jitter : ajoute un alea sur x et y
# utile pour démasquer les points qui seraient superposés
ggplot(mpg) +
  geom_point(aes(x=displ, y=hwy), position="jitter")

# Equivalent de 
ggplot(mpg) +
  geom_jitter(aes(x=displ, y=hwy))

# Notez que le jitter ajoute des valeurs aléatoires sur les positions en X et Y
# et que chaque exécution du graphique donnera des résultats légèrement différents
# Pour forcer les mêmes valeurs aléatoires à chaque exécution, on peut déclarer
# un seed (point de départ pour les valeurs aléatoires) via
set.seed(1234)
ggplot(mpg) +
  geom_jitter(aes(x=displ, y=hwy))


# Inverser les axes X et Y avec coord_flip
ggplot(mpg, aes(x=class, y=hwy)) +
  geom_boxplot() +
  coord_flip()

# Passer en coordonnées polaires avec coord_polar
ggplot(mpg, aes(x=class, y=hwy)) +
  geom_boxplot() +
  coord_polar()
```

# Travailler avec MySQL

``` r
# Installer RMySQL (nécessaire que la 1e fois)
install.packages("RMySQL")

# Connexion à la base
library(RMySQL)
library(tidyverse)
con <- dbConnect(MySQL(), host="localhost", user="root", password="root", dbname="evaluation")

# Accès à une table
tbl(con, "question")

# On peut chainer avec les opérations classiques
tbl(con, "reponse") %>% 
  filter(!is.na(score)) %>% 
  head(10)

# Si on veut stocker les données localement il faut finir par un collect()
data <- tbl(con, "reponse") %>% 
  filter(!is.na(score)) %>% 
  head(10) %>% 
  collect()

# Tout ce qui est placé avant collect est converti en équivalent SQL
# On peut afficher cet équivalent avec show_query()
tbl(con, "reponse") %>% 
  filter(!is.na(score)) %>% 
  head(10) %>% 
  show_query()

# On peut aussi exécuter nos propres requêtes avec sql() :
tbl(con, sql("SELECT * FROM question WHERE id < 10"))



# Pour insérer des valeurs dans la requête de façon sécurisée :
#♥ on définit les variables nécessaires
id_min <- 3
id_max <- 10

# On prépare la requête à l'aide de glue_sql, les variables entre  {} seront insérées de façon protégées
library(glue)
req <- glue_sql("SELECT * FROM question WHERE id <= {id_max} AND id >= {id_min}", .con = con)

# Puis on utilise cette requête via tbl() + sql() comme auparavent
tbl(con, sql(req))
```

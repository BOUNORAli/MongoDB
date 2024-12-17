# Cours : Introduction à CouchDB et au MapReduce

## Contexte et objectifs

Dans le cadre de ce cours sur les bases de données NoSQL, nous avons déjà découvert Redis (une base clé-valeur en mémoire) et MongoDB (une base de documents orientée JSON). Nous avons appris à stocker, interroger et manipuler des données non structurées ou semi-structurées, ainsi qu’à comprendre les avantages et inconvénients des systèmes NoSQL par rapport aux SGBD relationnels.

Dans le cours précédent sur MongoDB, **nous** avons notamment vu comment utiliser l’agrégation (pipeline d’agrégation) pour effectuer des traitements complexes sur les données. À présent, nous allons nous concentrer sur **CouchDB**, une base de données documentaire distribuée, et sur la manière dont elle exploite le modèle **MapReduce** pour l’analyse et l’agrégation de données. À l’instar de ce que nous avons déjà découvert avec Redis et MongoDB, CouchDB manipule des documents au format JSON, mais propose une interaction par API REST (HTTP) et intègre son propre moteur MapReduce.

### À l’issue de ce cours, nous serons capables de :

- Comprendre les principes de base de CouchDB : installation, interface, interaction via HTTP.
- Réaliser des opérations CRUD (Create, Read, Update, Delete) sur des documents JSON dans CouchDB.
- Importer et gérer des collections de documents.
- Définir et exécuter des fonctions MapReduce dans CouchDB pour produire des vues agrégées.
- Comprendre l’intérêt du modèle MapReduce dans un contexte distribué et comparer ses logiques avec celles déjà abordées dans MongoDB (agrégations, pipelines) et Redis (opérations simples sur données en mémoire).

---

## 1. Présentation de CouchDB

### 1.1 Qu’est-ce que CouchDB ?

CouchDB est un système de gestion de bases de données NoSQL orienté documents. Chaque document est stocké sous forme JSON, sans schéma imposé. Parmi ses caractéristiques :

- **Open source** : Soutenu par la fondation Apache.
- **API RESTful** : Toutes les opérations se font via des requêtes HTTP standard.
- **Document Store** : Comme MongoDB, CouchDB stocke des données sous forme de documents JSON.
- **Distribution et Tolérance aux pannes** : Conçu pour être distribué, CouchDB gère la réplication et permet la montée en charge horizontale.

Par rapport à Redis (un store clé-valeur en mémoire) et MongoDB (un store documentaire avec un langage de requête spécifique), CouchDB se distingue en proposant une interaction purement REST, et un mécanisme MapReduce intégré pour l’agrégation des données.

### 1.2 Installation et Accès

Nous pouvons installer CouchDB localement ou via Docker. Par exemple, avec Docker :

```bash
docker run -d --name couchdbdemo \
    -e COUCHDB_USER=Ali -e COUCHDB_PASSWORD=Bounor \
    -p 5984:5984 couchdb
```

L’interface graphique Fauxton, accessible via `http://localhost:5984/_utils`, nous permet d’administrer et d’explorer la base. Nous pouvons également utiliser `curl` ou n’importe quel client HTTP (comme Postman) pour manipuler nos données.

---

## 2. Interactions avec CouchDB via les verbes HTTP

Contrairement à MongoDB, où nous utilisons des requêtes JavaScript ou l’agrégation interne, CouchDB s’appuie sur les verbes HTTP standard :

- **GET** : Récupérer une ressource (un document, la liste des bases, etc.)
- **PUT** : Créer ou remplacer une ressource (une base, un document avec un ID défini)
- **POST** : Ajouter une ressource sans ID prédéfini, ou envoyer une série de documents
- **DELETE** : Supprimer une ressource (un document par son ID et sa révision)

### 2.1 Créer une base de données

```bash
curl -X PUT http://Ali:Bounor@localhost:5984/films
```

Cette requête crée une base nommée `films`.

### 2.2 Insérer des documents

- **Insertion avec un ID connu (PUT)** :

```bash
curl -X PUT http://Ali:Bounor@localhost:5984/films/doc1 \
    -H "Content-Type: application/json" \
    -d '{"titre":"Inception","annee":2010}'
```

- **Insertion sans ID (POST)** :

```bash
curl -X POST http://Ali:Bounor@localhost:5984/films \
    -H "Content-Type: application/json" \
    -d '{"titre":"Interstellar","annee":2014}'
```

### 2.3 Lecture d’un document (GET)

```bash
curl -X GET http://Ali:Bounor@localhost:5984/films/doc1
```

Retourne le document `doc1` en JSON.

### 2.4 Mise à jour et suppression (PUT et DELETE)

Pour mettre à jour, nous devons connaître la dernière révision `_rev` du document. Pour supprimer, un `DELETE` avec l’`_rev` est nécessaire :

```bash
curl -X DELETE "http://Ali:Bounor@localhost:5984/films/doc1?rev=1-..."
```

### 2.5 Importation en masse

Nous pouvons insérer plusieurs documents à la fois via `_bulk_docs` :

```bash
curl -X POST http://Ali:Bounor@localhost:5984/films/_bulk_docs \
     -H "Content-Type: application/json" \
     -d @films_couchdb.json
```

---

## 3. Introduction à MapReduce dans CouchDB

Au cours précédent sur MongoDB, nous avons vu comment utiliser l’agrégation (pipeline d’agrégation) pour regrouper et analyser les données. CouchDB propose un mécanisme différent, inspiré du modèle **MapReduce**, largement utilisé dans le Big Data (notamment Hadoop).

### 3.1 Le principe de MapReduce

- **Map** : S’applique à chaque document indépendamment et émet une ou plusieurs paires (clé, valeur) intermédiaires.  
- **Reduce** : Combine les valeurs ayant la même clé intermédiaire pour calculer une agrégation (somme, moyenne, décompte, etc.).

Ce processus permet de paralléliser le traitement sur plusieurs nœuds. Dans un contexte distribué, chaque nœud applique la fonction map sur un sous-ensemble de données, puis les résultats sont agrégés par reduce.

### 3.2 Fonctions Map et Reduce dans CouchDB

CouchDB stocke ces fonctions (en JavaScript) dans des « vues ». Une vue comprend une fonction `map`, et éventuellement une fonction `reduce`. L’originalité de CouchDB est que la fonction `reduce` n’est pas obligatoire ; nous pouvons visualiser le résultat intermédiaire après map, ce qui est moins courant dans d’autres systèmes.

#### Exemple : Compter le nombre de films par année

- **Fonction map** (définie dans Fauxton ou via l’API) :

```javascript
function(doc) {
  if (doc.annee && doc.titre) {
    emit(doc.annee, 1);
  }
}
```

Ici, chaque film émet une paire (annee, 1).

- **Fonction reduce** (pour compter) :

```javascript
function(keys, values, rereduce) {
  return sum(values);
}
```

Une fois la vue définie, nous pouvons activer la réduction. CouchDB regroupera tous les documents par année et en fera la somme, nous obtenons ainsi le nombre de films par année.

#### Un autre exemple : Compter le nombre de films par acteur

Supposons que chaque document film contienne un tableau `actors`. Nous voulons compter le nombre de films par acteur :

- **Map** :

```javascript
function(doc) {
  if (doc.actors && doc.actors.length > 0) {
    for (var i=0; i<doc.actors.length; i++) {
      var acteur = doc.actors[i].first_name + " " + doc.actors[i].last_name;
      emit(acteur, 1);
    }
  }
}
```

- **Reduce** (même que précédemment) :

```javascript
function(keys, values, rereduce) {
  return sum(values);
}
```

CouchDB va alors nous donner, pour chaque acteur, le nombre total de films dans lesquels il a joué.

### 3.3 Comparaison avec MongoDB et Redis

- **MongoDB** : Dispose d’un langage de requête riche, de pipelines d’agrégation, plus proches de SQL. Le pipeline d’agrégation permet des transformations complexes, du filtrage, du groupement, etc.
- **Redis** : Système clé/valeur très rapide, moins orienté requête analytique. Nous nous appuyons sur des opérations de base (listes, ensembles) et non sur un mécanisme MapReduce.
- **CouchDB** : Propose MapReduce pour agréger les données distribuées. C’est un paradigme moins déclaratif que MongoDB. Nous « codons » la logique d’agrégation, ce qui offre flexibilité et parallélisation dans un système distribué.

---

## 4. Avantages et Inconvénients de l’approche CouchDB/MapReduce

- **Avantages** :
  - Grande flexibilité dans la définition des vues (MapReduce en JavaScript).
  - Parfaitement adapté à un environnement distribué et scalable.
  - Pas de schéma strict, très adapté à des données hétérogènes.
  
- **Inconvénients** :
  - Risque d’incohérences si nous ne maîtrisons pas la logique de schéma.
  - Pas de langage de requête aussi riche que celui de MongoDB.
  - Nous devons maîtriser JavaScript et le concept MapReduce pour réaliser des agrégations avancées.


---

## Conclusion

Nous avons maintenant une vue d’ensemble de CouchDB, un SGBD NoSQL documentaire qui s’appuie sur une API REST, et du modèle MapReduce pour agréger et analyser des données distribuées. Nous savons créer des bases, insérer, lire, mettre à jour et supprimer des documents, et définir des vues MapReduce pour obtenir des agrégations.

Cette approche complète notre compréhension de l’écosystème NoSQL, en ajoutant une vision distribuée et parallèle du traitement des données, différente de l’approche de Redis (en mémoire, opérations clé-valeur simples) et de MongoDB (requêtes plus élaborées et agrégations déclaratives).

### **Rapport sur l’utilisation de Redis : Comprendre les bases et les fonctionnalités principales**

#### **Introduction**
Redis est une base de données en mémoire open-source, utilisée principalement comme structure de données clé/valeur. Sa rapidité et sa polyvalence en font un outil de choix pour diverses applications, telles que le stockage temporaire, les files d'attente et les communications en temps réel. Ce rapport explore les fonctionnalités fondamentales de Redis, en mettant l’accent sur les types de données disponibles, la gestion des canaux de communication (**pub/sub**) et la manipulation des bases internes de Redis.

---

### **1. Types de données dans Redis**
Redis offre une variété de types de données qui permettent de répondre à différents besoins. Voici un aperçu de quelques types clés :

#### **1.1. Les chaînes (Strings)**
Les chaînes représentent le type de donnée le plus basique de Redis. Elles permettent de stocker une simple valeur associée à une clé. Par exemple : 
```bash
SET clé "valeur"
GET clé
```
Ce type est souvent utilisé pour stocker des informations simples comme des noms ou des messages.

#### **1.2. Les listes**
Les listes sont des collections ordonnées d'éléments, qui permettent d'ajouter ou de retirer des éléments à une extrémité. Les commandes associées incluent :
- `LPUSH` : Ajouter un élément au début de la liste.
- `RPUSH` : Ajouter un élément à la fin de la liste.
- `LRANGE` : Obtenir une plage d'éléments.

Exemple d’utilisation :
```bash
LPUSH maListe "élément1"
LPUSH maListe "élément2"
LRANGE maListe 0 -1
```

#### **1.3. Les ensembles (Sets)**
Les ensembles sont des collections non ordonnées d'éléments uniques, idéales pour représenter des regroupements sans doublons. Les commandes incluent :
- `SADD` : Ajouter un élément à l'ensemble.
- `SMEMBERS` : Voir tous les éléments de l'ensemble.
- `SREM` : Retirer un élément.

Exemple :
```bash
SADD monEnsemble "valeur1"
SADD monEnsemble "valeur2"
SMEMBERS monEnsemble
```

#### **1.4. Les ensembles ordonnés (Sorted Sets)**
Un ensemble ordonné ajoute une notion de priorité à chaque élément sous la forme d'un score numérique. Les commandes incluent :
- `ZADD` : Ajouter un élément avec un score.
- `ZRANGE` : Récupérer les éléments selon leur ordre.

---

### **2. Gestion des canaux de communication (Pub/Sub)**
Redis offre une puissante fonctionnalité de publication/abonnement (**Publish/Subscribe**, ou Pub/Sub), idéale pour les applications en temps réel, comme les notifications ou la messagerie instantanée.

#### **2.1. Abonnement à un canal**
Un client peut s'inscrire à un canal en utilisant la commande `SUBSCRIBE`. Une fois inscrit, il recevra tous les messages publiés sur ce canal.

Exemple :
```bash
SUBSCRIBE canal1
```
Ici, le client reste en attente de tous les messages envoyés sur le canal `canal1`.

#### **2.2. Publication de messages**
La commande `PUBLISH` permet d’envoyer des messages sur un canal spécifique. Tous les clients abonnés à ce canal recevront le message instantanément.

Exemple :
```bash
PUBLISH canal1 "Message important"
```
Les clients inscrits au canal `canal1` recevront le message *"Message important"*.

#### **2.3. Abonnement avec des modèles (Patterns)**
Redis permet également de s’abonner à plusieurs canaux utilisant un motif spécifique grâce à la commande `PSUBSCRIBE`.

Exemple :
```bash
PSUBSCRIBE "canal*"
```
Dans cet exemple, le client s’inscrit à tous les canaux dont le nom commence par `canal`, comme `canal1` ou `canal_notes`.

#### **2.4. Messages directs**
Il est possible d'envoyer des messages à des utilisateurs spécifiques en incluant une désignation dans le nom du canal, par exemple :
```bash
PUBLISH user:1 "Message pour utilisateur 1"
```
Seuls les utilisateurs inscrits à ce canal spécifique recevront le message.

#### **2.5. Limites des abonnements**
Un client abonné à un canal ou à un motif spécifique ne recevra pas les messages d’autres canaux non liés. Cela garantit une communication ciblée et efficace.

---

### **3. Gestion des bases de données et des clés**
Redis divise son stockage en plusieurs bases de données internes. Par défaut, il y en a 16, accessibles par leurs indices (de 0 à 15).

#### **3.1. Changer de base de données**
Par défaut, Redis utilise la base numéro 0. On peut changer de base en utilisant la commande `SELECT` :
```bash
SELECT 1
```
Cette commande connecte l'utilisateur à la base 1.

#### **3.2. Lister les clés**
Pour voir toutes les clés d’une base, la commande `KEYS` est utilisée. Exemple :
```bash
KEYS *
```
Cela renvoie toutes les clés de la base courante.

#### **3.3. Persistance des données**
Redis ne sauvegarde pas automatiquement les données en mémoire sur disque. Si la persistance n'est pas configurée correctement, les données risquent d’être perdues en cas de panne. Il est donc crucial de configurer des mécanismes comme les snapshots ou la journalisation pour garantir la durabilité.

---

### **4. Précautions et limitations**
Redis est extrêmement rapide et efficace, mais il n'est pas conçu pour remplacer complètement une base relationnelle classique comme MySQL ou Oracle. Sa gestion en mémoire le rend vulnérable aux pertes de données si la persistance n'est pas activée. Pour des cas critiques, il est conseillé d'assurer une configuration adéquate et de comprendre ses limitations.

---

### **Conclusion**
Ce rapport a couvert les bases de Redis, en se concentrant sur les types de données, la gestion des canaux de communication en temps réel, et les bases internes de Redis. Grâce à ces fonctionnalités, Redis peut être utilisé pour des applications variées, allant du stockage temporaire aux systèmes de messagerie en temps réel.

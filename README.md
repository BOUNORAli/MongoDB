# MongoDB


## 1. Vérifier que le données ont été importées, en les comptant par exemple


<img width="598" alt="image" src="https://github.com/user-attachments/assets/8291f6a4-eaea-46c3-b888-cee5d76b5c4e">


## 2. Avant de commencer à interroger la base de données lesfilms, il est essentiel de bien comprendre la structure d’un document dans notre collection de films. Pour ce faire, nous allons utiliser la fonction findOne(), qui permet de récupérer un seul document. db.films.findOne ()


![image](https://github.com/user-attachments/assets/eb398153-2799-4642-a850-a51f24ff8597)


## 3. Afficher la liste des films d’action.

j'ai utilisé la requête suivante :
```bash

db.films.find({ genre: "Action" })
```

![image](https://github.com/user-attachments/assets/90af34a7-45b2-4573-a473-2c22ca6cab55)

## 4. Afficher le nombre de films d’action

j'ai utilisé la requête suivante :
```bash

db.films.count({ genre: "Action" })
```

![image](https://github.com/user-attachments/assets/f81652aa-e3df-49b8-91dc-152ff6ced9c4)



## 5. Afficher les films d’action produits en France

j'ai utilisé la requête suivante :
```bash
db.films.find({ genre: "Action", country: "FR" })
```

![image](https://github.com/user-attachments/assets/10a875e9-668d-43d3-851e-755386e270d7)


## 6. Afficher les films d’action produits en France en 1963.

j'ai utilisé la requête suivante :
```bash
db.films.find({ genre: "Action", country: "FR", year: 1963 })
```

![image](https://github.com/user-attachments/assets/1ae1d492-1777-42ff-980d-08f78ccaecac)


## 7. Jusqu’à maintenant, à chaque fois qu’un document est renvoyé, tous ses attributs sont affichés. Pour n’afficher que certains attributs, on utilise un filtre. Pour afficher les films d’action réalisés en France, sans les grades, le filtre est passé comme deuxième paramètre de la fonction find() comme ceci.

j'ai utilisé la requête suivante :
```bash
db.films.find(
  { genre: "Action", country: "FR" },  
  { grades: 0 }                        
)
```
![image](https://github.com/user-attachments/assets/8cc2d19c-6655-4b37-8e6e-afc6680a8a88)


## 8. Vous avez certainement remarqué que les identifiants des documents sont affichés meme si un filtre est appliqué aux résultats. Pour ne pas les afficher, vous pouvez ajouter sa valeur est la mettre à zéro, comme ceci (cette requete nous renvois tous les films d’action réalisés en France sans les identifiants) :


j'ai utilisé la requête suivante :
```bash
db.films.find(
  { genre: "Action", country: "FR" },  
  { _id: 0, grades: 0 }               
)
```
![image](https://github.com/user-attachments/assets/9854f55c-11b6-4347-a712-2e8d463711a9)


## 9. Afficher les titres et les grades des films d’action réalisés en France sans leurs identifiants.

j'ai utilisé la requête suivante :
```bash
db.films.find(
  { genre: "Action", country: "FR" },  
  { _id: 0, title: 1, grades: 1 }       
)
```
![image](https://github.com/user-attachments/assets/66960d47-0aac-450d-90e9-57c1b3439647)


## 10. Les filtres affichés jusqu’à présent sont par valeur exacte. Afficher les titres et les notes des films d’action réalisés en France et ayant obtenus une note supérieur à 10. Remarquez que meme si des films ont obtenu certaines notes inférieures à 10 sont affichés.

j'ai utilisé la requête suivante :
```bash
db.films.find(
  { 
    genre: "Action", 
    country: "FR", 
    grades: { $elemMatch: { note: { $gt: 10 } } } 
  },  
  { _id: 0, title: 1, grades: 1 } 
)
```

![image](https://github.com/user-attachments/assets/7f5927c9-247a-422e-bcbe-e330e507fd86)


## 11. Si l’on souhaite afficher que des films ayant que des notes supérieures à 10, on doit préciser que les notes obtenues sont toutes supérieures à 40. La requetes suivantes permet d’afficher les films d’action réalisés en France ayant obtenus que des notes supérieures à 10.

j'ai utilisé la requête suivante :
```bash
db.films.find(
  { 
    genre: "Action", 
    country: "FR", 
    grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } 
  },
  { _id: 0, title: 1, grades: 1 }
)
```
![image](https://github.com/user-attachments/assets/7af61e11-6ffb-4e10-a8dc-bd2977934a7f)



## 12. Afficher les différents genres de la base lesfilms.

j'ai utilisé la requête suivante :
```bash
db.films.aggregate([
  { $group: { _id: "$genre" } }
])
```
![image](https://github.com/user-attachments/assets/5fe59b2c-0e15-45d7-b67a-616b348d4424)


## 13. Afficher les différents grades attribués.

j'ai utilisé la requête suivante :
```bash
db.films.aggregate([
  { $unwind: "$grades" },
  { $group: { _id: "$grades.grade" } }
])
```
![image](https://github.com/user-attachments/assets/b1e76a76-a32a-4330-951d-ae2f52f6c4f6)


## 14. Afficher tous les filmes dans lesquels joue au moins un des artistes suivants [”artist:4”,”artist:18”,”artist:11”].


j'ai utilisé la requête suivante, mais ça ne retourne rien car il n'existent pas :
```bash
db.films.find({
  "actors._id": { $in: ["artist:4", "artist:18", "artist:11"] }
})
```
![image](https://github.com/user-attachments/assets/92c1815a-9492-4d98-92fc-70fbe0897c8b)



## 15. Afficher tous les films qui n’ont pas de résumé.

j'ai utilisé la requête suivante :
```bash
db.films.find({
  $or: [
    { summary: { $exists: false } },
    { summary: "" }
  ]
})
```
![image](https://github.com/user-attachments/assets/7ea66ab0-98bf-4f59-849e-d137fa2be438)


## 16. Afficher tous les films tournés avec Leonardo DiCaprio en 1997.

j'ai utilisé la requête suivante :
```bash
db.films.find({
  year: 1997,
  "actors.first_name": "Leonardo",
  "actors.last_name": "DiCaprio"
})
```
![image](https://github.com/user-attachments/assets/a2e36568-4390-431e-945c-491065b5c3a1)


## 17. Afficher les films tournée avec Leonardo DiCaprio ou 1997.

j'ai utilisé la requête suivante :
```bash
db.films.find({
  year: 1997,
  "actors": {
    $elemMatch: {
      first_name: "Leonardo",
      last_name: "DiCaprio"
    }
  }
})
```
![image](https://github.com/user-attachments/assets/5edbefc9-f483-4887-8f2a-03df37cd4f7a)




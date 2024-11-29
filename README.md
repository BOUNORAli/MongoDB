# MongoDB


## 1. Vérifier que le données ont été importées, en les comptant par exemple
<img width="598" alt="image" src="https://github.com/user-attachments/assets/8291f6a4-eaea-46c3-b888-cee5d76b5c4e">


## 2. Avant de commencer `a interroger la base de donn´ees lesfilms, il est essentiel de bien comprendre
la structure d’un document dans notre collection de films. Pour ce faire, nous allons utiliser la
fonction findOne(), qui permet de r´ecup´erer un seul document.
db . films . findOne ()

![image](https://github.com/user-attachments/assets/eb398153-2799-4642-a850-a51f24ff8597)


## 3. Afficher la liste des films d’action.

j'ai utilisé la commande :

db.films.find({ genre: "Action" })

![image](https://github.com/user-attachments/assets/90af34a7-45b2-4573-a473-2c22ca6cab55)

## 4. Afficher le nombre de films d’action

j'ai utilisé la commande :

db.films.count({ genre: "Action" })

![image](https://github.com/user-attachments/assets/f81652aa-e3df-49b8-91dc-152ff6ced9c4)



## 5. Afficher les films d’action produits en France

j'ai utilisé la commande :

db.films.find({ genre: "Action", country: "FR" })

![image](https://github.com/user-attachments/assets/10a875e9-668d-43d3-851e-755386e270d7)


## 6. Afficher les films d’action produits en France en 1963.

j'ai utilisé la commande :

db.films.find({ genre: "Action", country: "FR", year: 1963 })

![image](https://github.com/user-attachments/assets/1ae1d492-1777-42ff-980d-08f78ccaecac)


## 7. Jusqu’à maintenant, à chaque fois qu’un document est renvoyé, tous ses attributs sont affichés. Pour n’afficher que certains attributs, on utilise un filtre. Pour afficher les films d’action réalisés en France, sans les grades, le filtre est passé comme deuxième paramètre de la fonction find() comme ceci.

j'ai utilisé la commande :

db.films.find(
  { genre: "Action", country: "FR" },  
  { grades: 0 }                        
)

![image](https://github.com/user-attachments/assets/8cc2d19c-6655-4b37-8e6e-afc6680a8a88)



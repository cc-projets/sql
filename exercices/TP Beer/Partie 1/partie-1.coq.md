## Docker

Si vous avez déjà vu Docker, créez via docker un conteneur MySQL et importez le fichier `beer.sql` dans votre conteneur.

Si vous n'avez pas encore vu Docker, importez le fichier `beer.sql` dans votre base de données MySQL ou demandez à votre
formateur de vous aider ou de vous donner le docker-compose.

### Prérequis

- Vous devez avoir un dockerfile et un docker-compose.yml
- Vous ne devez pas utiliser de `bind mount` !
- Vous avez le droit d'utiliser un `volume`

## Etude du modèle

### Effectuez un reverse engineering du modèle

- Quelles sont les tables ?

### Il y a des mauvaises pratiques dans ce modèle, lesquelles ?


## Exercices

Réalisez les requêtes suivantes :

### Quels sont les tickets qui comportent l’article d’ID 500, afficher le numéro de ticket uniquement ?

```mysql
SELECT ticket.NUMERO_TICKET 
FROM ticket 
    NATURAL JOIN ventes 
    NATURAL JOIN article 
WHERE article.ID_ARTICLE = "500"
```

### Afficher les tickets du 15/01/2014.

```mysql
SELECT NUMERO_TICKET, DATE_VENTE
FROM ticket
WHERE DATE_VENTE LIKE "2014-01-15%"
```

### Afficher les tickets émis du 15/01/2014 et le 17/01/2014.

```mysql
SELECT NUMERO_TICKET, DATE_VENTE
FROM ticket 
WHERE Date_VENTE BETWEEN '2014-01-15 00:00:00' 
    AND '2014-01-17 23:59:59';
```

### Afficher la liste des articles apparaissant à 50 et plus exemplaires sur un ticket.

```mysql
SELECT ventes.ID_ARTICLE, article.NOM_ARTICLE 
FROM article 
    NATURAL JOIN ventes 
WHERE ventes.QUANTITE >= 50
```

### Quelles sont les tickets émis au mois de mars 2014.

```mysql
SELECT NUMERO_TICKET, DATE_VENTE
FROM ticket 
WHERE MONTH(Date_VENTE) = 3 
  AND YEAR(Date_VENTE) = 2014;
```

### Quelles sont les tickets émis entre les mois de mars et avril 2014 ?

```mysql
SELECT NUMERO_TICKET, DATE_VENTE
FROM ticket 
WHERE Date_VENTE >= '2014-03-01' 
  AND Date_VENTE < '2014-05-01';
```

### Quelles sont les tickets émis au mois de mars et juin 2014 ?

```mysql
SELECT NUMERO_TICKET, DATE_VENTE
FROM ticket 
WHERE (MONTH(Date_VENTE) = 3 
           AND YEAR(Date_VENTE) = 2014)
   OR (MONTH(Date_VENTE) = 6 
           AND YEAR(Date_VENTE) = 2014);
```

### Afficher la liste des bières classée par couleur. (Afficher l’id et le nom)

```mysql
SELECT NOM_ARTICLE, ID_Couleur 
FROM article 
ORDER BY ID_Couleur
```

### Afficher la liste des bières n’ayant pas de couleur. (Afficher l’id et le nom)

```mysql
SELECT NOM_ARTICLE, ID_Couleur
FROM article 
WHERE ID_Couleur IS NULL
```
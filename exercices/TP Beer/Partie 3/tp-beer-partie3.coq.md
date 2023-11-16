# TP Beez - Partie 3

> Vous devrez utiliser des sous requêtes pour toutes les questions !

## 20. Listez les articles qui n’ont fait l’objet d’aucune vente en 2014.

```mysql
SELECT ID_ARTICLE, NOM_ARTICLE
FROM article
WHERE ID_ARTICLE NOT IN (
    SELECT DISTINCT ventes.ID_ARTICLE
    FROM ventes
    WHERE ventes.ANNEE = 2014
)
ORDER BY ID_ARTICLE;
```

## 21. Codez de 2 manières différentes la requête suivante : Listez les pays qui fabriquent des bières de type ‘Trappiste’.

### Méthode 1 - Avec jointure

```mysql
SELECT DISTINCT pays.NOM_PAYS
FROM pays
    NATURAL JOIN marque
    NATURAL JOIN article 
    NATURAL JOIN type
WHERE type.NOM_TYPE = 'Trappiste';
```

### Méthode 2 - Avec sous Requête

```mysql
SELECT pays.NOM_PAYS
FROM pays
     NATURAL JOIN marque
WHERE marque.ID_MARQUE IN (
    SELECT ID_MARQUE
    FROM marque
    WHERE ID_MARQUE IN (
        SELECT ID_MARQUE
        FROM article
        WHERE ID_TYPE IN (
            SELECT ID_TYPE
            FROM type
            WHERE NOM_TYPE = 'Trappiste'
        )
    )
) GROUP BY pays.NOM_PAYS;

```

## 22. Listez les tickets sur lesquels apparaissent un des articles apparaissant aussi sur le ticket 2014-856.

```mysql
SELECT DISTINCT t1.NUMERO_TICKET
FROM ventes t1
WHERE t1.ID_ARTICLE IN (
    SELECT ID_ARTICLE
    FROM ventes
    WHERE NUMERO_TICKET = '2014-856'
)
ORDER BY t1.NUMERO_TICKET;
```

## 23. Listez les articles ayant un degré d’alcool plus élevé que la plus forte des trappistes.

```mysql
SELECT ID_ARTICLE, NOM_ARTICLE, TITRAGE
FROM article
WHERE TITRAGE > (
    SELECT MAX(TITRAGE)
    FROM article
    NATURAL JOIN type
    WHERE type.NOM_TYPE = 'Trappiste'
);
```

## 24. Affichez les quantités vendues pour chaque couleur en 2014.

```mysql
SELECT couleur.NOM_COULEUR, SUM(ventes.QUANTITE) AS QuantiteVendue
FROM ventes
    NATURAL JOIN article 
    NATURAL JOIN couleur
WHERE ventes.Annee = 2014
GROUP BY couleur.NOM_COULEUR
ORDER BY QuantiteVendue DESC;
```

## 25. Donnez l’ID, le nom, le volume et la quantité vendue des 20 articles les plus vendus en 2016.

```mysql
SELECT ID_ARTICLE, NOM_ARTICLE, VOLUME, QuantiteVendue
FROM (
    SELECT article.ID_ARTICLE, article.NOM_ARTICLE, article.VOLUME, 
           SUM(ventes.QUANTITE) AS QuantiteVendue,
           ROW_NUMBER() OVER (ORDER BY SUM(ventes.QUANTITE) DESC) as row_num
    FROM ventes
    JOIN article ON ventes.ID_ARTICLE = article.ID_ARTICLE
    WHERE ventes.ANNEE = 2016
    GROUP BY article.ID_ARTICLE, article.NOM_ARTICLE, article.VOLUME
) AS TopArticles
WHERE row_num <= 20;



```

## 26. Donner l’ID, le nom, le volume et la quantité vendue des 5 ‘Trappistes’ les plus vendus en 2016.

```mysql
SELECT ID_ARTICLE, NOM_ARTICLE, VOLUME, QuantiteVendue
FROM (
    SELECT article.ID_ARTICLE, article.NOM_ARTICLE, article.VOLUME, 
           SUM(ventes.QUANTITE) AS QuantiteVendue,
           ROW_NUMBER() OVER (ORDER BY SUM(ventes.QUANTITE) DESC) as row_num
    FROM ventes
    JOIN article ON ventes.ID_ARTICLE = article.ID_ARTICLE
    JOIN type ON article.ID_TYPE = type.ID_TYPE
    WHERE ventes.ANNEE = 2016
      AND type.NOM_TYPE = 'Trappiste'
    GROUP BY article.ID_ARTICLE, article.NOM_ARTICLE, article.VOLUME
) AS TopTrappistes
WHERE row_num <= 5;

```
----

# Pour les plus à l'aise

Si vous avez terminé, rendez-vous sur la partie 4 !



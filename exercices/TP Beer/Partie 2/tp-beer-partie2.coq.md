## 10 Listez pour chaque ticket la quantité totale d’articles vendus. (Classer par quantité décroissante)

```mysql
SELECT ticket.NUMERO_TICKET, SUM(ventes.QUANTITE) AS QuantiteTotale
FROM ticket
    NATURAL JOIN ventes 
GROUP BY ticket.NUMERO_TICKET
ORDER BY QuantiteTotale DESC;

```

## 11 Listez chaque ticket pour lequel la quantité totale d’articles vendus est supérieure à 500. (Classer par quantité décroissante)

```mysql
SELECT ticket.NUMERO_TICKET, SUM(ventes.QUANTITE) as qteTotal
FROM ticket
    NATURAL JOIN ventes 
GROUP BY ticket.NUMERO_TICKET
HAVING SUM(ventes.QUANTITE) > 500
ORDER BY qteTotal DESC;
```

## 12 Listez chaque ticket pour lequel la quantité totale d’articles vendus est supérieure à 500. On exclura du total, les ventes ayant une quantité inférieur à 50 (classer par quantité décroissante)

```mysql
SELECT ticket.NUMERO_TICKET, SUM(ventes.QUANTITE) AS QuantiteTotale
FROM ticket 
    NATURAL JOIN ventes
WHERE ventes.QUANTITE <= 50 
GROUP BY ticket.NUMERO_TICKET 
HAVING SUM(ventes.QUANTITE) > 500 
ORDER BY QuantiteTotale DESC;
```

## 13 Listez les bières de type ‘Trappiste’. (id, nom de la bière, volume et titrage)

```mysql
SELECT article.NOM_ARTICLE
FROM article
    NATURAL JOIN type
WHERE type.NOM_TYPE LIKE "Trappiste"**
```

## 14 Listez les marques de bières du continent ‘Afrique’

```mysql
SELECT marque.NOM_MARQUE
FROM marque
     NATURAL JOIN pays
     NATURAL JOIN continent
WHERE continent.NOM_CONTINENT LIKE "Afrique"
```

## 15 Lister les bières du continent ‘Afrique’

```mysql
SELECT article.NOM_ARTICLE
FROM article
    NATURAL JOIN marque
    NATURAL JOIN pays
    NATURAL JOIN continent
WHERE continent.NOM_CONTINENT LIKE "Afrique"
```

## 16. Lister les tickets (année, numéro de ticket, montant total payé). En sachant que le prix de vente est égal au prix d’achat augmenté de 15%.

```mysql
SELECT ticket.NUMERO_TICKET, YEAR(ticket.ANNEE) AS Annee, SUM(article.PRIX_ACHAT * 1.15 * ventes.QUANTITE) AS MontantTotalPaye
FROM ticket
	NATURAL JOIN ventes
    NATURAL JOIN article
GROUP BY Annee, ticket.NUMERO_TICKET
ORDER BY Annee, MontantTotalPaye DESC;
```

## 17  Donner le C.A. par année.

```mysql
SELECT ventes.ANNEE AS Annee, SUM(article.PRIX_ACHAT * 1.15 * ventes.QUANTITE) AS ChiffreAffaires
FROM ventes
    NATURAL JOIN article
GROUP BY Annee
ORDER BY Annee;
```

## 18. Lister les quantités vendues de chaque article pour l’année 2016.

```mysql
SELECT article.ID_ARTICLE, article.NOM_ARTICLE, SUM(ventes.QUANTITE) AS QuantiteVendue
FROM ventes
    NATURAL JOIN article
    NATURAL JOIN ticket 
WHERE ventes.ANNEE = 2016
GROUP BY article.ID_ARTICLE, article.NOM_ARTICLE
ORDER BY QuantiteVendue DESC;
```

## 19. Lister les quantités vendues de chaque article pour les années 2014, 2015, 2016.

```mysql
SELECT article.ID_ARTICLE, article.NOM_ARTICLE, ventes.ANNEE AS Annee, SUM(ventes.QUANTITE) AS QuantiteVendue
FROM ventes
NATURAL JOIN article
WHERE ventes.ANNEE IN (2014, 2015, 2016)
GROUP BY article.ID_ARTICLE, article.NOM_ARTICLE, Annee
ORDER BY Annee, QuantiteVendue DESC;
```


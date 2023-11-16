# TP Beez - PArtie 4

Si vous êtes arrivé jusque-là, c'est que vous avez déjà fait les parties 1, 2 et 3 du TP Beer. Bravo !
Il est donc temps pour vous de vous heurter à des requêtes un peu plus complexes !

> Avant de poursuivre, faites un dump de votre BDD et sauvegardez-le dans le dossier `Partie 4` de votre projet.

## Donnez l’ID, le nom, le volume et les quantités vendues en 2015 et 2016, des bières dont les ventes ont été stables. (Moins de 1% de variation)

```mysql
SELECT
    Subquery.ID_ARTICLE,
    Subquery.NOM_ARTICLE,
    Subquery.VOLUME,
    Subquery.Quantite_2015,
    Subquery.Quantite_2016
FROM (
    SELECT
        article.ID_ARTICLE,
        article.NOM_ARTICLE,
        article.VOLUME,
        SUM(CASE WHEN ventes.ANNEE = 2015 THEN ventes.QUANTITE ELSE 0 END) AS Quantite_2015,
        SUM(CASE WHEN ventes.ANNEE = 2016 THEN ventes.QUANTITE ELSE 0 END) AS Quantite_2016,
        MAX(ventes.QUANTITE) AS MaxQuantite,
        MIN(ventes.QUANTITE) AS MinQuantite
    FROM ventes
    NATURAL JOIN article 
    GROUP BY article.ID_ARTICLE, article.NOM_ARTICLE, article.VOLUME
) AS Subquery
WHERE 
	(Subquery.MaxQuantite - Subquery.MinQuantite) / NULLIF(Subquery.MinQuantite, 0) <= 1
```

## Listez les types de bières suivant l’évolution de leurs ventes entre 2015 et 2016. Classez le résultat par ordre décroissant des performances.

```mysql
SELECT
    NOM_TYPE,
    Ventes_2015,
    Ventes_2016,
    (Ventes_2016 - Ventes_2015) AS Evolution
FROM (
    SELECT
        type.NOM_TYPE,
        SUM(CASE WHEN ventes.ANNEE = 2015 THEN ventes.QUANTITE ELSE 0 END) AS Ventes_2015,
        SUM(CASE WHEN ventes.ANNEE = 2016 THEN ventes.QUANTITE ELSE 0 END) AS Ventes_2016
    FROM ventes
    NATURAL JOIN article 
    NATURAL JOIN type
    WHERE ventes.ANNEE IN (2015, 2016)
    GROUP BY type.NOM_TYPE
) AS Subquery
ORDER BY
    Evolution DESC;

```

## Existe-t-il des tickets sans vente ?

```mysql
SELECT NUMERO_TICKET
FROM ticket
WHERE NUMERO_TICKET NOT IN (
    SELECT NUMERO_TICKET
    FROM ventes
);
```

## Listez les produits vendus en 2016 dans des quantités jusqu’à -15% des quantités de l’article le plus vendu.

```mysql
SELECT
    article.ID_ARTICLE,
    article.NOM_ARTICLE,
    ventes.ANNEE,
    ventes.QUANTITE
FROM ventes
NATURAL JOIN article 
WHERE ventes.ANNEE = 2016
    AND ventes.QUANTITE <= (
		SELECT MAX(QUANTITE) * 0.85 
        FROM ventes 
        WHERE ANNEE = 2016);
```

## Appliquez une augmentation de tarif de 10% pour toutes les bières ‘Trappistes’ de couleur ‘Blonde’

```mysql
SELECT
    ID_ARTICLE,
    NOM_ARTICLE,
    PRIX_ACHAT,
    PRIX_ACHAT * 1.10 AS PRIX_VENTE_AVEC_AUGMENTATION
FROM
    article
WHERE
    ID_TYPE IN (SELECT ID_TYPE FROM type WHERE NOM_TYPE = 'Trappiste')
    AND ID_COULEUR IN (SELECT ID_COULEUR FROM couleur WHERE NOM_COULEUR = 'Blonde');
```

## Mettre à jour le degré d’alcool des toutes les bières n’ayant pas cette information. On y mettra le degré d’alcool de la moins forte des bières du même type et de même couleur.

```mysql
SELECT
    a.ID_ARTICLE,
    a.NOM_ARTICLE,
    a.TITRAGE,
    (
        SELECT b.TITRAGE
        FROM article AS b
        WHERE b.ID_TYPE = a.ID_TYPE
          AND b.ID_COULEUR = a.ID_COULEUR
          AND b.TITRAGE IS NOT NULL
        ORDER BY b.TITRAGE ASC, b.ID_COULEUR ASC
        LIMIT 1
    ) AS TITRAGE_MIN
FROM
    article AS a
WHERE
    a.TITRAGE IS NULL;

```

## Suppression des bières qui ne sont pas des bières ! (Type ‘Bière Aromatisée’)

```mysql
DELETE FROM article
WHERE ID_TYPE IN (
    SELECT ID_TYPE 
    FROM type 
    WHERE NOM_TYPE = 'Bière Aromatisée'
);

```
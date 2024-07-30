# 3a - Requêtes avec plus d'une table (Contacts)

```sql
SET search_path TO contacts;
```

## Produit cartésien

- Associer chaque ligne d'une table avec chaque ligne de l'autre table
    - donne trop de lignes
    - rarement utilisé car il ne donne généralement pas d'informations utiles
    - et il peut devenir très inefficace si les tables impliquées sont grandes
        - le nombre de lignes dans les résultats sera la multiplication du nombre de lignes dans chaque table impliquée
```sql
SELECT *
FROM call,
    contact;
```

## Jointure (avec produit cartésien)

- Ne garder que les lignes correspondantes, en "suivant" la clé étrangère de `call` à `contact`
    - ceci est l'ancienne manière de faire une jointure (un produit cartésien suivi d'une condition `WHERE`)
```sql
SELECT *
FROM call,
    contact
WHERE call.contact_id = contact.contact_id;
```

## Jointures (avec `JOIN`)

### Jointure Interne

- Une manière plus moderne d'exprimer une jointure en SQL
    - il existe plusieurs types de jointures, le type le plus courant est une `INNER JOIN`
```sql
SELECT *
FROM call
    INNER JOIN contact
               ON call.contact_id = contact.contact_id;
```

### Jointure Naturelle

- Jointure sur des colonnes ayant les mêmes noms, en utilisant l'opérateur =, et en supprimant les colonnes dupliquées
    - notez que les colonnes sont dans un ordre différent
    - il n'est pas recommandé (voire déconseillé) d'utiliser les jointures naturelles car la condition de jointure n'est pas spécifiée et pourrait avoir des conséquences difficiles à prévoir, surtout à long terme si la base de données est modifiée après l'écriture des requêtes
```sql
SELECT *
FROM call
        NATURAL JOIN contact; 
```
- L'appel téléphonique avec l'ID 2 ne correspond à aucun contact, il n'est donc pas listé dans les résultats
- Chaque fois que nous utilisons = avec `NULL`, c'est toujours faux, donc l'appel 3 n'est pas dans les résultats

### Jointures Externes

- Utiliser une *jointure externe gauche* pour conserver les appels téléphoniques ne correspondant à aucun contact
    - c'est comme une jointure interne, mais les lignes à gauche ne correspondant à rien à droite seront conservées
```sql
SELECT *
FROM call
        LEFT OUTER JOIN contact
                        ON call.contact_id = contact.contact_id;
```

- Utiliser une *jointure externe droite* pour conserver les contacts ne correspondant à aucun appel téléphonique
```sql
SELECT *
FROM call
        RIGHT OUTER JOIN contact
                        ON call.contact_id = contact.contact_id;
```

- Ou une *jointure externe complète* pour conserver les lignes ne correspondant à rien des deux côtés
```sql
SELECT *
FROM call FULL OUTER JOIN contact
   ON call.contact_id = contact.contact_id;
```
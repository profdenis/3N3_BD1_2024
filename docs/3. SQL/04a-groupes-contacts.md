# 4a - Groupement et Agrégats (Contacts)

```sql
SET search_path TO contacts;
```

1- Compter le nombre de lignes dans la table `call`
```sql
SELECT COUNT(*)
FROM call;
```

2- Compter le nombre d'appels pour chaque numéro de téléphone dans la table `call`, et renommer la colonne `count` en quelque chose de plus approprié.
```sql
SELECT phone, COUNT(*) AS n_calls
FROM call
GROUP BY phone;
```

3- Compter le nombre d'appels pour chaque numéro de téléphone dans la table `call`, et trier les résultats par le plus grand nombre d'appels en premier.
```sql
SELECT phone, COUNT(*) AS n_calls
FROM call
GROUP BY phone
ORDER BY 2 DESC;
```

4- Compter le nombre d'appels pour chaque numéro de téléphone dans la table `call`, et ne garder que les numéros de téléphone avec plus d'un appel.

- utiliser `HAVING` avec une condition
- `HAVING` est similaire à `WHERE`, mais il est exécuté après le `GROUP BY`, tandis que le `WHERE` est exécuté après le `FROM`, mais avant le `GROUP BY`
- même si `SELECT` est écrit en premier, il est en fait exécuté après `HAVING`, mais avant `ORDER BY` (si présent)
```sql
SELECT phone, COUNT(*) AS n_calls
FROM call
GROUP BY phone
HAVING COUNT(*) > 1;
```

5- Nous pouvons grouper non seulement les lignes d'une table, mais aussi tout tableau de résultats de la partie `FROM` de la requête

- cette requête calcule le nombre d'appels pour chaque contact (non pas pour chaque numéro de téléphone dans `call`, mais pour chaque `contact_id` dans `contact`)
- notez que les contacts sans aucun appel sont listés avec un compte de 0 parce que nous utilisons une jointure externe gauche, donc les contacts ne correspondant à rien dans `call`, en d'autres termes, les contacts sans appels associés, seront conservés dans les résultats
```sql
SELECT contact.contact_id, COUNT(call_id) AS n_calls
FROM contact
        LEFT OUTER JOIN call
                        ON contact.contact_id = call.contact_id
GROUP BY contact.contact_id
ORDER BY n_calls DESC;
```
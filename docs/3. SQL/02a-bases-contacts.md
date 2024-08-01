# 2a - Base de Données de Contacts

- Pensez à une base de données de contacts sur un téléphone
- 2 tables :
    - `contact(CONTACT_ID, name, phone, address, email)`
    - `call(CALL_ID, phone, date, time, contact_id*)`

## Création de la Base de Données de Contacts

1- Exécutez le
fichier [contact_create.sql](../src/create/contact_create.sql) pour créer
la base de données

- les commandes SQL incluses dans ce fichier seront expliquées plus tard
- ouvrez le fichier dans DataGrip et exécutez-le dans une session existante ou
  créez une nouvelle session

2- Pour vérifier les résultats, vous pouvez utiliser l'onglet *Database
Explorer* ou exécuter les requêtes suivantes :

```sql
SELECT *
FROM contacts.contact;

SELECT *
FROM contacts.call;
```

3- Pour éviter de préfixer les noms de tables avec le nom du schéma à chaque
fois, nous pouvons exécuter la commande suivante :

```sql
SET search_path TO contacts;

SELECT *
FROM contact;

SELECT *
FROM call;
```

## Requêtes sur une Seule Table

1- Trouver l'adresse email de John Doe

```sql
SELECT email
FROM contact
WHERE name = 'John Doe';
```

2- Trouver les contacts sans numéro de téléphone

```sql
SELECT *
FROM contact
WHERE phone IS NULL;
```

3- Trouver les contacts avec un numéro de téléphone

```sql
SELECT *
FROM contact
WHERE phone IS NOT NULL;
```

### Avec des Fonctions Agrégées

4- Compter le nombre de lignes dans la table `call`

```sql
SELECT COUNT(*)
FROM call;
```

5- Compter le nombre de lignes dans la table `call` avec des valeurs non nulles
pour `contact_id`

```sql
SELECT COUNT(contact_id)
FROM call;
```

6- Compter le nombre de lignes dans la table `call` avec des valeurs nulles
pour `contact_id`

```sql
SELECT COUNT(*)
FROM call
WHERE contact_id IS NULL;
```
# 3b - Requêtes avec plus d'une table (Université)

```sql
set search_path to university;
```

## Produit cartésien

1- Associer chaque ligne de la première table avec chaque ligne de la seconde

```sql
select *
from offering,
     instructor;
```

## Jointure

2- Comme le produit cartésien, mais ne garder que les lignes correspondantes
intéressantes

- ici, ne garder que les identifiants d'instructeurs correspondants (
colonne `iid`)

```sql
select *
from offering,
     instructor
where offering.iid = instructor.iid;
```

3- Manière plus moderne d'écrire la même requête : utiliser une `inner join`

```sql
select *
from offering
         inner join instructor on offering.iid = instructor.iid;

select *
from offering as o
         inner join instructor as i on o.iid = i.iid;


select semester, year, section, i.name as instructor_name, c.name as course_name
from offering as o
         inner join instructor as i on o.iid = i.iid
         inner join course c on c.cid = o.cid;
```

4- (Presque) la même requête avec une `natural join`

- différences : une seule colonne `iid`, et les colonnes dans un ordre différent
- non recommandé : le comportement d'une jointure naturelle peut être
imprévisible

```sql
select *
from offering
         natural join instructor;

-- ne fonctionne pas
select *
from offering
         natural join instructor
         natural join course;
```

5- Obtenir les identifiants et les noms des instructeurs enseignant au semestre
d'hiver 2020

- il faut spécifier laquelle des deux colonnes `iid` nous voulons, même si elles
sont égales

```sql
select instructor.iid, name
from offering
         inner join instructor on offering.iid = instructor.iid
where semester = 'W'
  and year = 2020;
```

6- Utiliser `distinct` pour supprimer les doublons

```sql
select distinct instructor.iid, name
from offering
         inner join instructor on offering.iid = instructor.iid
where semester = 'W'
  and year = 2020;
```

7- Obtenir les codes et les noms des cours offerts au semestre d'hiver 2020

```sql
select distinct course.code, course.name
from course
         inner join offering on course.cid = offering.cid
where semester = 'W'
  and year = 2020;
```

8- Obtenir les codes et les noms des cours offerts au semestre d'hiver 2020,
ainsi que les noms des instructeurs

- première tentative : pourquoi cela ne fonctionne-t-il pas ?

```sql
select code, course.name, instructor.name
from offering
         natural join instructor
         natural join course
where semester = 'W'
  and year = 2020;

--- deuxième tentative
select distinct code, course.name, instructor.name
from offering
         inner join instructor on offering.iid = instructor.iid
         inner join course on offering.cid = course.cid
where semester = 'W'
  and year = 2020;
```

9- Qu'en est-il des offres sans instructeur ?

- Le `iid` dans offering peut être nul, mais pas le `cid`
- si `iid` est nul dans offering, il ne correspondra à rien dans instructor
- *jointures externes* : conserver les lignes qui ne correspondent pas

```sql
select o.oid, o.iid, i.iid
from offering as o
         inner join instructor as i on o.iid = i.iid;

select o.oid, o.iid, i.iid
from offering o
         left outer join instructor i on o.iid = i.iid;

select o.oid, o.iid, i.iid
from offering o
         right outer join instructor i on o.iid = i.iid;

select o.oid, o.iid, i.iid
from offering o
         full outer join instructor i on o.iid = i.iid;
```

10- Obtenir les identifiants des cours offerts en 2020, ainsi que les noms des
instructeurs

```sql
select distinct cid, instructor.name as instructor_name
from offering
         left join instructor on offering.iid = instructor.iid
where year = 2020;
```

11- Obtenir les codes et les noms des cours offerts en 2020, ainsi que le
semestre et les noms des instructeurs

```sql
select distinct code,
                course.name     as course_name,
                semester,
                instructor.name as instructor_name
from (offering left join instructor on offering.iid = instructor.iid)
         right join course on offering.cid = course.cid
where year = 2020;
```

12- Trouver les offres sans instructeur

```sql
select *
from offering
where iid is null;
```

13- Trouver les étudiants non inscrits à un cours

```sql
select s.*
from student s
         left join enrollment e on s.sid = e.sid
where oid is null;
```

14- Trouver les cours qui n'ont jamais été offerts

```sql
select c.*
from course c
         left join offering o on c.cid = o.cid
where oid is null;
```

15- Trouver les offres dans lesquelles aucun étudiant n'est inscrit

```sql
select o.*
from offering o
         left join enrollment e on o.oid = e.oid
where e.oid is null;
```
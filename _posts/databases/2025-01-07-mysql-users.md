---
title: "MySQL: Gestion des utilisateurs"
date: 2025-01-07 08:30:00 +0000 
categories: databases
tags: bdd databases sql mysql
author: mdevoldere
--- 


MySQL est un logiciel de gestion de base de données open-source qui aide les développeurs à stocker, organiser et récupérer des données. Un large éventail d’options est disponible pour gérer les autorisations des utilisateurs dans les tables et les bases de données. 

> Si vous utilisez la base de données MariaDB au lieu de MySQL, les concepts présentés dans cet article sont identiques.

# Créer un nouvel utilisateur

Pour le moment, nous avons travaillé dans MySQL en tant que **root** user avec un accès complet à toutes les bases de données. Cependant, dans les cas où vous auriez besoin de configurer des restrictions supplémentaires, il existe des moyens de créer des utilisateurs avec des autorisations spécifiques.

Commençons par créer un nouvel utilisateur dans le shell MySQL :

```sql
CREATE USER 'toto'@'localhost' IDENTIFIED BY 'myPassword';
```

> Remarque : lorsque nous ajouterons des utilisateurs, nous indiquerons que l’hôte de l’utilisateur est `localhost` et non l’adresse IP du serveur. `localhost` est un nom d’hôte qui signifie **cet ordinateur**. MySQL traite tout particulièrement ce nom d’hôte spécifique : lorsqu’un utilisateur avec cet hôte se connecte sur MySQL, il tentera de se connecter au serveur local.

À ce stade, **toto** ne possède aucune autorisation et ne peut rien faire sur les bases de données. En effet, même si **toto** tente de se connecter (avec le mot de passe myPassword), il ne pourra exécuter aucune instruction. En d'autres termes, **toto** peut se connecter au serveur et c'est tout...

Par conséquent, la première chose à faire consiste à octroyer les autorisations nécessaires à l’utilisateur. On limitera ces autorisations aux besoins de l'utilisateur (et indirectement, de l'application qui consommera la base de données).

```sql
/* Octroie tous les privilèges à toto sur toutes les bases de données (*.*) */
GRANT ALL PRIVILEGES ON *.* TO 'toto'@'localhost';
```

Les astérisques de cette commande renvoient à la base de données et à la table (respectivement) auxquelles ils peuvent accéder. Cette commande spécifique permet à l’utilisateur de lire, modifier, exécuter et effectuer toutes tâches sur l’intégralité des bases de données et des tables.

Notez que, dans cet exemple, nous accordons à **toto** un total accès à tout ce qui se trouve dans notre base de données. Bien que cela soit utile pour expliquer certains concepts MySQL, il se peut que cette méthode soit impraticable sur la plupart des cas d’utilisation et expose votre base de données à d’importants risques de sécurité.

> Pour respecter les bonnes pratiques en matière de sécurité un utilisateur ne devrait avoir accès qu'à la base de données sur laquelle il doit travailler.

Une fois que vous en aurez fini avec les autorisations que vous souhaitez configurer pour vos nouveaux utilisateurs, veillez à toujours recharger tous les privilèges.

```sql
/* Recharge les privilèges au niveau du serveur SQL */
FLUSH PRIVILEGES;
```

Maintenant, vos modifications ont pris effet.

Pour ne donner accès à l'utilisateur qu'à une base de données spécifique, on précisera le nom de la base de données à laquelle l'utilisateur devra se connecter pour bénéficier de ces autorisations.

```sql
/* Octroie tous les privilèges à toto sur toutes les tables de la base de données "videos" (videos.*) */
GRANT ALL PRIVILEGES ON videos.* TO 'toto'@'localhost';
```

## Les différentes autorisations utilisateurs

Voici une courte liste des autres autorisations possibles couramment utilisées que vous pouvez accorder aux utilisateurs.

- **ALL PRIVILEGES** : comme nous l’avons vu précédemment, cela donne à l’utilisateur de MySQL un total accès à une base de données désignée (ou un accès global à l’ensemble du système si aucune base de données n’est sélectionnée)
- **CREATE** : permet de créer de nouvelles tables ou bases de données
- **DROP** :permet de supprimer des tables ou des bases de données
- **DELETE** : permet de supprimer des lignes des tables
- **INSERT** : permet d’insérer des lignes dans les tables
- **SELECT** :permet d’utiliser la commande SELECT pour lire des bases de données
- **UPDATE** : permet de mettre à jour les lignes d’une table
- **GRANT OPTION** : permet d’accorder ou de supprimer les privilèges des autres utilisateurs

Pour donner une autorisation à un utilisateur spécifique, vous pouvez utiliser le framework suivant :

```sql 
GRANT type_of_permission ON database_name.table_name TO 'username'@'localhost';
```

Exemples concrets : 

```sql 
/* Accorde à l'utilisateur "username" le droit d'exécuter des commandes "SELECT" sur la table "films" de la base de données "videos" */
GRANT SELECT ON videos.films TO 'username'@'localhost';
```

```sql 
/* Accorde à l'utilisateur "username" le droit d'exécuter des commandes "INSERT" et "UPDATE" sur toutes les tables de la base de données "videos" */
GRANT INSERT, UPDATE ON videos.* TO 'username'@'localhost';
```

```sql 
/* Accorde à l'utilisateur "username" le droit d'exécuter des commandes "SELECT" et "UPDATE" sur toutes les tables de toutes les bases de données du serveur */
GRANT SELECT, UPDATE ON *.* TO 'username'@'localhost';
```

À chaque fois que vous mettez à jour ou modifiez une autorisation, veillez à bien utiliser la commande `FLUSH PRIVILEGES`.

Pour révoquer une autorisation, la structure est pratiquement identique à celle de l’octroi d’autorisation :

```sql
REVOKE type_of_permission ON database_name.table_name FROM 'username'@'localhost';
```

Notez que, lorsque vous révoquez des autorisations, vous devez utiliser la syntaxe **FROM** à la place du **TO** que nous avons utilisé pour accorder des autorisations.

Vous pouvez contrôler les autorisations actuellement accordées à un utilisateur en exécutant la commande suivante :

```sql
SHOW GRANTS FOR 'username'@'localhost';
```

De la même manière que vous pouvez supprimer des bases de données avec **DROP**, vous pouvez utiliser **DROP** pour supprimer complètement un utilisateur :

```sql
DROP USER 'username'@'localhost';
```

# Conclusion

Vous devriez maintenant avoir une meilleure idée sur la manière d’ajouter de nouveaux utilisateurs et de leur accorder différentes autorisations dans une base de données MySQL. 

Vous pouvez continuer d’explorer et d’expérimenter les différents paramètres d’autorisation qui existent pour votre base de données ou en apprendre plus sur certaines configurations MySQL de niveau supérieur.

Pour de plus amples informations sur les bases de MySQL, nous vous encourageons à consulter le site [https://sql.sh](https://sql.sh).

## III. Configuration
### Stockez la configuation dans l'environnement

La *configuration* d'une application c'est tout ce qui est susceptible de varier entre des [déploiement](./codebase) (validation, production, environnement de développement, etc). Cela inclue :

* Les ressources gérées par la base de données, Memcached, ou tout autre [service de stockage](./backing-services)
* Les autorisations (!!credentials) pour des services externes, tel qu'Amazon S3 ou Twitter
* Les valeurs par déploiement, tel que les hôtes canoniques du déploiement (!!Per-deploy values such as the canonical hostname for the deploy)

Les applications stockent parfois la configuration avec des constantes dans le code. C'est une violation des 12 facteurs, qui requiert une **stricte séparation de la configuration et du code**. La configuration peut varier substantiellement à travers les déploiement, alors que ce n'est pas le cas du code.

Un bon moyen de tester si une application a correctement séparé son code, c'est de se demander si l'application pourrait être rendu open-source à tout instant, sans compromettre de données sensibles (mos de passes, identifiants !!credentials).


Notez que cette définition de "configuration" n'inclut **pas** la configuration interne de l'application, tel que `config/routes.rb` avec Rails, ou comment [les modules du noyau sont connecté](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html) dans [Spring](http://spring.io/). Ce type de configuration ne varie pas à travers les déploiement, et est ainsi mieux fait via le code (!!).

Une autre approche de la configuration, c'est d'utiliser des fichiers de configuration qui ne sont pas inclus dans le système de contrôle de version, comme par exemple `config/database.yml` de Rails. C'est une amélioration considérable par rapport à l'utilisation de constantes qui sont versionnées dans le dépôt de code, mais a toujours des faiblesses : il est facile d'ajouter par inadvertance un fichier de configuration dans le dépot. Il y a une tendance à ce que les fichiers de configuration soient dispersés à différents endroits et dans différents formats, rendant ainsi difficile de voir et gérer la configuration à un unique endroit. De plus, ces formats ont tendances à être spécifiques à un language ou un framework.

**Les applications 12 facteurs stockent la configuration dans des *variables d'environnement*** (souvent raccourcies en *variables d'env*, ou *env*). Les variables d'environnement sont faciles à changer entre des déploiement sans changer le moindre code; contrairement aux fichiers de configuration, il y a peu de chance pour qu'elles soient ajoutée au dépot de code accidentellement; et contrairement aux fichiers de configuration custom, ou tout autre mécanisme de configuration comme les propriétés système Java, ce sont des standards agnostiques du langage ou du système d'exploitation.

Un autre aspect de la gestion de configuration, c'est le groupage. Parfois les application regroupent la configuration dans des groupes nommés (souvent appelés les "environnements"), nommés ainsi d'après des déploiement spécifiques, comme les environnement `development`, `test`, et `production` de Rails. Cette méthode ne permet pas de grossir proprement : lorsque l'on ajoute de nouveaux déploiement à l'application, de nouveaux noms d'environnement sont nécessaires, comme `validation` ou `qa`. Quand le projet grossit encore plus, les développeurs vont avoir tendance à ajouter leur propres environnement particuliers, comme `joes-validation`, ce qui entraine une explosion combinatoire de la configuration qui rend la gestion des déploiement de l'application très fragile.

Dans une application 12 facteurs, les variables d'environnement permettent un contrôle granulaire, chacune complètement orthogonale aux autres variables d'environnement. Elles ne sont jamais groupées ensemble en "environnements", mais sont plutôt gérées indépendamment pour chaque déploiement. C'est un modèle qui permet de grossir verticalement en souplesse, lorsque l'application grossit naturellement en un plus grand nombre de déploiement au cours de sa vie.

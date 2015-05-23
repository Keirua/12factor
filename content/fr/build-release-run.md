## V. Build, release, run
### Séparez strictment les étapes d'assemblage et d'exécution

Une [codebase](./codebase) est transformée en un déploiement (non-développement) à travers les étapes suivantes :

* L'*étapes d'assemblage*  est une transformation qui convertit un dépot de code en un !!bundle exécutable appelé !!build. En utilisant une version du code référencée par un commit spécifié lors du processus de déploiement, l'étape de build va chercher toute les [dépendances externes](./dependencies) et compile les fichiers binaires et les !!ressources (assets).
* L'!!*étape de release* prend le !!build produit à l'!!étape de build et la combine avec la [configuration](./config) de déploiement courante. La !!release résultante contient à la fois le !!build et la configuration, et elle est prête pour une exécution immédiante dans l'environnement d'exécution.
* L'*étape d'exécution* (également appelé !!"runtime") fait fonctionner l'application dans l'environnement d'exécution, en lancant un ensemble de [processus](./processes) de l'application associée à la release considérée.

![Le code devient une build, qui est combiné à la configuration pour créer une release](/images/release.png)

**Les applications 12 facteurs ont une séparation stricte entre les étapes de build, de release et d'exécution.** Par exemple, il est impossible de faire des changements dans le code au moment de son exécution, car il n'y a pas moyen de propager ces changements vers l'étape de build.

**The twelve-factor app uses strict separation between the build, release, and run stages.**  For example, it is impossible to make changes to the code at runtime, since there is no way to propagate those changes back to the build stage.

Les outils de déploiement offrent généralement des outils de gestion de release, permettant notamment de revenir à une release antérieure. Par exemple, l'outil de déploiement [Capistrano](https://github.com/capistrano/capistrano/wiki) stocke les release dans un sous-répertoire appelé `releases`, où la release courante est un lien symbolique vers le répertoire de release courante. Sa commande `rollback` permet de facilement revenir à une release précédente.

Chaque release devrait toujours avoir un identifiant unique, comme un horodatage (timestamp) de la release (tel que `2011-04-06-20:32:17`) ou un nombre incrémental (tel que `v100`). Les liste des releases est accessible en écriture uniquement, et il n'est pas possible de modifier une release une fois qu'elle a été réalisée. Tout changement doit créer une nouvelle release.

Les !!build sont initiées par le développeur de l'application dès que du nouveau code est déployé. Son exécution, au contraire, peut avoir lieu automatiquement en cas d'un reboot du serveur, ou du crash d'un processus qui est relancé par le gestionnaire de processus. De ce fait, l'étape d'exécution doit se limiter à un nombre minimal de parties mobiles, car les problèmes qui empêchent une application de fonctionner peuvent entrainer des disfonctionnements au milieu de la nuit alors qu'aucun développeur ne sera là pour les corriger. L'étape de !!build peut être plus complexe, car les erreurs pourront toujours être résolus par le développeur qui réalise le déploiement.


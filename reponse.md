Question 1 : Dans le contexte d'une usine logicielle, un artefact est un fichier ou un ensemble de fichiers généré de manière automatique lors des différentes étapes du pipeline CI/CD (intégration et déploiement continus). Il représente un élément concret et immutable produit à partir du code source, prêt à être testé, déployé ou archivé. Les artefacts servent de base pour garantir la traçabilité et la reproductibilité des déploiements entre les différents environnements.

Voici trois exemples d'artefacts différents :
1. Une image Docker : il s'agit d'un artefact conteneurisé incluant l'application et toutes ses dépendances, prêt à être exécuté sur un registre comme Artifact Registry.
2. Un package binaire ou une archive (ex: fichier .jar pour Java, .whl pour Python) : c'est le résultat de la compilation ou du packaging du code source.
3. Un rapport de test ou de couverture de code (ex: fichier HTML ou XML généré par Pytest ou JUnit) : bien qu'il ne soit pas déployé, c'est un artefact de documentation de la qualité produit par le pipeline.

Question 2 : Le fait de tagger une image Docker avec plusieurs tags permet de concilier traçabilité technique, lisibilité humaine et simplicité d'utilisation.

Voici l'utilité de chaque tag :
- SHA complet (ex: 40 caractères) : C'est l'identifiant unique et immuable issu du commit Git. On l'utilise principalement pour une traçabilité absolue et automatisée dans les systèmes de déploiement, car il garantit que l'on déploie exactement la version correspondant à un commit précis.
- SHA court (7 caractères) : Il offre les mêmes avantages que le SHA complet en termes de traçabilité, mais il est beaucoup plus lisible pour un humain (par exemple dans une commande `docker images` ou une console cloud). Il est souvent utilisé par les développeurs pour identifier rapidement une version sans manipuler de longues chaînes de caractères.
- latest : C'est un tag flottant qui pointe toujours vers la version la plus récente de l'image (généralement issue du dernier build sur la branche principale). On l'utilise pour faciliter le téléchargement de la "dernière version" sans avoir à connaître son identifiant spécifique, par exemple dans des environnements de développement ou de test local. Cependant, il est déconseillé pour la production car il n'est pas immuable.

Question 3 : Le versioning sémantique (SemVer) est un système de numérotation de version normalisé sous la forme X.Y.Z (MAJOR.MINOR.PATCH) qui permet de communiquer clairement la nature des changements effectués dans le code.
- MAJOR (X) : Incrémenté lors de changements incompatibles avec les versions précédentes (breaking changes).
- MINOR (Y) : Incrémenté lors de l'ajout de fonctionnalités tout en maintenant la rétrocompatibilité.
- PATCH (Z) : Incrémenté pour des corrections de bugs rétrocompatibles.

Voici la classification pour les cas demandés :
- Ajout d'une route : MINOR (nouvelle fonctionnalité rétrocompatible).
- Correction d'un bug : PATCH (correction d'un comportement existant sans ajout de fonctionnalité).
- Changement du format de réponse JSON : MAJOR (changement incompatible qui risque de casser les clients consommant l'API).

Question 4 : La différence entre un tag Git léger et un tag annoté réside dans la quantité d'informations stockées et leur usage :
- Tag léger : C'est une simple référence (un pointeur) vers un commit spécifique, un peu comme une branche qui ne bougerait jamais. Il ne contient aucune information supplémentaire (ni auteur, ni date, ni message). On l'utilise généralement pour des besoins temporaires ou locaux.
- Tag annoté : C'est un objet complet dans la base de données Git. Il contient le nom de l'auteur, son adresse e-mail, la date de création, une signature GPG optionnelle et surtout un message de description. Ils sont recommandés pour marquer des versions officielles (releases) car ils assurent une meilleure traçabilité et pérennité des informations liées à la version.

Question 5 : Release-please s'appuie exclusivement sur la syntaxe des **Conventional Commits** pour déterminer le prochain numéro de version. Il analyse l'historique des messages de commit depuis le dernier tag de version :
- S'il trouve un commit de type `feat:`, il incrémente le numéro **MINOR** (Y dans X.Y.Z).
- S'il trouve un commit de type `fix:`, il incrémente le numéro **PATCH** (Z dans X.Y.Z).
- S'il détecte un "BREAKING CHANGE" (indiqué par un `!` après le type ou une mention dans le footer du commit), il incrémente le numéro **MAJOR** (X dans X.Y.Z).
Le lien est donc direct : la discipline de nommage des commits devient la source de vérité pour le cycle de vie des versions du logiciel.

Question 6 : L'automatisation des releases présente plusieurs avantages majeurs :
- **Réduction des erreurs humaines** : On évite les erreurs de saisie dans les numéros de version ou l'oubli de la mise à jour d'un fichier de métadonnées (comme `pyproject.toml`).
- **Génération automatique du Changelog** : Le document retraçant les évolutions est toujours à jour et reflète exactement le contenu de la version, sans effort manuel de rédaction.
- **Cohérence et Standardisation** : Toutes les versions suivent la même logique (SemVer) et le même processus, ce qui facilite le travail des équipes de développement et d'exploitation (DevOps).
- **Gain de temps** : Les développeurs peuvent se concentrer sur le code plutôt que sur les tâches administratives de gestion de version.

Question 7 : Le pipeline de release complet repose sur deux workflows distincts :
1. **Workflow CI (`ci.yml`)** : Il se déclenche à chaque push ou Pull Request. Son rôle est de valider la qualité et la sécurité du code (linting, tests unitaires, scans de sécurité). Il ne déploie rien.
2. **Workflow Release (`release.yml`)** : Il se déclenche à chaque push sur `main`. Il utilise `release-please` pour maintenir une PR de release. Une fois cette PR mergée, le workflow crée un tag Git et une release GitHub, ce qui déclenche alors le job de build et de déploiement de l'image Docker avec le tag de version correspondant.

Le flux est le suivant : Commit -> CI (Validation) -> PR de Release -> Merge PR -> Tag/Release -> Déploiement.

Question 8 :
- **Déploiement avec `github.sha` (TP5)** : Chaque modification du code entraîne un nouveau build et un déploiement. On déploie un état instantané du code. C'est idéal pour des environnements d'intégration continue (dev, staging) où l'on veut tester rapidement chaque changement.
- **Déploiement avec Tag SemVer (ce TP)** : On ne déploie que des versions "officielles" et identifiées. C'est la pratique recommandée pour la production. Cela permet de savoir exactement quelle version est en ligne, de consulter son changelog et de revenir facilement à une version précédente (rollback) en connaissant son impact fonctionnel.

Question 9 : L'immutabilité signifie qu'un artefact (comme une image Docker) ne doit jamais être modifié une fois créé. Un tag spécifique (ex: `v1.1.0`) doit toujours pointer vers le même contenu binaire.
C'est crucial pour la **reproductibilité** et la **fiabilité**. Si l'on écrase un tag Docker existant :
- On perd la traçabilité : deux déploiements portant le même numéro de version pourraient avoir des comportements différents.
- Le rollback devient impossible ou risqué : revenir à la "v1.0.0" pourrait nous ramener vers une version corrompue si le tag a été écrasé.
- Les systèmes de cache (nodes Kubernetes, serveurs) pourraient ne pas télécharger la nouvelle version s'ils possèdent déjà une image avec ce tag, entraînant des incohérences entre les instances d'une même application.

Question 10 : J'ai analysé les 5 dernières releases du projet **Flask** (https://github.com/pallets/flask/releases) :
1. **3.1.3** (PATCH) : Correction de bugs mineurs.
2. **3.1.2** (PATCH) : Correction de bugs mineurs.
3. **3.1.1** (PATCH) : Correction de bugs mineurs.
4. **3.1.0** (MINOR) : Ajout de fonctionnalités et mise à jour des dépendances minimales (ex: Werkzeug >= 3.1).
5. **3.0.3** (PATCH) : Correction de bugs.

L'analyse montre que :
- Les types de changements sont principalement des PATCH (corrections) et une version MINOR récente.
- Le changelog semble automatisé via des outils de la suite Pallets (vraisemblablement intégrés à leur processus de build Python).
- Les tags suivent rigoureusement le standard **SemVer** (MAJOR.MINOR.PATCH).
- Le projet utilise des outils comme `pip-tools` pour la gestion des dépendances et s'appuie sur les fonctionnalités natives de GitHub pour la publication des releases.
- Lien vers la page Releases : https://github.com/pallets/flask/releases

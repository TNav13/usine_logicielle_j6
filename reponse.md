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

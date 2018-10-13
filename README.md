# Ant-JUnit-Bootcamp

Dans l'écosystème Java, il existe 3 outils populaires de construction (conversion de code source pour la compilation par exemple, et autres artefacts) à savoir:

- Apache Ant
- Maven
- Gradle

## Apache Ant

Ant, une abréviation pour Another Neat Tool est principalement utilisé pour la construction et le déploiement de projets java, il est utilisé pour toutes les taches répétitives tel que la génération du JavaDoc

Un processus de construction Java comprend les opérations suivantes:

- La compilation du code source Java en bytecode Java
- La création du fichier .jar pour la distribution du code
- La création de documentation Javadoc

Ant utilise le fichier XML "build.xml" pour sa configuration, les constructions étant basées sur 3 blocs:

- tâches (task)
- cibles (target)
- point d'extension (extension points)

Une tâche est une unité de travail à réaliser et qui doit être atomique (ou indivisible) comme par exemple le lancement de la compilation du code source ou la génération du Javadoc, les tâches peuvent être regroupées en cibles. Vous comprendrez donc que pour la réalisation de ces tâches, Ant aura besoin du kit de développement Java (JDK)

Une cible peut être directement invoquée par Ant, les cibles peuvent spécifier leur dépendances. Ant exécutera automatiquement toutes les cibles dépendantes.

Si aucune cible n'est spécifiée, alors Ant exécutera la cible par défaut définie dans le fichier build.xml.

Sur Linux, linstallation est simple

```
# Pour Debian, Ubuntu:

sudo apt-get installa ant

# Pour Redhat, Fedora ou CentOS

sudo yum install ant



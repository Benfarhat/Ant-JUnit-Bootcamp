# Ant-JUnit-Bootcamp

Dans l'écosystème Java, il existe 3 outils populaires de construction (conversion de code source pour la compilation par exemple, et autres artefacts) à savoir:

- Apache Ant
- Maven
- Gradle

## Apache Ant

### Présentation de Ant

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

## Installation de Ant

Sur Linux, l'installation est simple

```
# Pour Debian, Ubuntu:

sudo apt-get install ant

# Pour Redhat, Fedora ou CentOS

sudo yum install ant

# Pour vérifier l'installation, assurez vous que la commande suivante réponde normalement

ant -version

```

Sur Windows il faut se rendre à l'adresse suivante: [https://ant.apache.org/bindownload.cgi](https://ant.apache.org/bindownload.cgi) pour télécharger l'un des binaires de distributions qui se trouve dans l'un des formats suivants (zip, tar.gz et tar.bz2)

Après avoir décompréssé le fichier d'installation (de préférence avec un outil tar puisque le site annonce qu'il contient des noms de liens très long) dans un répértoire de votre choix vous devriez avoir l'arborescence suivante:

```
  ant
   +--- bin  // contains launcher scripts
   |
   +--- etc // contains xsl goodies to:
   |         //   - create an enhanced report from xml output of various tasks.
   |         //   - migrate your build files and get rid of 'deprecated' warning
   |         //   - ... and more ;-)
   +--- lib  // contains Ant JARs plus necessary dependencies
   |
   +--- manual  // Ant documentation (a must read ;-)
   |   
   |
   +--- README, LICENSE, fetch.xml, other text files. //basic information 
```

- Définisez la variable d'environnement ANT_HOME au repertoire précédemment choisir
- Rajouter dans la variable PATH d'environnement `%ANT_HOME%\bin;`

Au final ce sont les élément suivant qui doivent être résolus:

- requis: %ANT_HOME%\bin\ant.bat
- optionnel: %JAVA_HOME%\bin\java.exe
- requis: %PATH%=...eventuelles-entrees...;%ANT_HOME%\bin;...autres-eventuelles-entrees...

Donc une installation manuelle serait comme suit pour Windows and OS/2

```
set ANT_HOME=c:\ant
set JAVA_HOME=c:\jdk1.7.0_51
set PATH=%PATH%;%ANT_HOME%\bin
```

Pour Linux/Unix (bash)

```
export ANT_HOME=/usr/local/ant
export JAVA_HOME=/usr/local/jdk1.7.0_51
export PATH=${PATH}:${ANT_HOME}/bin
```

Et pour Linux/Unix (csh)

```
setenv ANT_HOME /usr/local/ant
setenv JAVA_HOME /usr/local/jdk/jdk1.7.0_51
set path=( $path $ANT_HOME/bin )
```

Vérifions que l'installation s'est bien passée

```
C:\Users\PC>echo ANT_HOME
ANT_HOME

C:\Users\PC>echo %ANT_HOME%
D:\apache-ant-1.10.5

C:\Users\PC>ant
Buildfile: build.xml does not exist!
Build failed

C:\Users\PC>

```

## Utilisation de Ant
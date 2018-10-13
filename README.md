# Ant-JUnit-Bootcamp

Dans l'écosystème Java, il existe 3 outils populaires de construction (conversion de code source pour la compilation par exemple, et autres artefacts) à savoir:

- Apache Ant
- Maven
- Gradle

## Apache Ant

![](https://upload.wikimedia.org/wikipedia/commons/2/2f/Apache-Ant-logo.svg)

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

Créeons un projet Java avec deux classes

Example sous le package com.benfarhat.antjunitbc

et

Test sous le package com.benfarhat.test


## Les Tests

Les tests vont nous permettre de valider qu'un code fait exactement ce qu'on attend de lui, Tester l'Etat attendu via les "state testing" et Tester qu'il exécute la séquence d'évènements attendue via les "Behavior tests"

Les frameworks de tests sont nombreux, dans le monde Java, les plus populaires sont JUnit et TestNG


### Que doit on testé?

Il n'y a pas d'entente générale sur ce qui doit testé ou pas, certains pensent que chaque déclaration de code doit l'être, d'autre non, certains mania veulent atteindre une couverture de code de 100% qui dans des gros projets et irréalisable, mais la norme se situe autour de 80%. 
Par exemple les méthodes triviales n'ont pas besoin d'être testées tel que les setter ou les getter (La JVM le fait déjà)

Dans tous les cas, il faut écrire des tests pour chaque partie de code jugée critique et complexe. pour toute nouvelle fonctionalité ou correction de défaut et il faut également vérifier que cette dernière n'apporte pas de regression de code, c'est à dire vérifier que ce qui fonctionnait avant la modification de code, continue à fonctionner

### Quand doit on tester notre code?

Pendant kes phase de maintenance ou durant le développement. Les tests sont vitaux si vous utiisez une méthodologie de developpement basée sur les tests ou le comportement (TDD et BDD) 
Il y a également des tests à refaire et d'autres non (la selection doit être pertinente pour éviter que cela ne devienne couteux)

### JUnit

JUnit est un framework de test qui utilise les annotations pour identifier les méthodes de Test, il offre:

- Des primitives pour créer un test (assertions)
- Des primitives pour gérer des suites de tests
- Des facilités pour l'exécution des tests
- Des statistiques pour la converture de code réalisée par les tests
- Des point d'extensions pour des situations spécifiques

Pour chaque Fichier Foo.java nous devons théoriquement avoir un fichier FooTest.java qui inclut junit.framework.*
Dans FooTest.java pour chaque classe Foo de Foo.java, écrire une classe FooTest qui hérite de TestCase, c'est la classe de test
Dans FooTest définir les méthodes suivante:

- Le constructeur qui initialiser la nom de la suite de tests
- setUp qui est appelé avant chaque test pour la préparation qui est annoté par @Override
    - Il est possible de donner un autre nom à la méthode et de l'annoter avec @Before
- tearDown qui est appelé après chaque test pour le nettoyage et qui est annoté par @Override
    - Il est également possible d'avoir une autre méthode a exécuté après les tests via l'annotation @After
- une ou plusieurs méthodes dont le nom est préfixé par test et qui implémentent les tests unitaires, ce sont les méthodes de test
    - On peut également identifier une méthode de test via l'annotation @Test
- suite qui appele les tests unitaires
- main quiappelle l'exécution de la suite

Les annotations fréquemment utilisées sont 

- @Before : Exécuté avant chaque test de cas
- @BeforeClass : Exécuté avant l'initialisation de la classe
- @After : Exécuté après chaque test
- @AfterClass : Exécuté à la fin de la Class
- @Test : Indique une méthode de test
- @Ignore : Comme son nom l'indique, elle permet d'ignorer une méthode de test même si celle ci est annoté @Test
- @Test(timeout=500) : Cette annotation permet de retarder l'exécution du test d'un nombre prédéfini de millisecondes
- @Test(expected=IllegalArgumentException.class): Permet de tester une exception

Nous aurons éventuellement ce genre d'exécution:
    @BeforeClass

        @Before
            @Test
        @After

        @Before
            @Test
        @After

        @Before
            @Test
        @After

    @AfterClass


Attention il n'est pas possible d'avoir une variable qui permettrait de faire un partage d'état entre les méthodes puisque pour chaque méthode de test, JUnit créer une nouvelle instance et pour chacun il exécutera les méthodes setUp et tearDown (ou respectivement les annotées @Before et @After)
Dans les méthodes de tests unitaires, les méthodes testées sont appelées et leur résultat est testé à l'aide d'assertions (en français affirmation):

* assertEquals(a,b)
    * Teste sir a est égale à b, a et b sont soit des valeurs primitives, soit des objets possédant une méthode equals
* asserTrue(exp) et assertFalse(expr)
    * testent si l'expression booléenne expr est vrai resp. faux
* assertSame(a,b) et assertNotSame(a,b)
    * testent si a et b réfèrent au même objet ou  on
* assertNul(a) et assertNotNull(a)
    * testent sur l'objet a est null ou non
* fail(message)
    * Si le test doit échouer, permet de lever une exception


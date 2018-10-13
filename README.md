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

Main sous le même package


Soit les contenus des deux classes

```

// Example.java

package com.benfarhat.antjunitbc;

public class Example {
	public boolean testPrime(int number){		
		int quotient = number/2;		
		boolean prime = true;		
		for(int i=2; i<=quotient; i++){
			if(number%i == 0){
				prime = false;
				break;
			}
		}		
		return prime;
	}
	
}

// Main.java

public class Main {
	public static void main(String[] args) {
		Example pm = new Example();
		int number1 = 20;
		System.out.println("Liste des nombre premier inférieur à 100");
		System.out.println("****************************************");
		for(int i = 1; i < 100; i++) {
			if(pm.testPrime(i))
				System.out.println(i + " est premier !");
		}
	}
}

```

### La méthode manuelle

Création de nos bytecode

```
javac -sourcepath src -d ./testbuild/classes/ ./src/com/benfarhat/antjunitbc/Main.java
```

Exécution du code

```
java -cp ./build/classes/ com.benfarhat.antjunitbc.Main
```

Préparons notre fichier Manifest, nous allons déclarer le point d'entrée (Entry Point) de notre application, celui pointe sur la classe Main
Notez que la dernière ligne du manifest doit être vide et qu'il doit être encodé en UTF8, le contenu va permettre de dire comment le fichier jar sera exécuté

```
echo Main-class: com.benfarhat.antjunitbc.Main>MyManifest
```

A présent créons notre fichier jar

La commande de base est:

```
jar cfm jar-file manifest-addition input-file(s)
```

- L'option c indique que vous souhaitez créer un fichier JAR.
- L'option m indique que vous souhaitez fusionner les informations d'un fichier existant.
- L'option f indique que vous souhaitez exporter plutôt que vers une sortie standard (le fichier JAR que vous créez).
- manifest-add est le nom (ou le chemin) du fichier texte existant.
- jar-fichier est le nom que vous souhaitez que le fichier JAR.
- L'argument input-file (s) est une liste d'un ou de plusieurs fichiers que vous souhaitez inclure dans votre fichier JAR, séparés par des espaces.

```
mkdir -p ./testbuild/jar
jar cfm ./testbuild/jar/antjunitbc.jar MyManifest -C ./testbuild/classes/ .
```

Testons le fichier nouvellement créer:

```
java -jar ./testbuild/jar/antjunitbc.jar
Liste des nombre premier infÃ©rieur Ã  100
****************************************
1 est premier !
2 est premier !
3 est premier !
5 est premier !
7 est premier !
11 est premier !
13 est premier !
17 est premier !
19 est premier !
23 est premier !
29 est premier !
31 est premier !
37 est premier !
41 est premier !
43 est premier !
47 est premier !
53 est premier !
59 est premier !
61 est premier !
67 est premier !
71 est premier !
73 est premier !
79 est premier !
83 est premier !
89 est premier !
97 est premier !

```

#### Les formats connexes au fichier JAR

- Les fichiers WAR (Web Application aRchive) contenant des fichiers XML, des classes Java, des JSP et les objets necessaires pour une application Web
- Les fichiers EAR (Enterprise Application aRchive) contenant des fichiers XML, des classes Java et les objets necessaires pour une application d'entreprise
- Les fichiers RAR (Resource Adapter aRchive) contenant des fichier XML, des classes Java et les objets necessaires pour l'architecture de connecteurs (JCA) de la plateforme J2EE
- Les fichiers AAR (Apache Axis aRchive) spécifique pour le déploiement de service web en technologie SOAP sous Axis 2

### Construction via Ant

A présent nous allons créer notre fichier build.xml comme suit, les commentaires permettront d'expliquer chaque directive, noter qu'il doit toujours y avoir dans un projet une tache par défaut permettant d'appeler Ant sans nom de tâche.

```
<?xml version="1.0"?>
<project name="Ant-Test" default="main" basedir="."> 
	<presetdef name="javac">    
		<javac includeantruntime="false" />  
	</presetdef>
	<!-- On va créer une timestamp dans la variable TODAY_DATE_AS_FILENAME pour l'utiliser dans le nom du fichier jar-->
	<tstamp>
		<format property="TODAY_DATE_AS_FILENAME" pattern="yyyyMMdd-HHmmss"  locale="fr,FR" />
	</tstamp>
    <!-- Déclaration de variable a utiliser plus tard. -->
    <!-- La valeur des propriété est accessible via ${} -->
    <property name="src.dir" location="src" />
    <property name="build.dir" location="bin" />
    <property name="dist.dir" location="dest" />
    <property name="docs.dir" location="docs" />

    <!-- On néttoie les build existant-->
    <target name="clean">
		<!-- Si il est impossible de supprimer le repertoire, pas besoin de faire échouer la target-->
    	<delete failonerror="false" dir="${build.dir}" />
        <delete failonerror="false" dir="${docs.dir}" />
        <delete failonerror="false" dir="${dist.dir}" />
    </target>

    <!-- On créer les répetoire pour les builds-->
    <target name="makedir">
        <mkdir dir="${build.dir}" />
        <mkdir dir="${docs.dir}" />
        <mkdir dir="${dist.dir}" />
    </target>

    <!-- Compile le code java (incluant l'utilisation de JUnit) -->
    <target name="compile" depends="clean, makedir">
        <javac srcdir="${src.dir}" destdir="${build.dir}">
        </javac>

    </target>

    <!-- Creation des Javadoc -->
    <target name="docs" depends="compile">
        <javadoc packagenames="src" sourcepath="${src.dir}" destdir="${docs.dir}">
            <!-- Définition des fichier et repertoire à inclure, nous incluons tout -->
             <fileset dir="${src.dir}">
                <include name="**/*.java" />
             </fileset>
        </javadoc>
    </target>

    <!--Création du fichier jar de deploiement -->
    <target name="jar" depends="compile">
        <jar destfile="${dist.dir}\ant.junit.bootcamp.${TODAY_DATE_AS_FILENAME}.jar" basedir="${build.dir}">
            <manifest>
            	<!-- Définition le point d'entrée -->
                <attribute name="Main-Class" value="com.benfarhat.antjunitbc.Main" />
            </manifest>
        </jar>
    </target>

    <target name="main" depends="compile, jar, docs">
        <description>Main target</description>
    </target>

</project>
```

A présent pour l'executer vous pouvez le faire via Eclipse ou en ligne de commande via la commande 

```
ant
Buildfile: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\build.xml
Trying to override old definition of task javac

clean:
   [delete] Deleting directory C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\bin
   [delete] Deleting directory C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\docs
   [delete] Deleting directory C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\dest

makedir:
    [mkdir] Created dir: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\bin
    [mkdir] Created dir: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\docs
    [mkdir] Created dir: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\dest

compile:
    [javac] Compiling 2 source files to C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\bin

jar:
      [jar] Building jar: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\dest\ant.junit.bootcamp.20181013-145451.jar

docs:
  [javadoc] Generating Javadoc
  [javadoc] Javadoc execution
  [javadoc] Loading source file C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\src\com\benfarhat\antjunitbc\Example.java...
  [javadoc] Loading source file C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\src\com\benfarhat\antjunitbc\Main.java...
  [javadoc] Constructing Javadoc information...
  [javadoc] javadoc: warning - You have not specified the version of HTML to use.
  [javadoc] The default is currently HTML 4.01, but this will change to HTML5
  [javadoc] in a future release. To suppress this warning, please specify the
  [javadoc] version of HTML used in your documentation comments and to be
  [javadoc] generated by this doclet, using the -html4 or -html5 options.
  [javadoc] Standard Doclet version 10.0.2
  [javadoc] Building tree for all the packages and classes...
  [javadoc] Building index for all the packages and classes...
  [javadoc] Building index for all classes...
  [javadoc] Generating C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\docs\help-doc.html...
  [javadoc] 1 warning

main:

BUILD SUCCESSFUL
Total time: 8 seconds
```

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

![](https://developer.ibm.com/dwblog/wp-content/uploads/sites/73/dwblog-junit2-450x225.png)

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


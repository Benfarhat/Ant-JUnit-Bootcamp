# Ant-JUnit-Bootcamp

Dans l'écosystème Java, il existe 3 outils populaires de construction (conversion de code source pour la compilation par exemple, et autres artefacts) à savoir:

- Apache Ant
- Maven
- Gradle

## Apache Ant

![](https://upload.wikimedia.org/wikipedia/commons/2/2f/Apache-Ant-logo.svg)

### Présentation de Ant

Ant, une abréviation pour Another Neat Tool est un projet du groupe Apache-Jakarta. Son but est de fournir un outil indépendant de toute plate-formepour la construction d'applications java, il est utilisé pour toutes les taches répétitives tel que la génération du JavaDoc, la compilation, les tâches pre et post compilation
Il est principalement vu comme une analogie à l'outil make sous Unix

Un processus de construction Java comprend les opérations suivantes:

- La compilation du code source Java en bytecode Java
- La création du fichier .jar pour la distribution du code
- La création de documentation Javadoc

Ant utilise et repose sur un fichier de configuration XML décrivant les différentes tâches de construction. les constructions étant basées sur 3 éléments:

- Les cibles (targets) qui sont les étapes du projet de construction
- Les taches (tasks) qui sont les traitements unitaires à réaliser
- Les propriétés (properties) qui sont des couples variables/valeurs utilisables par d'autres éléments (cibles ou tâches)

Une tâche est une unité de travail à réaliser et qui doit être atomique (ou indivisible) comme par exemple le lancement de la compilation du code source ou la génération du Javadoc, les tâches peuvent être regroupées en cibles. Vous comprendrez donc que pour la réalisation de ces tâches, Ant aura besoin du kit de développement Java (JDK)

Une cible peut être directement invoquée par Ant, les cibles peuvent spécifier leur dépendances. Ant exécutera automatiquement toutes les cibles dépendantes.

Si aucune cible n'est spécifiée, alors Ant exécutera la cible par défaut définie dans le fichier de configuration.
Par défaut il s'agit du fichier build.xml, sinon il est possible de spécifier un autre fichier:

```
ant -buildfile monFichierDeBuild.xml
```
Noter que les chemins indiqués dans ce fichier utilisent tous comme séparateur le slash"/" (même sous Windows).

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

Dans notre fichier build.xml nous allons définir un projet qui a comme attributs un "name" (nom du projet), un attribut "default" (précisant le cible par défaut à utiliser) et un attribut "basedir" qui donnera le répertoire de référence à utiliser avec les chemins relatifs.

`<project name="Ant-Test" default="main" basedir="."> `

Les commentaires sont identique à ceux du HTML (XML)

`<!-- Je suis un commentaire -->`

Pour définir une variable (donc réutilisable dans le fichier) on utilise les "property"

```
<property file="autreFichierDeProperty.properties" />
<property name="variable" value="valeur" />
<property name="projet.nom" value="Ant-test" />
<property name="projet.version" value="01.0" />
```
Pour accéder à la valeur d'une variable il faut faire: `${variable}`

Les filesets permettent de définir l'ensemble de fichiers sur lesquelles serons appliquer une tache donnée, l'expression `**/` permet de dire quelques soit le sous répertoire, par exemple pour appliquer une tache aux fichiers ayant l'extension .java uniquement on fait `<fileset dir="src" includes="**/*.java">`, pour utiliser une liste de fichier on fera `<filelist dir="texte" files="fichier1.txt,fichier2.txt" />` le `fileset` peut être affiné en utilisant des includes ou excludes dans un `patternset`, par exemple pour cibler tous les fichier java sauf ceux dans un répertoire contenant dans son nom le terme Test on fait:
```
<patternset id="non.test.sources">
  <include name="**/*.java"/>
  <exclude name="**/*Test*"/>
</patternset>
```

On pourrait également utiliser la directive `echo` pour afficher un message ou pour écrire dans un fichier 

``` 
<echo message="Debut des traitements" />
<echo>
Fin des traitements du projet ${ant.project.name}
</echo>
<echo file="${basedir}/log.txt" append="false" message="Debut des traitements" />
<echo file="${basedir}/log.txt" append="true" >
</echo> 
```

la directive `mkdir` est utilisé pour créer un repertoire `<mkdir dir="${basedir}/gen" />`, `copy` permet de faire une copie:

```
    <copy todir="${projet.bin.dir}" >
      <fileset dir="${projet.sources.dir}" >
        <include name="**/*.properties"/>
        <include name="**/*.hbm.xml"/>
        <include name="**/*.cfg.xml"/>
      </fileset>
    </copy>
```

Alors que la directive `delete` permet de supprimer un fichier ou des repertoires:

```
    <delete dir="${basedir}/gen" />
    <delete file="${basedir}/log.txt" />
    <delete>
      <fileset dir="${basedir}/bin" includes="**/*.class" />
    </delete>
```
La tâche `tstamp` permet de définir trois propriétés utilisable dans le code:

- DSTAMP : initialisée avec la date du jour au format AAAMMJJ
- TSTAMP : initialisée avec l'heure actuelle sous la forme HHMM
- TODAY : initialisée avec la date du jour au format long

Enfin les directives `java` (pour lancer une machine virtuelle), `javac` (pour la compilation), `javadoc` (pour la génération du javadoc) et `jar` (pour la création del'archive jar) sont les incontournables d'un fichier xml de configuration

### Notre fichier de configuration
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

D'autres options sont disponibles comme par exemple `-quiet` pour founir un minimum d'informations lors de l'exécution, `-verbose` pour au contraire en fournir un maximum, `-version` que nous avons déjà vu et qui renseigne sur la version de Ant et `-projecthelp` qui permet d'afficher les cibles définies dans le projet ainsi que leur description, ce qui permet de ne pas avoir besoin d'afficher son contenu pour lancer une tache avec ant.

```
$ ant -projecthelp
Buildfile: C:\Users\PC\eclipse-workspace\Ant-Junit-Bootcamp\build.xml
Trying to override old definition of task javac
Main target
Main targets:

Other targets:

 clean
 compile
 docs
 jar
 main
 makedir
Default target: main

```


## Les Tests

Les tests vont nous permettre de valider qu'un code fait exactement ce qu'on attend de lui, Tester l'Etat attendu via les "state testing" et Tester qu'il exécute la séquence d'évènements attendue via les "Behavior tests"

Il y a différents types de tests:

- tests unitaires
- tests d'intégration
- tests de recette
- tests de charge
- tests de stress
- tests d'acceptabilit
- tests de sécurité
- ...

Les frameworks de tests sont nombreux, dans le monde Java, les plus populaires sont JUnit et TestNG

Pour l'automatisation des tests unitaires nous avons XUnit et TestNG
Pour la couverture de code: EMMA et Cobertura
Pour les tests des IHM: Selenium pour les applications web (voir Selenium IDE)
Pour les test fonctionnels: fitNesse

C'est sur les tests unitaires avec JUnit que nous allons nous concentrer

### Comment se passe un test unitaire

Il se déroule en étapes

- setup: pour initialiser des objets ou des ressources
- call: pour exécuter le code à vérifier
- verify: pour vérifier les données issues des traitements
- teardown: pour nettoyer et libérer des ressources

Les tests unitaires automatisés sont très importants et ce durant tout le cycle de vie d'une application:

- conception: redaction de la liste des cas de tests
- développemet: tests du code, détection précoce de bugs
- maintenance: tests de non regression, encouragent et facilient le refactoring

Les tests unitaires automatisés sont primordiaux lors de l'utilisation de méthodologies comme XP (eXtreme Programming) et TDD (Test Driven Development) puisqu'ils sont garant de qualité et de fiabilité du code. Un test automatisé s'exécutera par exemple à chaque commit

Par expérience, si vous avez trop de test à faire pour valider une seule méthode, alors dites vous qu'il faut la réecrire (refactorer) jusqu'à:

- Améliorer la granularité des méthodes
- Réduire la dépendance entre les objets en utilisant certains design pattern
- Une classe avec un couplage fort vers d'autres classes est difficile à tester

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

Notez qu'il y a également les assumptions qui sont équivalent aux assertions sans pour autant faire échouer les tests


### JUnit 5 (JUnit Jupiter)

Beaucoup font la confusion entre les différentes annotations, c'est pour cela que dans un soucis de clarté, JUnit 5 à préféré renommer:

- @BeforeClass  -> @BeforeAll
- @Before       -> @BeforeEach
- @After        -> @AfterEach
- @AfterClass   -> @AfterAll
- @Ignore       -> @Disabled
- @Category     -> @Tages

D'autres annotations sont intéréssantes comme:

- @DisplayName("Nom à afficher dans les résultats de test") 
- @RepeatedTest(10) 
- @EnabledIf(condition)

Les annotations sont dans le package org.junit.jupiter.api
Les assertions dans org.junit.jupiter

## Ecriture de testsCi joint la 

Prenons la classe ExampleTest contenant le code suivant:

```
package com.benfarhat.antjunitbc;

import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.*;
import org.junit.jupiter.params.*;
import org.junit.jupiter.params.provider.*;

class ExampleTest {

	@ParameterizedTest
	@ValueSource(ints = { 13, 29, 47, 67, 89 })
	@DisplayName("Test des nombres premiers")
	void withValueSource(int number) {
        Example pm = new Example();
        assertTrue(pm.testPrime(number));
	}
	
    @Test
	@DisplayName("Test avec des nombres divers")
    public void testPrime() {
        Example pm = new Example();
        assertTrue(pm.testPrime(13));
        assertTrue(pm.testPrime(47));
        assertTrue(pm.testPrime(89));
        assertFalse(pm.testPrime(14));
        assertFalse(pm.testPrime(42));
        assertFalse(pm.testPrime(75));
    }
}
```

### De retour dans notre fichier build.xml

Il est possible de définir un conteneur de classpath que l'ont pourra utiliser plus tard. Le classpath renseigne les targets du fichier build.xml sur les chemins ou on pourrait retrouver les classes necessaires.

```
...
    <path id="build.classpath">
        <fileset dir="${lib.dir}">
            <include name="**/*.jar" />
        </fileset>
    </path>
...
    <!-- Compilation du code java -->
    <target name="compile" depends="clean, makedir">
        <javac srcdir="${src.dir}" destdir="${build.dir}" classpathref="build.classpath" />
    </target>
```

Il est possible d'imprimer le classpath dans la console comme suit:

```
<!-- Créer une ligne par élément du classpath-->
<pathconvert pathsep="${line.separator}" property="echo.classpath" refid="junit.class.path">
</pathconvert>
<!-- Afficher le résultat dans la console -->
<echo message="The following classpath is associated with junit.class.path " />
<echo message="${echo.classpath}" />
```

### Exécuter des tests unitaires

Sachez qu'il existe la directive junit qui permet de gérer cela, voici un exemple d'usage:

```
!-- Define the classpath which includes the junit.jar and the classes after compiling-->
    <path id="junit.class.path">
        <pathelement location="lib/junit.jar" />
        <pathelement location="${build.dir}" />
    </path>
...
    <!-- Run the JUnit Tests -->
    <!-- Output is XML, could also be plain-->
    <target name="junit" depends="compile">
        <junit printsummary="on" fork="true" haltonfailure="yes">
            <classpath refid="junit.class.path" />
            <formatter type="xml" />
            <batchtest todir="${test.report.dir}">
                <fileset dir="${src.dir}">
                    <include name="**/*Test*.java" />
                </fileset>
            </batchtest>
        </junit>
    </target>
```

Dans notre cas nous aurons besoin d'enlever les fichiers de test de la compilation, nous les mettrons dans un autre task avec un classpath qui pointe vers junit
Nous téléchargerons également ce fichier [http://central.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.3.1/junit-platform-console-standalone-1.3.1.jar](http://central.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.3.1/junit-platform-console-standalone-1.3.1.jar) que nous mettrons dans un répertoire lib

ce qui donnera pour la compilation

```
    <target name="compile" depends="clean, makedir">
        <javac 
        	srcdir="${src.dir}" 
        	destdir="${build.dir}"
            includes="**/*.java" 
        	excludes="**/*Test.java"
        	debug="on" deprecation="on">
        </javac>

    </target>
```

Pour les nouveaux repertoires (et variables qui vont avec)

```
    <property name="src.dir" location="src" />
    <property name="build.dir" location="bin" />
    <property name="build.rep" location="build" />
    <property name="dist.dir" location="dest" />
    <property name="docs.dir" location="docs" />
    <property name="build.test" location="build/test" />
    <property name="build.testreport" location="build/test-report" />

    <!-- On créer les répetoire pour les builds-->
    <target name="makedir">
        <mkdir dir="${build.dir}" />
        <mkdir dir="${docs.dir}" />
        <mkdir dir="${dist.dir}" />
        <mkdir dir="${build.rep}" />
        <mkdir dir="${build.test}" />
        <mkdir dir="${build.testreport}" />
    </target>
```

Notre classpath (après téléchargement du fichier junit-platform-console-standalone-1.3.1.jar dans le repertoire lib):

```
    <path id="test.classpath">
        <pathelement path="build/test"/>
        <pathelement path="build/main"/>
        <fileset dir="lib" includes="junit-platform-console-standalone-1.3.1.jar" />
	</path>
```

La compilation de nos tests

```
    <target name="compile.test" depends="makedir">
        <!--<javac destdir="build/main" srcdir="src/main/java" includeantruntime="false"/>-->
        <javac 
        	destdir="${build.test}" 
        	classpathref="test.classpath" 
        	srcdir="${src.dir}" 
        	includes="**/*Test.java" 
        	debug="on" 
        	deprecation="on"/>
	</target>
```

Et enfin le lancement des test

```
    <target name="test.junit.launcher" depends="compile.test">
        <junitlauncher haltOnFailure="true" printSummary="true">
            <classpath refid="test.classpath"/>
            <testclasses outputdir="${build.testreport}">
                <fileset dir="${build.test}">
                    <include name="**/*Tests.class"/>
                </fileset>
                <listener type="legacy-xml" sendSysOut="true" sendSysErr="true"/>
                <listener type="legacy-plain" sendSysOut="true" />
            </testclasses>
        </junitlauncher>
    </target>
```

Plus d'info [ici]( https://junit.org/junit5/docs/snapshot/user-guide/#running-tests-build-ant)
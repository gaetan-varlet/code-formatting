# Formatage du code

- Pourquoi formater son code : pour pouvoir **comparer les changements** effectués dans le code
- Si on change les règles de formatage au cours de route, les changements importants seront noyés par les changements d’indentations et autres retours à la ligne
- Il existe plusieurs façons de formater son code
	- à la main
	- formatage automatique via l'IDE
		- possibilité de personnaliser les règles de formatage
		- possibilité d'activer le formatage automatique à la sauvegarde
		- inconvénient : demande à ce que tous les développeurs sur un projet configure leur IDE de la même manière (avec parfois des IDE différents)
	- utilisation d'outil de vérification du respect des normes de formatage, et applications de celles-ci

## Java

Utilisation du formateur de code source d'Eclipse pour partager les règles de formatage
- possibilité d'importer et d'exporter les règles pour les partager. Exemples :
	- en XML : https://github.com/google/styleguide/blob/gh-pages/eclipse-java-google-style.xml
	- en properties :
	```properties
	org.eclipse.jdt.core.formatter.tabulation.char=tab
	org.eclipse.jdt.core.formatter.tabulation.size=4
	```
	- exemple pour la gestion des lignes d'imports : https://gist.github.com/kaibeedevadmin/4da9d01e4ad9e71a441d47c25d416157
- possibilité d'importer cette configuration dans IntelliJ via le plugin **Eclipse Code Formatter**

### Utilisation de Spotless

- plugin de formatage de code
	- permet de vérifier que notre code respecte le formatage du projet
	- permet d'appliquer le formatage du projet
	- permet de faire échouer la compilation du code source si le formatage n'est pas respecté
- documentation : https://github.com/diffplug/spotless/tree/main/plugin-maven
- 2 commandes à connaître :
	- `mvn spotless:check` : vérifie si le style est bien appliqué
	- `mvn spotless:apply` : applique le style défini par le plugin

### Mise en place

1. Ajout du plugin dans le POM

```xml
<plugin>
	<groupId>com.diffplug.spotless</groupId>
	<artifactId>spotless-maven-plugin</artifactId>
	<version>2.25.0</version>
</plugin>
```

2. Ajout d'une balise `configuration`

#### Définition de formats pour différents types de fichiers

```xml
<configuration>
	<formats>
		<!-- you can define as many formats as you want, each is independent -->
		<format>
			<!-- define the files to apply to -->
			<includes>
				<include>*.md</include>
				<include>.gitignore</include>
			</includes>
			<!-- define the steps to apply to those files -->
			<trimTrailingWhitespace />
			<endWithNewline />
			<indent>
				<tabs>true</tabs>
				<spacesPerTab>4</spacesPerTab>
			</indent>
		</format>
	</formats>
</configuration>
```

#### Utiliser un style existant, comme **Google Java Format**

```xml
<configuration>
	<!-- define a language-specific format -->
	<java>
		<!-- no need to specify files, inferred automatically, but you can if you want -->
		<!-- apply a specific flavor of google-java-format and reflow long strings -->
		<googleJavaFormat>
			<version>1.15.0</version>
			<style>AOSP</style>
			<reflowLongStrings>true</reflowLongStrings>
		</googleJavaFormat>
	</java>
</configuration>
```

#### Utiliser ses propres règles de formattage définies dans un fichier de configuration

- par exemple `https://gist.github.com/fbricon/30c5971f7e492c8a74ca2b2d7a7bb966`

```xml
<configuration>
	<java>
		<eclipse>
			<file>${project.basedir}/.settings/org.eclipse.jdt.core.prefs</file>
		</eclipse>
	</java>
</configuration>
```

#### Formatage du POM

```xml
<configuration>
	<pom>
		<includes>
			<include>pom.xml</include>
		</includes>
		<sortPom>
			<expandEmptyElements>false</expandEmptyElements>
			<spaceBeforeCloseEmptyElement>true</spaceBeforeCloseEmptyElement>
			<nrOfIndentSpace>-1</nrOfIndentSpace>
		</sortPom>
	</pom>
</configuration>
```

#### Gérer le formatage des imports

```xml
<importOrder /> <!-- standard import order -->
```

```xml
<java>
	<importOrder>
		<wildcardsLast>false</wildcardsLast>
		<order>\#fr.insee,\#,java,javax,org,com,fr,fr.insee,</order>
		<!-- or use <file>${project.basedir}/eclipse.importorder</file> -->
		<!-- you can use an empty string for all the imports you didn't specify explicitly, and '\\#` prefix for static imports. -->
	</importOrder>
</java>
```

#### Autres

Plein d'autres possibilités, voir la doc : Markdown, SQL, ...

3. Lancement automatique du `check` dans le `mvn compile`

Cela permet de faire échouer la compilation et donc la construction du livrable si on a oublié d'appliquer le formattage

```xml
<executions>
	<execution>
		<phase>compile</phase>
		<goals>
			<goal>check</goal>
		</goals>
	</execution>
</executions>
```

**TODO** : analyser le warning suivant
- The build lifecycle phase to bind the goals in this execution to. If omitted, the goals will be bound to the default phase specified by the plugin.
- Enable this execution in project build phase : `<?m2e execute onConfiguration,onIncremental?>`

### Configurer son IDE pour avoir le même style en autosave

Générer le fichier de configuration dans Eclipse
- possibilité de l'utiliser dans **IntelliJ** via le plugin **Eclipse Code Formatter**
- possibilité de l'utiliser dans **VS Code** en mettant le contenu du fichier dans le fichier `.settings/org.eclipse.jdt.core.prefs`

### Automatier le lancement de `spotless:apply` lors des commits

L'execution de la commande `mvn spotless:apply` peut être automatisée à chaque commit en local, en utilisant les hooks de git :

Dans le répertoire du projet, **en local**, aller dans `.git/hooks`
Créer un fichier nommmé "pre-commit" (⚠ sans extension) avec le contenu suivant :

```bash
#!/bin/sh

# Part 1
stagedFiles=$(git diff --staged --name-only)

# Part 2
echo "Running spotlessApply. Formatting code..."
mvn spotless:apply

# Part 3
for file in $stagedFiles; do
if test -f "$file"; then
git add $file
fi
done
```

- A chaque commit ce script sera executé automatiquement, en listant les fichiers ajouté dans l'index (`staged`) et prêt à être commités
- Le formattage est appliqué sur les fichiers (`staged` et `unstaged`) mais seul les fichiers `staged` sont commités

## Javascript

- **Prettier** est un formateur de code, plutôt orienté *front*
- s'occupe uniquement du formatage, pas de la qualité du code (utiliser ES Lint pour cela)
- installation dans le projet : `https://prettier.io/docs/en/install.html`

### Installation de Prettier

- `npm install --save-dev --save-exact prettier`
- création d'un fichier vide à la racine `.prettierrc.json`
 
### Utilisation de Prettier

- `npx prettier --check .` et `npx prettier --write .`
- `npx prettier --check **/*.jsx` et `npx prettier --write **/*.jsx`

### Utilisation dans l'IDE

Formatage automatique à la sauvegarde. Dans VS Code :
- extension VS Code à installer
- configuration  pour que ça fonctionne :
```json
"[javascript]": {
	"editor.defaultFormatter": "esbenp.prettier-vscode",
	"editor.formatOnSave": true
}
```

### Utilisation avec ESLint

- ajout d'une dépendance pour que les 2 fonctionnes bien ensemble : `npm install --save-dev eslint-config-prettier`

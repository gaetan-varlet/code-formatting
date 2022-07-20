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
- possibilité d'importer et d'exporter les règles pour les partager : `eclipse-code-formatter.xml`
- exemples :
	- https://github.com/spring-projects/spring-amqp/blob/main/eclipse-code-formatter.xml
	- https://gist.github.com/kaibeedevadmin/8c80f843fefc3dc3409210eb273a492e
	- https://github.com/google/styleguide/blob/gh-pages/eclipse-java-google-style.xml
	- exemple pour la gestion des lignes d'imports : https://gist.github.com/kaibeedevadmin/4da9d01e4ad9e71a441d47c25d416157
- possibilité d'importer cette configuration dans IntelliJ via le plugin **Eclipse Code Formatter**


### Utilisation de Spotless

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
	<version>2.22.8</version>
</plugin>
```

2. Ajout d'une balise `configuration`

- possibilité d'utiliser un style existant, comme **Google Java Format**
- possibilité d'utiliser ses propres règles de formattage définies dans un fichier de configuration

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


## Javascript

- Prettier




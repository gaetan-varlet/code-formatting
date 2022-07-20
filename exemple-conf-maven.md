# Exemple de configuration pour un projet Maven

```xml
<plugin>
	<groupId>com.diffplug.spotless</groupId>
	<artifactId>spotless-maven-plugin</artifactId>
	<version>2.22.8</version>
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
		<!-- define a language-specific format -->
		<java>
			<eclipse>
				<file>${project.basedir}/.settings/org.eclipse.jdt.core.prefs</file>
			</eclipse>
			<importOrder>
				<wildcardsLast>false</wildcardsLast>
				<order>\#fr.insee,\#,java,javax,org,com,fr,fr.insee,</order>
				<!-- or use <file>${project.basedir}/eclipse.importorder</file> -->
				<!-- you can use an empty string for all the imports you didn't specify explicitly, and '\\#` prefix for static imports. -->
			</importOrder>
		</java>
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
	<executions>
		<execution>
			<goals>
				<goal>check</goal>
			</goals>
			<phase>compile</phase>
		</execution>
	</executions>
</plugin>
```
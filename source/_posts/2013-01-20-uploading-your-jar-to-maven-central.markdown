---
layout: post
title: "Uploading your jar to Maven Central"
date: 2013-01-20 19:23
comments: true
categories: maven,maven central,jar,upload,repository
---

##Public Repo Creation

* Create a Sonatype Repo which can be used for the deployment of the project jars. For this, follow **Step 2** and **3** from [here]( https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide#SonatypeOSSMavenRepositoryUsageGuide-2.Signup).

* After u create the JIRA ticket, the guys responsible will get back to you with all the relevant details including the repositories to which you can deploy

* Now we have a snapshot and release repo ready. So let's ready our project for deployment.

##Changes in your POM file

* Make sure you have all these elements in your POM file

{% codeblock POM elements %}
	<modelVersion> 
	<groupId>
	<artifactId>
	<version>
	<packaging>
	<name>
	<description>
	<url>
	<licenses>
	<scm><url>
	<scm><connection>
	<developers>

{% endcodeblock %}

* Make sure that you append **-SNAPSHOT** to ````<version>````. Don't worry, when your project jar is being deployed to the release repo, the "-SNAPSHOT" string will be removed.

* Add the following xml snippets in your POM

{% codeblock Add these under <build> %}

<plugins>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-compiler-plugin</artifactId>
		<version>2.3.2</version>
		<configuration>
			<source>${java.version}</source>
			<target>${java.version}</target>
		</configuration>
	</plugin>
	<plugin>
		<artifactId>maven-assembly-plugin</artifactId>
		<version>2.3</version>
		<configuration>
			<descriptorRefs>
				<descriptorRef>jar-with-dependencies</descriptorRef>
			</descriptorRefs>
		</configuration>
		<executions>
			<execution>
				<phase>package</phase>
				<goals>
					<goal>single</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-source-plugin</artifactId>
		<version>2.1.2</version>
		<executions>
			<execution>
				<id>attach-sources</id>
				<goals>
					<goal>jar</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-javadoc-plugin</artifactId>
		<configuration>
			<quiet>true</quiet>
			<nonavbar>true</nonavbar>
			<notree>true</notree>
			<nocomment>true</nocomment>
			<nohelp>true</nohelp>

		</configuration>
		<executions>
			<execution>
				<id>attach-javadocs</id>
				<goals>
					<goal>jar</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-gpg-plugin</artifactId>
		<executions>
			<execution>
				<id>sign-artifacts</id>
				<phase>verify</phase>
				<goals>
					<goal>sign</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
</plugins>

{% endcodeblock %}

* Add the distribution info like this:

{% codeblock  %} 

<distributionManagement>
	<!-- Repository for snapshots -->
	<repository>
		<id>sonatype-nexus-snapshots</id>
		<url>https://oss.sonatype.org/content/repositories/snapshots</url>
	</repository>
	<!-- Repository for releases -->
	<repository>
		<id>nexus-releases</id>
		<name>Nexus Release Repository</name>
		<url>http://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
	</repository>
</distributionManagement>

{% endcodeblock %}

* Add the Sonatype parent info:

{% codeblock  %} 

<parent>
	<groupId>org.sonatype.oss</groupId>
	<artifactId>oss-parent</artifactId>
	<version>7</version>
</parent>

{% endcodeblock %}

* Create a file ````settings.xml```` inside your local Maven repository folder (which is usually ````~/.m2/```` ), and add the following xml snippet

{% codeblock  %} 

<settings>
	<servers>
		<server>
			<id>sonatype-nexus-snapshots</id>
			<username>your username</username>
			<password>your password</password>
		</server>
		<server>
			<id>nexus-releases</id>
			<username>your username</username>
			<password>your password</password>
		</server>
	</servers>
	<profiles>
		<profile>
			<id>gpg</id>
			<properties>
				<gpg.passphrase>your passphrase</gpg.passphrase>
				<gpg.keyname>your pgp key</gpg.keyname>
			</properties>
		</profile>
	</profiles>
</settings>

{% endcodeblock %}

The username and password refers to the one you used for creating the jira account earlier.

## Using Public key to sign artifacts

* You can sign your project jars using pgp signatures by following the instructions [here](https://docs.sonatype.org/display/Repository/How+To+Generate+PGP+Signatures+With+Maven) 

* Make sure to upload your key to the public keyserver as mentioned in the link above.

* The ````maven-gpg-plugin```` plugin above will help to sign all your project jars with a pgp signature.

## Upload to Repository

* Before you proceed, make sure to push all the changes made locally to your SCM. **This is a very important step**.

* Create a [public SSH key in github](https://help.github.com/articles/generating-ssh-keys), if you don't have one. If not created, you will face the following error while uploading

{% codeblock %} 

 Permission denied (publickey). error.

{% endcodeblock %}


* To upload your project to the snapshot repository simply run

{% codeblock %} 

 mvn clean deploy

{% endcodeblock %}

* To upload your project for staging before the actual release, run the commands below

{% codeblock %} 

//Cleanup before releasing 
 mvn release:clean

//Preparing for the release
 mvn release:prepare

{% endcodeblock %}

* If your project was prepared successfully, use the following command to upload the jars to the staging repository.

{% codeblock %} 

 mvn release:perform

{% endcodeblock %}

If everything ran well, you have now successfully deployed your project artifacts to the sonatype staging repository.


## Promote staged project to Maven repo

* Login to [Sonatype Repository browser](https://oss.sonatype.org/)
* Follow the instructions given [here](https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide#SonatypeOSSMavenRepositoryUsageGuide-8a.ReleaseIt)
* Before you release your jars into Maven Central, make sure to download and test them locally. 
*  **Do not release your artifacts without ensuring that they work as expected**


Once your staged release has been promoted and synced to the Central Repo, do not forget to close the JIRA ticket you created in the beginning.


That's it. You're done ! Go and chillout :)










# parent-pom
Parent pom.xml for all dropwizard-bundles projects to share.

## Release process setup

#### 1. Maven `settings.xml`
Make sure that you have a valid maven `settings.xml` file in your `$HOME/.m2` directory.  At a
minimum this `settings.xml` needs to have a server definition with id `oss.sonatype.org` in it
along with your username and password for [OSS Sonatype](https://oss.sonatype.org).  This is needed
to allow you to push artifacts to Sonatype upon releasing.

NOTE: Your [OSS Sonatype](https://oss.sonatype.org) account must be associated with the
`io.dropwizard.bundles` group.  Please ask @bbeck or @nbauernfeind for help in getting that setup
-- one of them will have to make a request on your behalf to get your account added to the group.

NOTE: It's heavily advised that you take advantage of maven password encryption described in the
[maven documentation](https://maven.apache.org/guides/mini/guide-encryption.html).  This will allow
you to not store cleartext passwords locally on your machine.

Here is an example `settings.xml` with the `oss.sonatype.org` server defined:
```xml
<settings xmlns="http://maven.apache.org/Settings/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/Settings/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>oss.sonatype.org</id>
      <username>... your username here ...</username>
      <password>{... my encrypted password here ...}</password>
    </server>
  </servers>
</settings>
```

#### 2. GPG key
In order to push artifacts to [OSS Sonatype](https://oss.sonatype.org) and onto Maven Central the
artifacts need to be GPG signed.  This is to ensure that the artifacts that are published can be
verified to be from the project developers.  Follow the 
[instructions](http://central.sonatype.org/pages/working-with-pgp-signatures.html) on the Sonatype
site describing how to setup GPG for signing on your machine.

NOTE: Depending on the version of GPG and maven you use, it might be easier for you to include your
GPG phassphrase in your maven `settings.xml` file.

Here is an example `settings.xml` that includes an encrypted GPG key in it:
```xml
<settings xmlns="http://maven.apache.org/Settings/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/Settings/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <profiles>
    <profile>
      <id>release-profile</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <gpg.keyname>... your GPG key id here ...</gpg.keyname>
      </properties>
    </profile>
  </profiles>
  <servers>
    <server>
      <id>oss.sonatype.org</id>
      <username>... your username here ...</username>
      <password>{... your encrypted password here ...}</password>
    </server>
    <server>
      <id>gpg.passphrase</id>
      <passphrase>{... your encrypted passphrase here ...}</passphrase>
    </server>
  </servers>
</settings>
```

## Releasing

Now that you have maven setup and able to connect to `oss.sonatype.org` you're ready to create a
release build and push it to [OSS Sonatype](https://oss.sonatype.org) automatically.

For releasing we use the standard `maven-release-plugin`.

```sh
mvn release:clean release:prepare release:perform
```

Maven will ask you for several pieces of information as part of this process.  In general the
defaults are mostly correct, but make sure you read the prompts and change the version numbers
where necessary.

```
What is the release version for "dropwizard-version-bundle"? (io.dropwizard-bundles:dropwizard-version-bundle) 1.0.5: :
What is SCM release tag or label for "dropwizard-version-bundle"? (io.dropwizard-bundles:dropwizard-version-bundle) v1.0.5: :
What is the new development version for "dropwizard-version-bundle"? (io.dropwizard-bundles:dropwizard-version-bundle) 1.0.6-SNAPSHOT: :
```
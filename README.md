# ShieldDB

![Build](https://github.com/kuroidoruido/ShieldDB/workflows/Build/badge.svg)
    
ShieldDB is a simple embedded database with JSON file backend with optional in-memory read cache.

## Features

- Easy to use : use ShieldDB like you use a standard Java List
- Easy to extends
- No mandatory external dependencies
- No reflection API usage (except for some json mapper implementation like Gson)

## Contributing to ShielDB

If you want to contribute to ShieldDB or if you want better know how ShieldDB is working, you can check the [DEVELOPER.md](DEVELOPER.md).

## How to use it?

### Add the core dependency

```xml
...
<dependencies>
	...
	<dependency>
		<groupId>nf.fr.k49</groupId>
		<artifactId>shielddb-core</artifactId>
		<version>0.1.1</version>
	</dependency>
	...
</dependencies>
...
```

### Add as dependency a mapper

```xml
...
<dependencies>
	...
	<dependency>
		<groupId>nf.fr.k49</groupId>
		<artifactId>shielddb-gson-backend</artifactId>
		<version>0.1.1</version>
	</dependency>
	...
</dependencies>
...
```

Note: At this time, there is only a Gson mapper implementation. But you can make yours and maybe make a Pull Request :-)

### Initialize a ShieldDB instance

Create a bean class:
```java
public class User {
	private String firstname;
	private String lastname;
	private int age;
	private LocalDate birthDate;
	...
}
```

```java
final List<User> userDb = ShieldDB.<User>builder()
		.mapper(new ShieldDBGson<User>())
		.storage(new FileStorage("User.json"))
		.build();
```

Note: If you want to store a type that has no custom equals implementation, you may have some issues.

### (Optional) Add some shield to customize the way ShieldDB will read/write

```java
final List<User> userDb = ShieldDB.<User>builder()
		.shield(new AtomicShield())// ensure only one thread use userDb
		.shield(new ReadOnlyShield())// throw an UnsupportedOperationException on write access
		//.shield(new SilentReadOnlyShield())// do nothing on write access (no exception)
		.shield(new InMemoryArrayListReadCacheShield())// add an in memory read cache
		.mapper(new ShieldDBGson<User>())
		.storage(new FileStorage("User.json"))
		.build();
```


See more usage example here: https://github.com/kuroidoruido/ShieldDB/tree/master/shielddb-demo-app/

## Release process

- ensure snapshot deploys: mvn clean deploy
- set all packages to a release version: mvn versions:set -DnewVersion=1.0.0
- commit changes
- deploy release: mvn clean deploy
- await new release is visible on maven central: https://repo1.maven.org/maven2/nf/fr/k49/
- set all packages to the next snapshot version: mvn versions:set -DnewVersion=1.0.1-SNAPSHOT
- commit changes
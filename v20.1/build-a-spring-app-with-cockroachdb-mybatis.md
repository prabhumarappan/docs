---
title: Build a Spring App with CockroachDB and MyBatis
summary: Learn how to use CockroachDB from a simple Spring application with MyBatis.
toc: true
twitter: false
---

<div class="filters filters-big clearfix">
    <a href="build-a-java-app-with-cockroachdb.html"><button class="filter-button">Use <strong>JDBC</strong></button></a>
    <a href="build-a-java-app-with-cockroachdb-hibernate.html"><button class="filter-button">Use <strong>Hibernate</strong></button></a>
    <a href="build-a-java-app-with-cockroachdb-jooq.html"><button class="filter-button">Use <strong>jOOQ</strong></button></a>
    <a href="build-a-spring-app-with-cockroachdb-mybatis.html"><button class="filter-button current">Use <strong>MyBatis-Spring</strong></button></a>
</div>

This tutorial shows you how to build a simple [Spring Boot](https://spring.io/projects/spring-boot) application with CockroachDB, using the [MyBatis-Spring-Boot-Starter module](http://mybatis.org/spring-boot-starter) for data access.

## Before you begin

{% include {{page.version.version}}/app/before-you-begin.md %}

## Step 1. Install JDK

Download and install a Java Development Kit. MyBatis-Spring supports Java versions 8+. In this tutorial, we use [JDK 11 from OpenJDK](https://openjdk.java.net/install/).

## Step 2. Install Gradle

This example application uses [Gradle](https://gradle.org/) to manage all application dependencies. Spring supports Gradle versions 6+.

To install Gradle on macOS, run the following command:

{% include copy-clipboard.html %}
~~~ shell
$ brew install gradle
~~~

To install Gradle on a Debian-based Linux distribution like Ubuntu:

{% include copy-clipboard.html %}
~~~ shell
$ apt-get install gradle
~~~

To install Gradle on a Red Hat-based Linux distribution like Fedora:

{% include copy-clipboard.html %}
~~~ shell
$ dnf install gradle
~~~

For other ways to install Gradle, see [its official documentation](https://docs.gradle.org/current/userguide/installation.html).

## Step 3. Get the application code

To get the application code, download or clone the [`mybatis-cockroach-demo` repository](https://github.com/jeffgbutler/mybatis-cockroach-demo).

## Step 4. Create the `maxroach` user and `bank` database

<section class="filter-content" markdown="1" data-scope="secure">

Start the [built-in SQL shell](cockroach-sql.html):

{% include copy-clipboard.html %}
~~~ shell
$ cockroach sql --certs-dir=certs
~~~

In the SQL shell, issue the following statements to create the `maxroach` user and `bank` database:

{% include copy-clipboard.html %}
~~~ sql
> CREATE USER IF NOT EXISTS maxroach;
~~~

{% include copy-clipboard.html %}
~~~ sql
> CREATE DATABASE bank;
~~~

Give the `bank` user the necessary permissions:

{% include copy-clipboard.html %}
~~~ sql
> GRANT ALL ON DATABASE bank TO maxroach;
~~~

Exit the SQL shell:

{% include copy-clipboard.html %}
~~~ sql
> \q
~~~

## Step 5. Generate a certificate for the `maxroach` user

Create a certificate and key for the `maxroach` user by running the following command. The code samples will run as this user.

{% include copy-clipboard.html %}
~~~ shell
$ cockroach cert create-client maxroach --certs-dir=certs --ca-key=my-safe-directory/ca.key --also-generate-pkcs8-key
~~~

The [`--also-generate-pkcs8-key` flag](cockroach-cert.html#flag-pkcs8) generates a key in [PKCS#8 format](https://tools.ietf.org/html/rfc5208), which is the standard key encoding format in Java. In this case, the generated PKCS8 key will be named `client.maxroach.key.pk8`.

## Step 6. Run the application

To run the application:

1. Open and edit the `src/main/resources/application.yml` file so that the `url` field specifies the full [connection string](connection-parameters.html#connect-using-a-url) to the [running CockroachDB cluster](#before-you-begin). To connect to a secure cluster, this connection string must set the `sslmode` connection parameter to `require`, and specify the full path to the client, node, and user certificates in the connection parameters. For example:

      ~~~ yml
      ...
      datasource:
        url: jdbc:postgresql://localhost:26257/bank?ssl=true&sslmode=require&sslrootcert=certs/ca.crt&sslkey=certs/client.maxroach.key.pk8&sslcert=certs/client.maxroach.crt
      ...
      ~~~
1. Open a terminal, and navigate to the `mybatis-cockroach-demo` project directory:

    {% include copy-clipboard.html %}
    ~~~ shell
    $ cd <path>/mybatis-cockroach-demo
    ~~~

1. Run the Gradle script to download the application dependencies, compile the code, and run the application:

    {% include copy-clipboard.html %}
    ~~~ shell
    $ ./gradlew bootRun
    ~~~

</section>

<section class="filter-content" markdown="1" data-scope="insecure">

Start the [built-in SQL shell](cockroach-sql.html):

{% include copy-clipboard.html %}
~~~ shell
$ cockroach sql --insecure
~~~

In the SQL shell, issue the following statements to create the `maxroach` user and `bank` database:

{% include copy-clipboard.html %}
~~~ sql
> CREATE USER IF NOT EXISTS maxroach;
~~~

{% include copy-clipboard.html %}
~~~ sql
> CREATE DATABASE bank;
~~~

Give the `bank` user the necessary permissions:

{% include copy-clipboard.html %}
~~~ sql
> GRANT ALL ON DATABASE bank TO maxroach;
~~~

Exit the SQL shell:

{% include copy-clipboard.html %}
~~~ sql
> \q
~~~

## Step 6. Run the application

To run the application:

1. Open and edit the `src/main/resources/application.yml` file so that the `url` field specifies the full [connection string](connection-parameters.html#connect-using-a-url) to the [running CockroachDB cluster](#before-you-begin). For example:

      ~~~ yaml
      ...
      datasource:
        url: jdbc:postgresql://localhost:26257/bank?ssl=true&sslmode=disable
      ...
      ~~~
1. Open a terminal, and navigate to the `mybatis-cockroach-demo` project directory:

    {% include copy-clipboard.html %}
    ~~~ shell
    $ cd <path>/mybatis-cockroach-demo
    ~~~

1. Run the Gradle script to download the application dependencies, compile the code, and run the application:

    {% include copy-clipboard.html %}
    ~~~ shell
    $ ./gradlew bootRun
    ~~~

</section>

The output should look like the following:

~~~
> Task :bootRun

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.2.6.RELEASE)

2020-06-01 14:40:04.333  INFO 55970 --- [           main] c.e.c.CockroachDemoApplication           : Starting CockroachDemoApplication on MyComputer with PID 55970 (path/mybatis-cockroach-demo/build/classes/java/main started by user in path/mybatis-cockroach-demo)
2020-06-01 14:40:04.335  INFO 55970 --- [           main] c.e.c.CockroachDemoApplication           : No active profile set, falling back to default profiles: default
2020-06-01 14:40:05.195  INFO 55970 --- [           main] c.e.c.CockroachDemoApplication           : Started CockroachDemoApplication in 1.39 seconds (JVM running for 1.792)
2020-06-01 14:40:05.216  INFO 55970 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2020-06-01 14:40:05.611  INFO 55970 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
deleteAllAccounts:
    => 2 total deleted accounts
insertAccounts:
    => 2 total new accounts in 1 batches
printNumberOfAccounts:
    => Number of accounts at time '14:40:05.660226':
    => 2 total accounts
printBalances:
    => Account balances at time '14:40:05.678942':
    ID 1 => $1000
    ID 2 => $250
transferFunds:
    => $100 transferred between accounts 1 and 2, 2 rows updated
printBalances:
    => Account balances at time '14:40:05.688511':
    ID 1 => $900
    ID 2 => $350
bulkInsertRandomAccountData:
    => finished, 500 total rows inserted in 4 batches
printNumberOfAccounts:
    => Number of accounts at time '14:40:05.960214':
    => 502 total accounts
2020-06-01 14:40:05.968  INFO 55970 --- [extShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2020-06-01 14:40:05.993  INFO 55970 --- [extShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.

BUILD SUCCESSFUL in 12s
3 actionable tasks: 3 executed
~~~

As the output states, the application runs a number of test functions that result in transactional reads and writes on the running CockroachDB cluster. For more details about the application code, see [Application details](#application-details).

## Application details

This section walks you through the different components of the application project in detail.

### Main process

The main process of the application is defined in `src/main/java/com/example/cockroachdemo/CockroachDemoApplication.java`:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/CockroachDemoApplication.java %}
~~~

The `SpringApplication.run` call in the `main` method bootstraps and launches a Spring application using the configuration properties of the `CockroachDemoApplication` configuration class. These properties are set with the [`@SpringBootApplication` annotation](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-using-springbootapplication-annotation), which triggers Spring's [component scanning](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-structuring-your-code) and [auto-configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-auto-configuration) features.

The `BasicExample` class, defined in `src/main/java/com/example/cockroachdemo/BasicExample.java`, is one of the components detected in the component scan:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/BasicExample.java %}
~~~

This class implements the `CommandLineRunner` interface. Implementations of this interface automatically run when detected in a Spring project directory. The `BasicExample` class runs a series of test methods that are eventually executed as SQL queries by the [data access layer of the application](#mappers).

### Configuration

All MyBatis-Spring applications need a [`DataSource`](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-configure-datasource), a [`SqlSessionFactory`](https://mybatis.org/spring/factorybean.html), and at least one [mapper interface](https://mybatis.org/spring/mappers.html). The [MyBatis-Spring-Boot-Starter](hhttps://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure) module, built on [MyBatis](https://mybatis.org/mybatis-3/) and [MyBatis-Spring](https://mybatis.org/spring/), greatly simplifies how you configure each of these required elements.

Applications that use MyBatis-Spring-Boot-Starter typically need just an annotated mapper interface and an existing `DataSource` in the Spring environment. The module autodetects the `DataSource`, creates a `SqlSessionFactory` from the `DataSource`, creates a thread-safe [`SqlSessionTemplate`](https://mybatis.org/spring/sqlsession.html#SqlSessionTemplate) with the `SqlSessionFactory`, and then auto-scans the mappers and links them to the `SqlSessionTemplate` for injection. The `SqlSessionTemplate` automatically commits, rolls back, and closes sessions, based on the application's [Spring transaction configuration](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction).

This sample application implements [batch write operations](insert.html#performance-best-practices), a CockroachDB best practice for executing multiple `INSERT` and `UPSERT` statements at a time. MyBatis applications that support batch operations must have a mapper interface for the batch query methods and a `SqlSessionTemplate` constructor that specifies the [`BATCH` executor type](https://mybatis.org/mybatis-3/apidocs/reference/org/apache/ibatis/executor/BatchExecutor.html). The configuration class defined in `src/main/java/com/example/cockroachdemo/MyBatisConfiguration.java` registers the `batchmapper` batch operations mapper defined in [`src/main/java/com/example/cockroachdemo/batchmapper/BatchMapper.java`](#mappers). The class also defines the batch-specific  `batchSqlSessionTemplate` constructor. To complete the MyBatis configuration, the class declares a `DataSource`, and explicitly defines the remaining `SqlSessionFactory` and `SqlSessionTemplate` beans:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/MyBatisConfiguration.java %}
~~~

Note that this file is not required for MyBatis-Spring-Boot-Starter applications that do not implement batch operations.

### Data source

`src/main/resources/application.yml` contains the metadata used to create a connection to the CockroachDB cluster:

{% include copy-clipboard.html %}
~~~ yaml
{% include {{page.version.version}}/app/spring-mybatis/application.yml %}
~~~

Spring Boot [auto-configures database connections with the `datasource`](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-configure-datasource) property that is defined in the `application.yml` file. This database connection configuration is injected into the `SqlSessionFactoryBean` through the autowired `dataSource` defined in [MyBatisConfiguration.java](#configuration).

### Mappers

All MyBatis applications require at least one mapper interface. These mappers take the place of manually-defined data access objects (DAOs). They provide other layers of the application an interface to the database.

`MyBatis-Spring-Boot-Starter` usually scans the project for interfaces annotated with `@Mapper`, links the interfaces to a `SqlSessionTemplate`, and registers them with Spring so they can be [injected into the application's Spring beans](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection). As mentioned in the [Configuration section](#configuration), because the application supports batch writes, the two mapper interfaces in the application are registered and linked manually in the `MyBatisConfiguration.java` configuration file.

The `src/main/java/com/example/cockroachdemo/mapper` and `src/main/java/com/example/cockroachdemo/batchmapper` folders contain the files that define the application's mapper interfaces with the [MyBatis Java API](https://mybatis.org/mybatis-3/java-api.html):

- `AccountMapper.java` defines the mapper interface to the `accounts` table:

  {% include copy-clipboard.html %}
  ~~~ java
  {% include {{page.version.version}}/app/spring-mybatis/mapper/AccountMapper.java %}
  ~~~

    The `@Mapper` annotation declares the interface a mapper for MyBatis to scan. The SQL statement annotations on each of the interface methods map them to SQL queries. For example, the first method, `deleteAllAccounts()` is marked as a `DELETE` statement with the `@Delete` annotation. This method executes the SQL statement specified in the string passed to the annotation (`delete from accounts`), which deletes all rows in the `accounts` table.

- `BatchAccountMapper.java` defines a mapper interface for [batch writes](https://www.cockroachlabs.com/docs/stable/insert.html#performance-best-practices):

  {% include copy-clipboard.html %}
  ~~~ java
  {% include {{page.version.version}}/app/spring-mybatis/batchmapper/BatchAccountMapper.java %}
  ~~~

    This interface has a single `INSERT` statement query method, along with a method for flushing (i.e., executing) a batch of statements.

### Services

The `src/main/java/com/example/cockroachdemo/service` folder contains the files that define the primary application service:

- `AccountService.java` defines the service interface, with a number of methods for reading and writing to the database:

  {% include copy-clipboard.html %}
  ~~~ java
  {% include {{page.version.version}}/app/spring-mybatis/service/AccountService.java %}
  ~~~

- `MyBatisAccountService.java` implements the `AccountService` interface, using the mappers defined in [`AccountMapper.java` and `BatchAccountMapper.java`](#mappers) and the models defined in [`Account.java` and `BatchResults.java`](#models):

  {% include copy-clipboard.html %}
  ~~~ java
  {% include {{page.version.version}}/app/spring-mybatis/service/MyBatisAccountService.java %}
  ~~~

    Note that the public methods (i.e., the methods to be called by other classes in the project) are annotated as [`@Transactional`](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction-declarative-annotations) methods. This ensures that all of the SQL statements executed in the data access layer are run within the context of a single [database transaction](transactions.html). This is especially important for the methods that execute batch writes to the database, as the multiple statements in a batch operation are meant to be committed as a part of the same transaction. For more details on transaction management in this application, [see below](#transaction-management).

### Models

Instances of the `Account` class, defined in `src/main/java/com/example/cockroachdemo/model/Account.java`, represent rows in the `accounts` table:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/model/Account.java %}
~~~

Instances of the `BatchResults` class, defined in `src/main/java/com/example/cockroachdemo/model/BatchResults.java`, hold metadata about a batch write operation and its results:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/model/BatchResults.java %}
~~~

### Transaction management

MyBatis-Spring supports Spring's [declarative, aspect-oriented transaction management syntax](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction-declarative), including the [`@Transactional`](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction-declarative-annotations) annotation and [AspectJ's AOP annotations](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction-declarative-aspectj). For information about MyBatis-Spring and transactions, see [the MyBatis documentation site](https://mybatis.org/spring/transactions.html).

As a best practice, we recommend writing [client-side transaction retry logic](transactions.html#client-side-intervention) in all applications built on CockroachDB. Transaction retries help [avoid transaction contention](performance-best-practices-overview.html#understanding-and-avoiding-transaction-contention), which can limit performance and lead to issues with data integrity. In this application, transaction retry logic is written into the [advice](https://en.wikipedia.org/wiki/Advice_(programming)) methods of the `RetryableTransactionAspect` class, defined in `src/main/java/com/example/cockroachdemo/RetryableTransactionAspect.java`:

{% include copy-clipboard.html %}
~~~ java
{% include {{page.version.version}}/app/spring-mybatis/RetryableTransactionAspect.java %}
~~~

This class is declared an [aspect](https://en.wikipedia.org/wiki/Aspect_(computer_programming)) with the [`@Aspect` annotation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-at-aspectj).

`anyTransactionBoundaryOperation` is declared as a [pointcut](https://en.wikipedia.org/wiki/Pointcut) with the [`@Pointcut` annotation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-pointcuts). The `@annotation` [designator](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-pointcuts-designators) passed to the pointcut limits the matching [join points](https://en.wikipedia.org/wiki/Join_point) to method calls with a specific annotation, in this case `@Transactional`.

`retryableOperation` handles the application retry logic, with [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff), in the form of aspect advice. Spring supports [several different annotations to declare advice](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-advice). The [`@Around` annotation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-ataspectj-around-advice) declares `retryableOperation` as the advice to execute at a `anyTransactionBoundaryOperation(transactional)` join point.

Note that the `RetryableTransactionAspect` class is also annotated with `@Order`. When multiple aspect advisors are defined at a single join point, an `@Order` annotation on the aspect can specify the order in which the advice should be evaluated. The `@Order` annotation on this class is passed `Ordered.LOWEST_PRECEDENCE-1`, which places this aspect's advice at a level of precedence before [the primary transaction advisor](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#tx-decl-explained), which is given the lowest level of precedence by default. For details about advice ordering in Spring, see [Advice Ordering](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-ataspectj-advice-ordering) on the Spring documentation site.

## See also

Spring documentation:

- [Spring Boot website](https://spring.io/projects/spring-boot)
- [Spring Framework Overview](https://docs.spring.io/spring/docs/current/spring-framework-reference/overview.html#overview)
- [Spring Core documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#spring-core)
- [MyBatis documentation](https://mybatis.org/mybatis-3/)
- [MyBatis Spring integration](https://mybatis.org/spring/)

CockroachDB documentation:

- [Learn CockroachDB SQL](learn-cockroachdb-sql.html)
- [Client Connection Parameters](connection-parameters.html)
- [CockroachDB Developer Guide](developer-guide-overview.html)
- [Hello World Example Apps](hello-world-example-apps.html)
- [Transactions](transactions.html)

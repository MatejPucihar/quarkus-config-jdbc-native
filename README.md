# showcase native image build failure
- tests were performed on mac M1 and linux amd64 processors
- run `./mvnw clean  package -Dnative -Dquarkus.native.container-build=true -DskipTests` 
- inspect application.yml - errors differ based on hardcoded `quarkus.config.jdbc.url` and friends properties!
- if there is no hardcoded url
  - ```
    Caused by: io.smallrye.config.ConfigValidationException: Configuration validation failed:
    java.util.NoSuchElementException: SRCFG00011: Could not expand value quarkus.datasource.jdbc.url in property quarkus.config.source.jdbc.url
    java.util.NoSuchElementException: SRCFG00011: Could not expand value quarkus.datasource.username in property quarkus.config.source.jdbc.username
    java.util.NoSuchElementException: SRCFG00011: Could not expand value quarkus.datasource.password in property quarkus.config.source.jdbc.password
    ```
- if there is hardcoded url
  - ```
    Caused by: java.lang.RuntimeException: Unable to get java.sql.Driver from DriverManager
    at io.agroal.pool.ConnectionFactory.newDriver(ConnectionFactory.java:130)
    at io.agroal.pool.ConnectionFactory.<init>(ConnectionFactory.java:69)
    at io.agroal.pool.ConnectionPool.<init>(ConnectionPool.java:108)
    at io.agroal.pool.DataSource.<init>(DataSource.java:37)
    at io.agroal.pool.DataSourceProvider.getDataSource(DataSourceProvider.java:21)
    at io.agroal.api.AgroalDataSource.from(AgroalDataSource.java:41)
    at io.quarkiverse.config.jdbc.runtime.Repository.prepareDataSource(Repository.java:107)
    at io.quarkiverse.config.jdbc.runtime.Repository.<init>(Repository.java:32)
    at io.quarkiverse.config.jdbc.runtime.JdbcConfigSourceFactory.getConfigSources(JdbcConfigSourceFactory.java:37)
    at io.smallrye.config.ConfigurableConfigSource.getConfigSources(ConfigurableConfigSource.java:50)
    at io.smallrye.config.SmallRyeConfig$ConfigSources.mapLateSources(SmallRyeConfig.java:845)
    at io.smallrye.config.SmallRyeConfig$ConfigSources.<init>(SmallRyeConfig.java:735)
    at io.smallrye.config.SmallRyeConfig.<init>(SmallRyeConfig.java:81)
    at io.smallrye.config.SmallRyeConfigBuilder.build(SmallRyeConfigBuilder.java:724)
    at io.quarkus.runtime.generated.Config.<clinit>(Unknown Source)
    ... 42 more
    Caused by: java.sql.SQLException: No suitable driver
    at java.sql/java.sql.DriverManager.getDriver(DriverManager.java:300)
    at io.agroal.pool.ConnectionFactory.newDriver(ConnectionFactory.java:128)
    ... 56 more
    ```

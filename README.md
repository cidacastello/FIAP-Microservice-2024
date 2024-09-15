# MICROSERVICE AND WEB ENGINEERING
## FIAP - 2024

Material de apoio disciplina **MICROSERVICE AND WEB ENGINEERING** - FIAP - 2024/02

***

***
### Problema com dependência no Maven
#### pom.xml - Maven
No arquivo `pom.xml` – caso tenha problemas, incluir o plugin conforme código abaixo: 

```xml
<!-- Failed to execute goal org.apache.maven.plugins:maven-resources-plugin:3.2.0:resources
(default-resources) on project application-etude: Input ength = 1 -->

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version> <!--$NO-MVN-MAN-VER$ -->
</plugin>
```

***

### Application properties
#### Ambiente de Teste - DB H2

Path: `\resources\`
File name: `application-test.properties`


```properties

# para expor o trace - somente para testes
# server.error.include-stacktrace = always
server.error.include-message=always

# para não expor o trace
server.error.include-stacktrace = never
# server.error.include-message=never

# Conexão com o banco H2
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA, SQL
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# http://localhost:8080/h2-console/

```
***
#### Seed DB para MS-Pagamento

```sql

INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(1200, 'Nicodemus C Souza', '1234567890123456', '12/30', '352', 'CRIADO', 1, 2);
INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(500.50, 'Amadeus Mozart',  '8597452369851245', '05/28', '647', 'CRIADO', 5, 2);
INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(1200, 'Maria Joaquina',    '2457896547123654', '01/25', '543', 'CRIADO', 3, 2);

```

***
### Swagger e OpenAPI 3.0
#### Documentação de API e Microsserviços com Swagger e OpenAPI 3.0

##### Dependência Maven no pom.xml

[https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-starter-webmvc-ui](https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-starter-webmvc-ui)

```xml
<!--Swagger-->
<dependency>
	<groupId>org.springdoc</groupId>
	<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
	<version>2.6.0</version>
</dependency>
```

Iniciar a aplicação e acessar no navegador:

Documentação em uma página web para navegar e fazer testes, parecido com Postman, Insomnia.
[http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html)

Gerando um JSON com a documentação, então podemos usar ferramentas que automatizam a criação do cliente dessa API com base nesse JSON.
[http://localhost:8080/v3/api-docs](http://localhost:8080/v3/api-docs)


***

### API ViaCEP

ViaCEP: https://viacep.com.br/

Para realizar a serialização/desserialização podemos utilizar a biblioteca ***Jackson*** que transforma objetos Java em JSON (serialização) e vice-versa (desserialização).

##### Dependência Maven no pom.xml 

https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind/2.17.2

```xml

<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.17.2</version>
</dependency>

```

***
### ModelMapper
#### Dependência no pom.xml

Adicionar a dependêndia da ***library*** no `pom.xml`

https://mvnrepository.com/artifact/org.modelmapper/modelmapper


```xml
<!-- https://mvnrepository.com/artifact/org.modelmapper/modelmapper -->
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>3.2.1</version>
</dependency>

```
***

### Comunicação entre Microsserviços
#### Eureka Discovery Client - Dependencies
##### MS-Pagamentos

Adicionar a dependência no `pom.xml` do MS-Pagamentos

```xml
<!--    código omitido-->
 <properties>
    <java.version>17</java.version>
    <spring-cloud.version>2023.0.3</spring-cloud.version>
  </properties>
  <dependencies>

    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>

  <!--    código omitido-->

  </dependencies>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>${spring-cloud.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <!--    código omitido-->

```

Configurações do application.properties

```properties

spring.application.name=ms-pagamento

spring.profiles.active=test
spring.jpa.open-in-view=false

# É preciso passar a configuração eureka.client.serviceUrl.defaultZone
# e o localhost 8081, onde o Eureka Server está recebendo as requisições
eureka.client.serviceUrl.defaultZone=http://localhost:8081/eureka

#define a porta para o Eureka controlar em qual porta ele vai subir o serviço
server.port=0
```
##### MS-Pedidos

Adicionar a dependência no `pom.xml` do MS-Pedidos

```xml
<!--    código omitido-->
 <properties>
    <java.version>17</java.version>
    <spring-cloud.version>2023.0.3</spring-cloud.version>
  </properties>
  <dependencies>

    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>

  <!--    código omitido-->

  </dependencies>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>${spring-cloud.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <!--    código omitido-->

```

Configurações do `application.properties`

```properties

spring.application.name=ms-pagamento

spring.profiles.active=test
spring.jpa.open-in-view=false

# É preciso passar a configuração eureka.client.serviceUrl.defaultZone
# e o localhost 8081, onde o Eureka Server está recebendo as requisições
eureka.client.serviceUrl.defaultZone=http://localhost:8081/eureka

#define a porta para o Eureka controlar em qual porta ele vai subir o serviço
server.port=0
```
***

 ### Gateway

 Acrescentar em `application.properties`.

```properties
 spring.application.name=gateway

# Porta padrão
server.port=8082

# Endereço do Eureka - igual nos MS's
eureka.client.serviceUrl.defaultZone=http://localhost:8081/eureka

# Processo de descoberta
spring.cloud.gateway.discovery.locator.enabled=true

# Define que o nome das aplicações serão em letras minúsculas
spring.cloud.gateway.discovery.locator.lowerCaseServiceId=true

```
***
### Spring Cloud OpenFeign

Dependência Spring Cloud OpenFeign

Adicionar a dependência no `pom.xml` do MS-Pagamentos.
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
***

### CircuitBreaker e Fallback

#### Dependências

Adicionar as dependências no `pom.xml` do MS-Pagamentos.

Dependência do resilience4j-spring-boot3
https://mvnrepository.com/artifact/io.github.resilience4j/resilience4j-spring-boot3/2.2.0

```xml

<!-- https://mvnrepository.com/artifact/io.github.resilience4j/resilience4j-spring-boot3 -->
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
    <version>2.2.0</version>
</dependency>

```

Dependência do spring-boot-starter-aop.

https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop

```xml

<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
    <version>3.3.3</version>
</dependency>

```
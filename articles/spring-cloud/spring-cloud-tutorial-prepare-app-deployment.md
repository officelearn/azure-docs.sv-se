---
title: How-to - Förbered ett Java Spring-program för distribution i Azure Spring Cloud
description: I det här avsnittet förbereder du ett Java Spring-program för distribution till Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 025dbc56fb46ef5b6225d35564b8e4ac3c82e6e3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414465"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Förbereda ett Java Spring-program för distribution i Azure Spring Cloud

Det här avsnittet visar hur du förbereder ett befintligt Java Spring-program för distribution till Azure Spring Cloud. Om Azure Spring Cloud är korrekt konfigurerat tillhandahåller de robusta tjänster för att övervaka, skala och uppdatera ditt Java Spring Cloud-program.

Andra exempel förklarar hur du distribuerar ett program till Azure Spring Cloud när POM-filen är konfigurerad. 
* [Starta app med Azure-portalen](spring-cloud-quickstart-launch-app-portal.md)
* [Starta app med Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

I den här artikeln beskrivs de beroenden som krävs och hur du lägger till dem i POM-filen.

## <a name="java-runtime-version"></a>Java Runtime-version

Endast spring/java-program kan köras i Azure Spring Cloud.

Azure Spring Cloud stöder både Java 8 och Java 11. Värdmiljön innehåller den senaste versionen av Azul Zulu OpenJDK för Azure. Mer information om Azul Zulu OpenJDK för Azure finns i [Installera JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot- och Spring Cloud-versioner

För att förbereda ett befintligt Spring Boot-program för distribution till Azure Spring Cloud inkluderar fjäderstart- och fjädermolnberoenden i programmet POM-filen som visas i följande avsnitt.

Azure Spring Cloud stöder endast Spring Boot-appar antingen Spring Boot version 2.1 eller version 2.2. I följande tabell visas kombinationerna Spring Boot och Spring Cloud som stöds:

Spring Boot version | Spring Cloud-versionen
---|---
2.1 | Greenwich.RELEASE (PÅ)Greenwich.RELEASE Greenwich.
2.2 | Hoxton.RELEASE (PÅ)Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Beroenden för Spring Boot version 2.1

För Spring Boot version 2.1 lägga till följande beroenden till ansökan POM fil.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Beroenden för Spring Boot version 2.2

För Spring Boot version 2.2 lägga till följande beroenden till ansökan POM fil.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud-klientberoende

Azure Spring Cloud är värd för och hanterar Spring Cloud-komponenter. Komponenterna inkluderar Spring Cloud Service Registry och Spring Cloud Config Server. Inkludera Azure Spring Cloud-klientbiblioteket i dina beroenden för att tillåta kommunikation med din Azure Spring Cloud-tjänstinstans.

I följande tabell visas rätt Azure Spring Cloud-versioner för din app som använder Spring Boot och Spring Cloud.

Spring Boot version | Spring Cloud-versionen | Azure Spring Cloud-version
---|---|---
2.1 | Greenwich.RELEASE (PÅ)Greenwich.RELEASE Greenwich. | 2.1
2.2 | Hoxton.RELEASE (PÅ)Hoxton.RELEASE | 2.2

Inkludera något av följande beroenden i filen pom.xml. Välj det beroende vars Azure Spring Cloud-version matchar din egen.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Beroende för Azure Spring Cloud version 2.1

För Spring Boot version 2.1 lägga till följande beroende till ansökan POM fil.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Beroende för Azure Spring Cloud version 2.2

För Spring Boot version 2.2 lägga till följande beroende till ansökan POM fil.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andra nödvändiga beroenden

För att aktivera de inbyggda funktionerna i Azure Spring Cloud måste ditt program innehålla följande beroenden. Den här inkluderingen säkerställer att programmet konfigurerar sig själv korrekt med varje komponent.

### <a name="enablediscoveryclient-annotation"></a>AktiveraDiscoveryClient-anteckning

Lägg till följande anteckning i programmets källkod.
```java
@EnableDiscoveryClient
```
Se till exempel piggymetrics-programmet från tidigare exempel:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>Beroende av tjänstregister

Om du vill använda den hanterade `spring-cloud-starter-netflix-eureka-client` Azure Service-registertjänsten inkluderar du beroendet i filen pom.xml som visas här:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Slutpunkten för Service Registry-servern injiceras automatiskt som miljövariabler med din app. Program kan registrera sig med serviceregisterservern och upptäcka andra beroende mikrotjänster.

### <a name="distributed-configuration-dependency"></a>Beroende av distribuerad konfiguration

Om du vill aktivera `spring-cloud-config-client` distribuerad konfiguration inkluderar du följande beroende i beroendeavsnittet i filen pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange `spring.cloud.config.enabled=false` inte i din bootstrap konfiguration. Annars slutar programmet att fungera med Config Server.

### <a name="metrics-dependency"></a>Beroende av mått

Inkludera `spring-boot-starter-actuator` beroendet i beroendesektionen i filen pom.xml som visas här:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Mått hämtas regelbundet från JMX-slutpunkterna. Du kan visualisera måtten med hjälp av Azure-portalen.

### <a name="distributed-tracing-dependency"></a>Beroende av distribuerad spårning

Inkludera följande `spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` och beroenden i avsnittet beroenden i filen pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Du måste också aktivera en Azure Application Insights-instans för att fungera med din Azure Spring Cloud-tjänstinstans. Läs [självstudien om distribuerad spårning](spring-cloud-tutorial-distributed-tracing.md) för att lära dig hur du använder Application Insights med Azure Spring Cloud.

## <a name="see-also"></a>Se även
* [Analysera programloggar och mätvärden](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Konfigurera konfigurationsservern](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Använda distribuerad spårning med Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Snabbstartsguide för våren](https://spring.io/quickstart)
* [Dokumentation för Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du konfigurerar javafjäderprogrammet för distribution till Azure Spring Cloud. Mer information om hur du konfigurerar en Config Server-instans finns i följande artikel.

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar en Config Server-instans](spring-cloud-tutorial-config-server.md)

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).

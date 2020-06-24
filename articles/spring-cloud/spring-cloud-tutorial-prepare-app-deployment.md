---
title: Så här förbereder du ett Java våren-program för distribution i Azure våren Cloud
description: I det här avsnittet förbereder du ett Java våren-program för distribution till Azure våren Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 668406bb90e1f1e064adf01d7dbab42923fe30aa
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789284"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Förbereda ett Java våren-program för distribution i Azure våren Cloud

Det här avsnittet visar hur du förbereder ett befintligt Java våren-program för distribution till Azure våren Cloud. Om Azure våren Cloud har kon figurer ATS korrekt tillhandahåller robusta tjänster för att övervaka, skala och uppdatera ditt Java våren Cloud-program.

Andra exempel beskriver hur du distribuerar ett program till Azure våren Cloud när POM-filen har kon figurer ATS. 
* [Starta appen med hjälp av Azure Portal](spring-cloud-quickstart-launch-app-portal.md)
* [Starta app med Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

I den här artikeln beskrivs de nödvändiga beroendena och hur du lägger till dem i POM-filen.

## <a name="java-runtime-version"></a>Java Runtime-version

Endast våren/Java-program kan köras i Azure våren-molnet.

Azure våren Cloud stöder både Java 8 och Java 11. Värd miljön innehåller den senaste versionen av Azul Zulu OpenJDK för Azure. Mer information om Azul Zulu OpenJDK för Azure finns i [installera JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Moln versioner för våren start och våren

För att förbereda ett befintligt våren Boot-program för distribution till Azure våren-molnet ingår våren-start-och våren-moln beroenden i program POM-filen som visas i följande avsnitt.

Azure våren Cloud stöder bara våren Boot-appar, antingen våren Boot version 2,1 eller version 2,2. I följande tabell visas de kombinationer som stöds av våren-start och våren:

Start version för våren | Våren Cloud-version
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton. RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Beroenden för vår start version 2,1

För våren Boot version 2,1 lägger du till följande beroenden i programmets POM-fil.

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

### <a name="dependencies-for-spring-boot-version-22"></a>Beroenden för vår start version 2,2

För våren Boot version 2,2 lägger du till följande beroenden i programmets POM-fil.

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

## <a name="azure-spring-cloud-client-dependency"></a>Klient beroende för Azure våren Cloud

Azure våren-moln är värdar för och hanterar våren Cloud-komponenter. Komponenterna inkluderar vår moln tjänst register och vår moln konfigurations Server. Ta med klient biblioteket Azure våren Cloud i dina beroenden för att tillåta kommunikation med din Azure våren Cloud Service-instans.

I följande tabell visas rätt Azure våren Cloud-versioner för din app som använder våren-start och våren Cloud.

Start version för våren | Våren Cloud-version | Azure våren Cloud-version
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton. RELEASE | 2.2

Inkludera något av följande beroenden i pom.xml-filen. Välj det beroende vars Azure våren Cloud-version matchar din egen.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Beroende av Azure våren Cloud version 2,1

För våren Boot version 2,1 lägger du till följande beroende till programmets POM-fil.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Beroende av Azure våren Cloud version 2,2

För våren Boot version 2,2 lägger du till följande beroende till programmets POM-fil.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Andra rekommenderade beroenden för att aktivera moln funktioner i Azure våren

Om du vill aktivera de inbyggda funktionerna i Azure våren Cloud från tjänst registret till distribuerad spårning måste du även inkludera följande beroenden i ditt program. Du kan ta bort vissa av dessa beroenden om du inte behöver motsvarande funktioner för de olika apparna.

### <a name="service-registry"></a>Tjänst register

Om du vill använda den hanterade tjänsten för Azure-tjänsten ska du inkludera `spring-cloud-starter-netflix-eureka-client` beroendet i pom.xml-filen som visas här:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Slut punkten för tjänst registrerings servern matas automatiskt in som miljövariabler med din app. Program kan registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient-anteckning

Lägg till följande anteckning i program käll koden.
```java
@EnableDiscoveryClient
```
Se till exempel programmet piggymetrics från tidigare exempel:
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

### <a name="distributed-configuration"></a>Distribuerad konfiguration

Om du vill aktivera distribuerad konfiguration inkluderar du följande beroende `spring-cloud-config-client` i avsnittet beroenden i pom.xml-filen:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange inte `spring.cloud.config.enabled=false` i Start konfigurationen. Annars slutar programmet att fungera med konfigurations servern.

### <a name="metrics"></a>Mått

Inkludera `spring-boot-starter-actuator` beroendet i avsnittet beroenden i pom.xml-filen som visas här:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Måtten hämtas regelbundet från JMX-slutpunkterna. Du kan visualisera måtten med hjälp av Azure Portal.

 > [!WARNING]
 > Ange `spring.jmx.enabled=true` i konfigurations egenskapen. Annars kan inte måtten visualiseras i Azure Portal.

### <a name="distributed-tracing"></a>Distribuerad spårning

Inkludera följande `spring-cloud-starter-sleuth` och `spring-cloud-starter-zipkin` beroenden i avsnittet beroenden i pom.xml-filen:

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

 Du måste också aktivera en Azure Application insikts-instans för att arbeta med din Azure våren Cloud Service-instans. Läs [självstudien Om distribuerad spårning](spring-cloud-tutorial-distributed-tracing.md) för att lära dig hur du använder Application Insights med Azure våren Cloud.

## <a name="see-also"></a>Se även
* [Analysera program loggar och mått](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Konfigurera konfigurationsservern](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Använd distribuerad spårning med Azure våren Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Guide för våren-snabb start](https://spring.io/quickstart)
* [Dokumentation om våren Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du konfigurerar ditt Java våren-program för distribution till Azure våren Cloud. Information om hur du konfigurerar en konfigurations Server instans finns i följande artikel.

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar en konfigurations Server instans](spring-cloud-tutorial-config-server.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).

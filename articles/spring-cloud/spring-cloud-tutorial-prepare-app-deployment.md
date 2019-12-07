---
title: Självstudie – förbereda ett våren-program för distribution i Azure våren Cloud
description: I den här självstudien förbereder du ett Java våren-program för distribution.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890322"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Självstudie: förbereda ett Java våren-program för distribution i Azure våren Cloud

Den här snabb starten visar hur du förbereder ett befintligt Java våren Cloud-program för distribution till Azure våren Cloud.  Azure våren Cloud har kon figurer ATS korrekt och ger robusta tjänster för att övervaka, skala och uppdatera ditt våren Cloud-program. 

## <a name="java-runtime-version"></a>Java Runtime-version

Endast våren/Java-program kan köras i Azure våren-molnet.

Både Java 8 och Java 11 stöds. Värd miljön innehåller den senaste Azul Zulu-OpenJDK för Azure. Mer information om Azul Zulu-OpenJDK för Azure hittar du i [den här artikeln](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) . 

## <a name="spring-boot-and-spring-cloud-versions"></a>Moln versioner för våren start och våren

Endast våren Boot-appar stöds i Azure våren-molnet. Både våren boot 2,0 och 2,1 stöds. De kombinationer av fjäder och våren som stöds finns i tabellen nedan.

Start version för våren | Våren Cloud-version
---|---
2.0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

Kontrol lera att din `pom.xml`-fil har moln beroenden våren start och våren baserat på din version.

### <a name="version-20"></a>Version 2,0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Version 2,1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Klient beroende för Azure våren Cloud

Azure våren-moln är värdar för och hanterar fjäder moln komponenter åt dig, till exempel Registry-molnet och vår moln konfigurations Server. Ta med klient biblioteket Azure våren Cloud i dina beroenden för att tillåta kommunikation med din Azure våren Cloud Service-instans.

I tabellen nedan visas rätt versioner för din våren Boot/våren Cloud-App.

Start version för våren | Våren Cloud-version | Azure våren Cloud-version
---|---|---
2.0. x | Finchley. RELEASE | 2.0. x
2.1. x | Greenwich. RELEASE | 2.1. x

Inkludera ett av kodfragmenten nedan i `pom.xml`.  Välj det kodfragment vars version matchar ditt eget.

### <a name="version-20x"></a>Version 2.0. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Version 2.1. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andra nödvändiga beroenden

För att aktivera de inbyggda funktionerna i Azure våren Cloud måste ditt program innehålla följande beroenden. På så sätt ser du till att programmet konfigurerar sig korrekt med varje komponent.  

### <a name="service-registry"></a>Tjänst register

Om du vill använda den hanterade tjänsten för Azure-tjänsten inkluderar du `spring-cloud-starter-netflix-eureka-client` i `POM.xml` som visas nedan.

Slut punkten för tjänst registrerings servern matas automatiskt in som miljövariabler med din app. Program kommer att kunna registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Distribuerad konfiguration

Om du vill aktivera distribuerad konfiguration inkluderar du `spring-cloud-config-client` i avsnittet beroenden i `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange inte `spring.cloud.config.enabled=false` i bootstrap-konfiguration eftersom det hindrar programmet från att fungera med konfigurations servern.

### <a name="metrics"></a>Mått

Inkludera `spring-boot-starter-actuator` i avsnittet beroenden i Pom. xml. Måtten kommer att hämtas regelbundet från JMX-slutpunkterna och kan visualiseras med hjälp av Azure Portal.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Distribuerad spårning

Inkludera `spring-cloud-starter-sleuth` och `spring-cloud-starter-zipkin` i avsnittet beroenden i Pom. XML enligt nedan. Du måste också aktivera en Azure App insikts-instans för att arbeta med din Azure våren Cloud Service-instans. Läs mer om hur du aktiverar App Insights med Azure våren Cloud [här](spring-cloud-tutorial-distributed-tracing.md)

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

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar ditt Java våren-program för distribution till Azure våren Cloud.  Fortsätt till nästa självstudie om du vill lära dig hur du aktiverar konfigurations servern.

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar konfigurations servern](spring-cloud-tutorial-config-server.md).

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

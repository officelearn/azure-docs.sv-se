---
title: Självstudie – förbereda ett Java våren-program för distribution i Azure våren Cloud
description: I den här självstudien förbereder du ett Java våren-program för distribution till Azure våren Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: af3611e4c4d1f5d8ca52b3ceb80d79dcfd7d2061
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190743"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Förbereda ett Java våren-program för distribution i Azure våren Cloud

Den här snabb starten visar hur du förbereder ett befintligt Java våren-program för distribution till Azure våren Cloud. Om Azure våren Cloud har kon figurer ATS korrekt tillhandahåller robusta tjänster för att övervaka, skala och uppdatera ditt Java våren Cloud-program.

## <a name="java-runtime-version"></a>Java Runtime-version

Endast våren/Java-program kan köras i Azure våren-molnet.

Azure våren Cloud stöder både Java 8 och Java 11. Värd miljön innehåller den senaste versionen av Azul Zulu OpenJDK för Azure. Mer information om Azul Zulu OpenJDK för Azure finns i [installera JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Moln versioner för våren start och våren

Azure våren Cloud stöder bara våren Boot-appar. Det stöder både våren Boot version 2,1 och version 2,2. I följande tabell visas de kombinationer som stöds av våren-start och våren:

Start version för våren | Våren Cloud-version
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton. RELEASE

Kontrol lera att Pom. XML-filen har rätt våren-start-och våren-moln beroenden baserat på din våren Boot-version.

### <a name="dependencies-for-spring-boot-version-21"></a>Beroenden för vår start version 2,1

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

Azure våren-moln är värdar för och hanterar fjäder moln komponenter åt dig. Dessa komponenter omfattar vår moln tjänst register och vår moln konfigurations Server. Ta med klient biblioteket Azure våren Cloud i dina beroenden för att tillåta kommunikation med din Azure våren Cloud Service-instans.

I följande tabell visas rätt Azure våren Cloud-versioner för din app som använder våren-start och våren Cloud.

Start version för våren | Våren Cloud-version | Azure våren Cloud-version
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton. RELEASE | 2.2

Inkludera något av följande beroenden i din Pom. XML-fil. Välj det beroende vars Azure våren Cloud-version matchar din egen.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Beroende av Azure våren Cloud version 2,1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Beroende av Azure våren Cloud version 2,2

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andra nödvändiga beroenden

För att aktivera de inbyggda funktionerna i Azure våren Cloud måste ditt program innehålla följande beroenden. Den här intagningen säkerställer att programmet konfigurerar sig korrekt med varje komponent.  

### <a name="service-registry-dependency"></a>Tjänstens register beroende

Om du vill använda den hanterade tjänsten för Azure-tjänsten ska du inkludera `spring-cloud-starter-netflix-eureka-client` beroende i Pom. XML-filen som visas här:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Slut punkten för tjänst registrerings servern matas automatiskt in som miljövariabler med din app. Program kan registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.

### <a name="distributed-configuration-dependency"></a>Beroende av distribuerad konfiguration

Om du vill aktivera distribuerad konfiguration inkluderar du följande `spring-cloud-config-client` beroende i avsnittet beroenden i din Pom. XML-fil:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange inte `spring.cloud.config.enabled=false` i Start konfigurationen. Annars slutar programmet att fungera med konfigurations servern.

### <a name="metrics-dependency"></a>Mått beroende

Ta med `spring-boot-starter-actuator` beroendet i avsnittet beroenden i Pom. XML-filen som visas här:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Måtten hämtas regelbundet från JMX-slutpunkterna. Du kan visualisera måtten med hjälp av Azure Portal.

### <a name="distributed-tracing-dependency"></a>Distribuerat spårnings beroende

Ta med följande `spring-cloud-starter-sleuth` och `spring-cloud-starter-zipkin` beroenden i avsnittet beroenden i din Pom. XML-fil:

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

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar ditt Java våren-program för distribution till Azure våren Cloud. Fortsätt till nästa självstudie om du vill veta hur du konfigurerar en konfigurations Server instans.

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar en konfigurations Server instans](spring-cloud-tutorial-config-server.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).

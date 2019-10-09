---
title: Automatisera tjänst registret och identifiering
description: Lär dig hur du automatiserar tjänst identifiering och registrering med hjälp av vår moln tjänst register
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038748"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Identifiera och registrera dina våren Cloud-tjänster

Tjänst identifiering är ett nyckel krav för en mikroservad arkitektur.  Det tar tid att konfigurera varje klient manuellt och vi presenterar risken för mänskligt fel.  Azure våren Cloud Service-registret löser det här problemet.  När den har kon figurer ATS kommer en tjänst register server att styra registreringen och identifieringen av programmets mikrotjänster. Tjänst register servern hanterar ett register med de distribuerade mikrotjänster, aktiverar belastnings utjämning på klient sidan och tar bort tjänst leverantörer från klienter utan att behöva använda DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrera ditt program med vår moln tjänst register

Innan ditt program kan hantera registrering och identifiering av tjänster med hjälp av våren Cloud Service Registry måste flera beroenden ingå i programmets *Pom. XML-* fil.

Börja genom att lägga till en ögonblicks bilds lagrings plats i avsnittet *lagring* i din *Pom. XML*

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Inkludera beroenden

Härnäst inkluderar vi beroenden för *våren-Cloud-starter-Netflix-Eureka-client* och *våren-Cloud-starter-Azure-våren-Cloud-client* till din *Pom. XML*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Uppdatera den översta nivån-klassen

Slutligen lägger vi till en anteckning till den översta klassen i programmet

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Källan till moln tjänstens register server kommer att matas in som en miljö variabel i ditt program.  Mikrotjänster kommer nu att kunna registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.

Observera att det kan ta några minuter innan ändringarna sprids från servern till alla mikrotjänster.

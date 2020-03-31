---
title: Automatisera tjänstregistret och identifieringen
description: Lär dig hur du automatiserar identifiering och registrering av tjänster med Spring Cloud Service-registret
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278862"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Upptäck och registrera dina Spring Cloud-tjänster

Service Discovery är ett viktigt krav för en mikrotjänstbaserad arkitektur.  Konfigurera varje klient manuellt tar tid och introducerar risken för mänskliga fel.  Azure Spring Cloud Service Registry löser problemet.  När en tjänstregisterserver har konfigurerats styr du tjänstens registrering och identifiering för programmets mikrotjänster. Service-registerservern underhåller ett register över de distribuerade mikrotjänsterna, möjliggör belastningsutjämning på klientsidan och frikopplar tjänstleverantörer från klienter utan att förlita sig på DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrera ditt program med Spring Cloud Service-registret

Innan ditt program kan hantera tjänstregistrering och identifiering med Spring Cloud Service-registret måste flera beroenden inkluderas i programmets *pom.xml-fil.*
Inkludera beroenden för *våren-moln-starter-netflix-eureka-klient* och *fjäder-moln-starter-azure-spring-cloud-klient* till din *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Uppdatera klassen på den högsta nivån

Slutligen lägger vi till en anteckning till klassen på den högsta nivån i ditt program

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

Slutpunkten för Spring Cloud Service-registerservern kommer att injiceras som en miljövariabel i ditt program.  Mikrotjänster kommer nu att kunna registrera sig hos serviceregisterservern och upptäcka andra beroende mikrotjänster.

Observera att det kan ta några minuter innan ändringarna sprids från servern till alla mikrotjänster.

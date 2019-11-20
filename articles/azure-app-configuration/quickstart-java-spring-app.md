---
title: Snabbstart för att lära dig hur du använder Azure App Configuration | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med Java Spring-appar.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184945"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Snabb start: skapa en Java våren-app med Azure App konfiguration

I den här snabb starten inkluderar du Azure App konfiguration i en Java våren-app för att centralisera lagring och hantering av program inställningar separat från din kod.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- En [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) som stöds med version 8.
- [Apache maven](https://maven.apache.org/download.cgi) version 3,0 eller senare.

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **konfigurations utforskaren** >  **+ skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | /application/config.message | Hello |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Du kan använda [våren Initializr](https://start.spring.io/) för att skapa ett nytt fjäder Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

2. Ange följande alternativ:

   * Generera ett **Maven**-projekt med **Java**.
   * Ange en **våren Boot** -version som är lika med eller större än 2,0.
   * Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   * Lägga till beroendet **Web**.

3. När du har angett föregående alternativ väljer du **generera projekt**. När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. När du har extraherat filerna i det lokala systemet är ditt vanliga start program redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

2. Öppna filen *pom.xml* i ett redigeringsprogram och lägg till Spring Cloud Azure Config-starter i listan över `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Öppna huvudprogrammets Java-fil och lägg till `@EnableConfigurationProperties` för att aktivera den här funktionen.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Skapa en ny fil med namnet `bootstrap.properties` under katalogen resurser i din app och Lägg till följande rader i filen. Ersätt exempel värdena med lämpliga egenskaper för appens konfigurations arkiv.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa ditt våren Boot-program med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. När programmet har körts använder du *sväng* för att testa programmet, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Du ser meddelandet som du angav i appens konfigurations arkiv.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det med en Java våren-app. Mer information finns i [våren på Azure](https://docs.microsoft.com/java/azure/spring-framework/). Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomst till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)

---
title: Snabbstart för att lära dig hur du använder Azure App-konfiguration
description: En snabbstart för användning av Azure App Configuration med Java Spring-appar.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: c156d5f1242674adc53a2a813e9b2c140221ecfb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245318"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Snabbstart: Skapa en Java Spring-app med Azure App-konfiguration

I den här snabbstarten införlivar du Azure App-konfiguration i en Java Spring-app för att centralisera lagring och hantering av programinställningar som är åtskilda från din kod.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- Ett [Java Development Kit (Som stöds)](https://docs.microsoft.com/java/azure/jdk) med version 8.
- [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 eller högre.

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Konfigurationsutforskaren** > **+ Skapa** > **nyckelvärde** om du vill lägga till följande nyckel-värde-par:

    | Nyckel | Värde |
    |---|---|
    | /application/config.message | Hello |

    Lämna **etikett** och **innehållstyp** tomma för tillfället.

7. Välj **Använd**.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Använd [Spring Initializr](https://start.spring.io/) för att skapa ett nytt Spring Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

1. Ange följande alternativ:

   - Generera ett **Maven**-projekt med **Java**.
   - Ange en **Spring Boot-version** som är lika med eller större än 2.0.
   - Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   - Lägg till **fjäderwebbberoendet.**

1. När du har angett föregående alternativ väljer du **Generera projekt**. När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. När du extraherar filerna på ditt lokala system är ditt enkla Spring Boot-program redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

1. Öppna filen *pom.xml* i ett redigeringsprogram och lägg till Spring Cloud Azure Config-starter i listan över `<dependencies>`:

    **Vårmoln 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Vårmoln 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

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

1. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

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

1. Öppna huvudprogrammets Java-fil och lägg till `@EnableConfigurationProperties` för att aktivera den här funktionen.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Skapa en ny `bootstrap.properties` fil som namnges under appens resurskatalog och lägg till följande rader i filen. Ersätt exempelvärdena med lämpliga egenskaper för App Configuration Store.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Ange en miljövariabel med namnet **APP_CONFIGURATION_CONNECTION_STRING**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. På kommandoraden kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

    ```cmd
        setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
        $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```cmd
        export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Bygg din Spring Boot ansökan med Maven och kör den, till exempel:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. När programmet har körts använder du *curl* för att testa ditt program, till exempel:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Meddelandet som du angav i App Configuration Store visas.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny App Configuration Store och använde den med en Java Spring-app. Mer information finns i [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). Om du vill veta hur du aktiverar javafjäderappen för att dynamiskt uppdatera konfigurationsinställningarna fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-java-spring-app.md)

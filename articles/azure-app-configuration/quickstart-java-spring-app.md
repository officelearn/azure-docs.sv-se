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
ms.openlocfilehash: d023c6ec9c3d24400fd2b7b9fcce9568aa851214
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202271"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Snabbstart: Skapa en Java Spring-app med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Du kan använda den för att enkelt lagra och hantera alla programinställningarna på ett ställe som är avgränsade från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en Java Spring-app.

Du kan använda valfri Kodredigerare för att utföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill göra den här snabbstarten, installera en stödd [Java Development Kit (JDK)](https://aka.ms/azure-jdks) med version 8 och [Apache Maven](https://maven.apache.org/) version 3.0 eller senare.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **nyckel/värde-Explorer** > **+ skapa** att lägga till följande nyckel / värde-par:

    | Nyckel | Värde |
    |---|---|
    | /application/config.message | Hello |

    Lämna **etikett** och **innehållstyp** tom för tillfället.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Du använder den [Spring Initializr](https://start.spring.io/) att skapa ett nytt Spring Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

2. Ange följande alternativ:

   * Generera ett **Maven**-projekt med **Java**.
   * Ange en **Spring Boot** version som är lika med eller större än 2,0.
   * Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   * Lägga till beroendet **Web**.

3. När du har angett de föregående alternativen väljer **generera projekt**. När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till en appbutik för konfiguration

1. När du har extraherat filerna på den lokala datorn är enkel Spring Boot programmet redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

2. Öppna filen *pom.xml* i ett redigeringsprogram och lägg till Spring Cloud Azure Config-starter i listan över `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
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

6. Skapa en ny fil med namnet `bootstrap.properties` under katalogen resurser för din app, och Lägg till följande rader i filen. Ersätt exempelvärdena med lämpliga egenskaper för konfiguration av lagra din app.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa din Spring Boot-program med Maven och kör den, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. När programmet körs, använda *curl* att testa ditt program, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Du ser meddelandet som du angav i konfigurationen appbutiken.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapade en ny konfiguration appbutik och används med en Java Spring-app. Mer information finns i [Spring på Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Om du vill veta mer om hur du använder Appkonfiguration kan du fortsätta till nästa självstudie som visar autentisering.

> [!div class="nextstepaction"]
> [Hanterad identitet integration](./howto-integrate-azure-managed-service-identity.md)

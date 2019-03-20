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
ms.openlocfilehash: 050a7a3718cb7c9eb864b7ed6ea0787c079e31b9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226325"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Snabbstart: Skapa en Java Spring-app med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Du kan använda den för att enkelt lagra och hantera alla programinställningarna på ett ställe som är avgränsade från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en Java Spring-app.

Du kan använda valfri Kodredigerare för att utföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här snabbstarten, installera en stödd [Java Development Kit (JDK)](https://aka.ms/azure-jdks) med version 8 och [Apache Maven](https://maven.apache.org/) version 3.0 eller senare.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

1. Om du vill skapa en ny konfiguration appbutik, logga in på den [Azure-portalen](https://aka.ms/azconfig/portal). I det övre vänstra hörnet på sidan Välj **+ skapa en resurs**. I den **Sök på Marketplace** anger **Appkonfiguration** och tryck på RETUR.

    ![Sök efter App Configuration](./media/quickstarts/azure-app-configuration-new.png)

2. Välj **Appkonfiguration** sökresultat och välj sedan **skapa**.

3. På den **Appkonfiguration** > **skapa** anger du följande inställningar.

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn som ska användas för appkonfigurationsarkivets resurs. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte börja eller sluta med den `-` tecknet eller efterföljande `-` tecken är ogiltiga.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration, det väljs automatiskt och **prenumeration** inte visas i listrutan. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resursgrupp för appkonfigurationsarkivets resurs. Den här gruppen är användbart för att ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Skapa resursen i samma region som andra komponenter i ditt program för bästa prestanda. |

    ![Skapa ett appkonfigurationsarkiv](./media/quickstarts/azure-app-configuration-create.png)

4. Välj **Skapa**. Distributionen kan ta några minuter att slutföra.

5. När distributionen är klar, Välj **inställningar** > **åtkomstnycklar**. Notera antingen eller primära skrivskyddade primärnyckelns anslutningssträng. Du kan använda den här anslutningssträngen senare för att konfigurera ditt program kan kommunicera med appbutiken för konfigurationen som du skapade. Anslutningssträngen har följande format:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Använd hela strängen i ditt program.

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
        <version>1.1.0.M1</version>
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
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
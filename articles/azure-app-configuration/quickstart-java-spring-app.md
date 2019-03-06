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
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960638"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Snabbstart: Skapa en Java Spring-app med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Med den kan du enkelt lagra och hantera alla programinställningar på ett ställe som är separat från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en Java Spring-app.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten installerar du en [Java Development Kit (JDK)](https://aka.ms/azure-jdks) med version 8 som stöds och [Apache Maven](http://maven.apache.org/) version 3.0 eller senare.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

1. För att skapa ett nytt appkonfigurationsarkiv loggar du först in på [Azure-portalen](https://aka.ms/azconfig/portal). Klicka på **+ Skapa en resurs** uppe till vänster på sidan. I textrutan **Sök på Marketplace** skriver du **App Configuration** och trycker på **Enter**.

    ![Sök efter App Configuration](./media/quickstarts/azure-app-configuration-new.png)

2. Klicka på **App Configuration** från sökresultatet och sedan på **Skapa**.

3. På sidan **App Configuration** > **Skapa** anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn som ska användas för appkonfigurationsarkivets resurs. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs den automatiskt och listrutan **Prenumeration** visas inte. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resursgrupp för appkonfigurationsarkivets resurs. Den här gruppen är användbar när du vill ta bort flera resurser samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |

    ![Skapa ett appkonfigurationsarkiv](./media/quickstarts/azure-app-configuration-create.png)

4. Klicka på **Skapa**. Det kan ta några minuter att slutföra distributionen.

5. När distributionen är klar klickar du på **Inställningar** > **Åtkomstnycklar**. Anteckna anslutningssträngen för antingen den skrivskyddade primärnyckeln eller primärnyckeln för läsning/skrivning. Du använder det här senare för att konfigurera programmet till att kommunicera med det appkonfigurationsarkiv som du just har skapat. Anslutningssträngen har följande format:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Du behöver använda hela strängen i programmet.

6. Klicka på **Key/Value Explorer** (Utforskare för nyckel/värde) och **+ Skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | /application/config.message | Hello |

    Låta **Label** (Etikett) och **Content Type** (Innehållstyp) vara tomma för tillfället.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Du kommer att använda [Spring Initializr](https://start.spring.io/) för att skapa ett nytt Spring Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

2. Ange följande alternativ:

   * Generera ett **Maven**-projekt med **Java**.
   * Ange en **Spring Boot**-version som är lika med eller större än 2.0.
   * Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   * Lägga till beroendet **Web**.

3. När du har angett de alternativ som beskrivs ovan klickar du på **Generate Project** (Generera projekt). När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="connect-to-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. När du har extraherat filerna på den lokala datorn är det enkla Spring Boot-programmet redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

2. Öppna filen *pom.xml* i ett redigeringsprogram och lägg till Spring Cloud Azure Config-starter i listan över `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog. Lägg till följande rader.

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

4. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog. Lägg till följande rader.

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

6. Skapa en ny fil med namnet `bootstrap.yaml` i appens resurskatalog, lägg till följande rader i filen och ersätt exempelvärdena med lämpliga egenskaper för ditt appkonfigurationsarkiv.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa Spring Boot-programmet med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. När programmet körs kan du använda *curl* för att testa programmet, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Du bör se det meddelande som du angav i appkonfigurationsarkivet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nytt appkonfigurationsarkiv och använt det med en Java Spring-app. Besök [Spring på Azure-startsidan](https://docs.microsoft.com/java/azure/spring-framework/) om du vill få mer information.

Om du vill lära dig mer om att använda App Configuration fortsätter du till nästa självstudie som visar hur autentisering går till.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
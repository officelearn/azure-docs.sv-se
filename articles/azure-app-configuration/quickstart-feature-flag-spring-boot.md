---
title: Snabb start för att lägga till funktions flaggor till våren Boot-Azure App Configuration | Microsoft Docs
description: En snabb start för att lägga till funktions flaggor till våren Boot Apps och hantera dem i Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 3e82354116969b01743700485b5c2dd75b4887e4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310075"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Snabb start: Lägg till funktions flaggor i en våren Boot-app

I den här snabb starten inkluderar du Azure App konfiguration i en våren Boot-webbapp för att skapa en slut punkt till slutpunkt-implementering av funktions hantering. Du kan använda app Configuration service för att centralt lagra alla funktions flaggor och kontrol lera deras tillstånd.

Biblioteken våren Boot Feature Management utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek har **inget** beroende av några Azure-bibliotek. De integreras sömlöst med app-konfigurationen via sin våren Boot Configuration Provider.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- En [Java Development Kit SDK](https://docs.microsoft.com/java/azure/jdk) som stöds med version 8.
- [Apache maven](https://maven.apache.org/download.cgi) version 3,0 eller senare.

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **funktions hanteraren** >  **+ skapa** för att lägga till följande funktions flaggor:

    | Nyckel | Status |
    |---|---|
    | Beta | Av |

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Du kan använda [våren Initializr](https://start.spring.io/) för att skapa ett nytt fjäder Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

2. Ange följande alternativ:

   - Generera ett **Maven**-projekt med **Java**.
   - Ange en **våren Boot** -version som är lika med eller större än 2,0.
   - Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.
   - Lägg till **våren-** webbberoendet.

3. När du har angett föregående alternativ väljer du **generera projekt**. När du uppmanas laddar du ned projektet till en sökväg på den lokala datorn.

## <a name="add-feature-management"></a>Lägg till funktions hantering

1. När du har extraherat filerna i det lokala systemet är ditt vanliga start program redo för redigering. Leta upp filen *pom.xml* i appens rotkatalog.

2. Öppna filen *Pom. XML* i en text redigerare och Lägg till våren Cloud Azure config starter och funktions hantering i listan över `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Det finns ett bibliotek för funktions hantering utanför webben som inte är beroende av våren-webben. Se ytterligare [dokument](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) för olikheter. När du inte använder konfiguration av appar ser du även [deklaration för funktions flagga](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration).

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Öppna _bootstrap. Properties_ under katalogen _resurser_ i din app. Om _bootstrap. Properties_ inte finns skapar du det. Lägg till följande rad i filen.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. I appens konfigurations Portal för config Store går du till åtkomst nycklar. Välj fliken skrivskyddade nycklar. På den här fliken kopierar du värdet för en av anslutnings strängarna och lägger till den som en ny miljö variabel med variabel namnet `APP_CONFIGURATION_CONNECTION_STRING`.

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

1. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog. Lägg till följande rader:

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

1. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog. Lägg till följande rader:

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

1. Skapa en ny HTML-fil med namnet *Welcome. html* i mappen mallar i appen. Lägg till följande rader:

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Skapa en ny mapp med namnet CSS under statisk och inuti den som en ny CSS-fil med namnet *main. CSS*. Lägg till följande rader:

    ```css
    html {
    position: relative;
    min-height: 100%;
    }
    body {
    margin-bottom: 60px;
    }
    .footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    line-height: 60px;
    background-color: #f5f5f5;
    }

    body > .container {
    padding: 60px 15px 0;
    }

    .footer > .container {
    padding-right: 15px;
    padding-left: 15px;
    }

    code {
    font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa ditt våren Boot-program med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Öppna ett webbläsarfönster och gå till `https://localhost:8080`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. I konfigurations portalen för app väljer du **funktions hanteraren**och ändrar status för **beta** nyckeln till **på**:

    | Nyckel | Status |
    |---|---|
    | Beta | På |

4. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det för att hantera funktioner i en våren Boot-webbapp via [biblioteken för funktions hantering](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktions hantering](./concept-feature-management.md).
- [Hantera funktions flaggor](./manage-feature-flags.md).
- [Använd funktions flaggor i en webbapp Boot Core-app](./use-feature-flags-spring-boot.md).

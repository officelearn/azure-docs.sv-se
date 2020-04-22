---
title: Snabbstart för att lägga till funktionsflaggor i Spring Boot med Azure App-konfiguration
description: Lägga till funktionsflaggor i Spring Boot-appar och hantera dem med Azure App-konfiguration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: e6dc51250987e0282530209ffa13e52d6e75aa9c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687370"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Snabbstart: Lägga till funktionsflaggor i en spring boot-app

I den här snabbstarten införlivar du Azure App-konfiguration i en Spring Boot-webbapp för att skapa en implementering från slutna till. Du kan använda tjänsten Appkonfiguration för att centralt lagra alla dina funktionsflaggor och styra deras tillstånd.

Spring Boot Feature Management bibliotek utöka ramen med omfattande funktion flagga stöd. Dessa bibliotek har **inte** ett beroende av några Azure-bibliotek. De integreras sömlöst med App-konfiguration via sin Spring Boot-konfigurationsleverantör.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* En [Java Development Kit SDK](https://docs.microsoft.com/java/azure/jdk) som stöds med version 8.
* [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 eller högre.

## <a name="create-an-app-configuration-instance"></a>Skapa en appkonfigurationsinstans

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Funktionshanteraren** > **+Lägg** till `Beta`om du vill lägga till en funktionsflagga med namnet .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktionsflagga med namnet Beta](media/add-beta-feature-flag.png)

    Lämna `label` odefinierad för tillfället.

## <a name="create-a-spring-boot-app"></a>Skapa en Spring Boot-app

Använd [Spring Initializr](https://start.spring.io/) för att skapa ett nytt Spring Boot-projekt.

1. Bläddra till <https://start.spring.io/>.

1. Ange följande alternativ:

   * Generera ett **Maven**-projekt med **Java**.
   * Ange en **Spring Boot-version** som är lika med eller större än 2.0.
   * Ange namnen för **Group** (Grupp) och **Artifact** (Artefakt) för ditt program.  Den här `com.example` `demo`artikeln använder och .
   * Lägg till **fjäderwebbberoendet.**

1. När du har angett föregående alternativ väljer du **Generera projekt**. När du uppmanas till det laddar du ned projektet till den lokala datorn.

## <a name="add-feature-management"></a>Lägga till funktionshantering

1. När du har extraherat filerna på ditt lokala system är springstartprogrammet redo för redigering. Leta reda på *pom.xml* i appens rotkatalog.

1. Öppna *filen pom.xml* i en textredigerare `<dependencies>`och lägg till följande i listan över:

    **Vårmoln 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Vårmoln 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Det finns ett icke-webbfunktionshanteringsbibliotek som inte är beroende av vårens webb. Se GitHubs [dokumentation](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) för skillnader.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Navigera till `resources` katalogen för `bootstrap.properties`din app och öppna .  Om filen inte finns skapar du den. Lägg till följande rad i filen.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Välj `Access keys` från sidofältet i appkonfigurationsportalen för konfigurationsarkivet. Välj fliken Skrivskyddade nycklar. Kopiera värdet för den primära anslutningssträngen.

1. Lägg till den primära anslutningssträngen `APP_CONFIGURATION_CONNECTION_STRING`som en miljövariabel med variabelnamnet .

1. Öppna huvudprogrammets Java-fil och lägg till `@EnableConfigurationProperties` för att aktivera den här funktionen.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Skapa en ny Java-fil med namnet *MessageProperties.java* i appens paketkatalog.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Skapa en ny Java-fil med namnet *HelloController.java* i appens paketkatalog.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("Beta").block());
            return "welcome";
        }
    }
    ```

1. Skapa en ny HTML-fil med namnet *welcome.html* i mallkatalogen för din app.

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

1. Skapa en ny mapp `static` med namnet CSS under och inuti en ny CSS-fil med namnet *main.css*.

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

1. Skapa Spring Boot-appen med Maven och kör den:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Öppna ett webbläsarfönster och gå `http://localhost:8080/welcome`till webbadressen: .

    ![Snabbstart av lokal app](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. I appkonfigurationsportalen väljer du **Funktionshanteraren**och ändrar **betanyckelns** tillstånd till **På**:

    | Nyckel | Status |
    |---|---|
    | Beta | På |

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny App Configuration Store och använde den för att hantera funktioner i en Spring Boot-webbapp via [funktionshanteringsbiblioteken](https://go.microsoft.com/fwlink/?linkid=2074664).

* Läs mer om [funktionshantering](./concept-feature-management.md).
* [Hantera funktionsflaggor](./manage-feature-flags.md).
* [Använd funktionsflaggor i en Spring Boot Core-app](./use-feature-flags-spring-boot.md).

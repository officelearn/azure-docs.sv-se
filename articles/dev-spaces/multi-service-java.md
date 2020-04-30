---
title: 'Köra flera beroende tjänster: Java & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Den här självstudien visar hur du använder Azure dev Spaces och Visual Studio Code för att felsöka ett Java-program med flera tjänster på Azure Kubernetes service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: beab91964cab9938a5d63584089326bb408f6efc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75438335"
---
# <a name="running-multiple-dependent-services-java-and-visual-studio-code-with-azure-dev-spaces"></a>Köra flera beroende tjänster: Java och Visual Studio Code med Azure dev Spaces

I den här självstudien lär du dig att utveckla program för flera tjänster med hjälp av Azure Dev Spaces samt några av de fördelar som finns i Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Anropa en tjänst som körs i en separat container

I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat container. Du ska sedan felsöka båda containrarna.

![Flera containrar](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Ladda ned exempelkoden för *mywebapi*
För enkelhetens skull laddar vi ned exempelkoden från en GitHub-databas. Gå till https://github.com/Azure/dev-spaces och välj **Klona eller Ladda ned** för att ladda ned GitHub-databasen. Koden för det här avsnittet finns i `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna mappen `mywebapi` i ett *separat VS Code-fönster*.
1. Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Du vet att det är klart när ett meddelande som liknar nedan visas i fel söknings konsolen:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. Slutpunktens URL ser ut ungefär så här: `http://localhost:<portnumber>`. **Tips! statusfältet för VS-koden blir orange och visar en klicknings bar URL.** Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklarmiljön i Azure. localhost-adressen beror på att `mywebapi` inte har definierat några offentliga slutpunkter och endast kan nås från Kubernetes-instansen. För enkelhetens skull, och för att underlätta interaktionen med den privata tjänsten från den lokala datorn, skapar Azure Dev Spaces en tillfällig SSH-tunnel för containern som körs i Azure.
1. När `mywebapi` är redo öppnar du webbläsaren på localhost-adressen.
1. Om alla steg lyckades bör du se ett svar från `mywebapi`-tjänsten.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Skicka en begäran från *webfrontend* till *mywebapi*
Nu ska vi skriva kod i `webfrontend` som skickar en begäran till `mywebapi`.
1. Växla till VS Code-fönstret för `webfrontend`.
1. *Lägg till* följande `import` instruktioner under `package`-instruktionen:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Ersätt* koden för hälsningsmetoden:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

I föregående kodexempel vidarebefordras rubriken `azds-route-as` från den inkommande till den utgående begäran. Lite längre fram ser du hur detta kan underlätta utvecklingsarbetet i ett team.

### <a name="debug-across-multiple-services"></a>Felsöka över flera tjänster
1. I detta läge bör `mywebapi` fortfarande köras med felsökaren. Om inte trycker du på F5 i `mywebapi`-projektet.
1. Ange en Bryt punkt i `index()` `mywebapi` projektets metod på [rad 19 i `Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java#L19)
1. I `webfrontend`-projektet lägger du till en brytpunkt precis innan en GET-begäran skickas till `mywebapi` på raden som börjar med `try`.
1. Tryck på F5 i `webfrontend`-projektet (eller starta om felsökningsprogrammet om det körs).
1. Anropa webbappen och stega igenom koden i båda tjänsterna.
1. I webbappen visar sidan Om ett sammanslaget meddelande från de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Bra gjort!
Nu har du ett program med flera containrar där varje container kan utvecklas och distribueras separat.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om teamutveckling i Dev Spaces](team-development-java.md)

---
title: Köra flera beroende tjänster med Java och VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
manager: yuvalm
ms.openlocfilehash: 9f371be384214447c9d98f6223dd830f7c240326
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707454"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Utveckling av flera tjänster med Azure Dev Spaces

I den här självstudien får du lära dig att utveckla program med flera tjänster med hjälp av Azure Dev Spaces, samt några av de fördelar som finns i Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Anropa en tjänst som körs i en separat container

I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat container. Du ska sedan felsöka båda containrarna.

![Flera containrar](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Ladda ned exempelkoden för *mywebapi*
För enkelhetens skull laddar vi ned exempelkoden från en GitHub-databas. Gå till https://github.com/Azure/dev-spaces och välj **Klona eller Ladda ned** för att ladda ned GitHub-databasen. Koden för det här avsnittet finns i `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna mappen `mywebapi` i ett *separat VS Code-fönster*.
1. Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Du vet att den är klar när ett meddelande som liknar den nedan visas i Felsökningskonsolen:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. Slutpunktens URL ser ut ungefär så här: `http://localhost:<portnumber>`. **Tips: Statusfältet i VS Code innehåller en klickbar URL.** Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklarmiljön i Azure. localhost-adressen beror på att `mywebapi` inte har definierat några offentliga slutpunkter och endast kan nås från Kubernetes-instansen. För enkelhetens skull, och för att underlätta interaktionen med den privata tjänsten från den lokala datorn, skapar Azure Dev Spaces en tillfällig SSH-tunnel för containern som körs i Azure.
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
1. Konfigurera en brytpunkt i den `index()`-metoden för `webapi`-projektet.
1. I `webfrontend`-projektet lägger du till en brytpunkt precis innan en GET-begäran skickas till `mywebapi` på raden som börjar med `try`.
1. Tryck på F5 i `webfrontend`-projektet (eller starta om felsökningsprogrammet om det körs).
1. Anropa webbappen och stega igenom koden i båda tjänsterna.
1. I webbappen visar sidan ”Om” ett sammanslaget meddelande från de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi” (Hej från webfrontend och hej från mywebapi).

### <a name="automatic-tracing-for-http-messages"></a>Automatisk spårning av HTTP-meddelanden
Du har kanske märkt att även om *webfrontend* inte innehåller någon särskild kod för att skriva ut HTTP-anropet till *mywebapi* så kan du se att HTTP spårar meddelanden i utdatafönstret:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
Detta är en av de ”extra” förmåner du får från Dev Spaces-instrumenteringen. Vi har lagt till komponenter som spårar HTTP-förfrågningar när de passerar systemet, så att det blir enklare för dig att spåra komplexa anrop till flera tjänster under utvecklingen.

### <a name="well-done"></a>Bra gjort!
Nu har du ett program med flera containrar där varje container kan utvecklas och distribueras separat.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om teamutveckling i Dev Spaces](team-development-java.md)

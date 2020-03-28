---
title: 'Köra flera beroende tjänster: Node.js & Visual Studio-kod'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Den här självstudien visar hur du använder Azure Dev Spaces och Visual Studio Code för att felsöka ett nodeprogram för flera tjänster på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
ms.openlocfilehash: a5fa0aae3a966dd96ee95e6bcafc3b2eec4e6837
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438300"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Köra flera beroende tjänster: Node.js och Visual Studio-kod med Azure Dev Spaces

I den här självstudien lär du dig att utveckla program för flera tjänster med hjälp av Azure Dev Spaces samt några av de fördelar som finns i Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Anropa en tjänst som körs i en separat container

I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat container. Du ska sedan felsöka båda containrarna.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Öppna exempelkod för *mywebapi*
Du bör redan ha `mywebapi` exempelkoden för den `samples` här guiden under https://github.com/Azure/dev-spaces en mapp som heter (om inte, gå till och välj **Klona eller Hämta** för att hämta GitHub-databasen.) Koden för det här `samples/nodejs/getting-started/mywebapi`avsnittet finns i .

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna mappen `mywebapi` i ett *separat VS Code-fönster*.
1. Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Det här kommandot ska inte förväxlas med kommandot `azds prep` som konfigurerar projektet för distribution.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Du vet att det är klart när meddelandet *Lyssna på port 80* visas i felsökningskonsolen.
1. Skriv ned slutpunktens URL, som ser ut ungefär så här: `http://localhost:<portnumber>`. **Vs-kodens statusfält blir orange och visar en klickbar webbadress.** Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure. localhost-adressen beror på att `mywebapi` inte har definierat några offentliga slutpunkter och endast kan nås från Kubernetes-instansen. För enkelhetens skull, och för att underlätta interaktionen med den privata tjänsten från den lokala datorn, skapar Azure Dev Spaces en tillfällig SSH-tunnel för containern som körs i Azure.
1. När `mywebapi` är redo öppnar du webbläsaren på localhost-adressen. Du bör se ett svar från `mywebapi`-tjänsten (”Hello from mywebapi”).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Skicka en begäran från *webfrontend* till *mywebapi*
Nu ska vi skriva kod i `webfrontend` som skickar en begäran till `mywebapi`.
1. Växla till VS Code-fönstret för `webfrontend`.
1. Lägg till följande kodrader längst upp i `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Ersätt* koden för `/api`-GET-hanteraren. När du hanterar en begäran gör den i sin tur ett anrop till `mywebapi` och returnerar sedan resultatet från båda tjänsterna.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Ta bort* raden `server.close()` i slutet av `server.js`

I föregående kodexempel vidarebefordras rubriken `azds-route-as` från den inkommande till den utgående begäran. Lite längre fram ser du hur detta kan underlätta utvecklingsarbetet i ett team.

### <a name="debug-across-multiple-services"></a>Felsöka över flera tjänster
1. I detta läge bör `mywebapi` fortfarande köras med felsökaren. Om inte trycker du på F5 i `mywebapi`-projektet.
1. Ställ in en brytpunkt `/` inuti standard GET-hanteraren [på rad 8 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8)i .
1. I `webfrontend`-projektet lägger du till en brytpunkt precis innan en GET-begäran skickas till `http://mywebapi`.
1. Tryck på F5 i `webfrontend`-projektet.
1. Öppna webbappen och stega igenom koden i båda tjänsterna. Webbappen bör visa ett sammanslaget meddelande för de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Bra gjort!
Nu har du ett program med flera containrar där varje container kan utvecklas och distribueras separat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om teamutveckling i Dev Spaces](team-development-nodejs.md)

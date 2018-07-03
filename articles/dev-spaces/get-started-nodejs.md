---
title: Skapa en molnbaserad utvecklingsmiljö för Node.js och Kubernetes med VS-kod | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: efd19393a661a48a566e85a058dad071c3bdb63c
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945997"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Komma igång med Azure Dev Spaces med Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Nu är du redo att skapa en Kubernetes-baserad utvecklingsmiljö i Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Installera Azure CLI
Azure Dev Spaces kräver minimal konfiguration av den lokala datorn. Merparten av utvecklingsmiljöns konfiguration lagras i molnet och kan delas med andra användare. Börja genom att ladda ned och köra [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Om du redan har installerat Azure CLI kontrollerar du att du använder version 2.0.38 eller senare.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Du kan börja skriva kod medan du väntar på att klustret skapas.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Skapa en Node.js-behållare i Kubernetes

I det här avsnittet ska du skapa en Node.js-webbapp och köra den i en behållare i Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Skapa en Node.js-webbapp
Ladda ned koden från GitHub genom att gå till https://github.com/Azure/dev-spaces och välja **Klona eller Ladda ned** för att ladda ned GitHub-databasen till den lokala miljön. Koden för den här guiden finns i `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil
Azure Dev Spaces handlar om mer än att bara få kod att köra i Kubernetes – det handlar om att du snabbt och löpande kan se effekten av dina kodändringar i en Kubernetes-miljö i molnet.

1. Leta upp filen `./public/index.html` och gör en ändring i HTML-koden. Ändra till exempel sidans bakgrundsfärg till en blå nyans:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Spara filen. Efter en liten stund visas ett meddelande i terminalfönstret som meddelar att en fil i den aktiva behållaren har uppdaterats.
1. Gå till webbläsaren och uppdatera sidan. Nu bör du se färguppdateringen som du gjorde.

Vad hände? Ändringar av innehållsfiler som HTML och CSS kräver inte att Node.js-processen startas om. Ett aktivt `azds up`-kommando synkroniserar i stället automatiskt modifierade innehållsfiler direkt i den aktiva behållaren i Azure, så att du snabbt ser dina innehållsändringar.

### <a name="test-from-a-mobile-device"></a>Testa från en mobil enhet
Öppna webbappen på en mobil enhet med hjälp av den offentliga URL:en för webfrontend. Du vill kanske kopiera och skicka URL: en från skrivbordet till din enhet för att inte behöva ange den långa adressen. När webbappen läsas in på den mobila enheten ser du att användargränssnittet inte visas korrekt på en liten enhet.

Du kan komma runt det här problemet genom att lägga till en `viewport`-metatagg:
1. Öppna filen `./public/index.html`
1. Lägg till en `viewport`-metatagg i det befintliga `head`-elementet:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Spara filen.
1. Uppdatera enhetens webbläsare. Nu bör webbappen återges korrekt på enheten. 

Detta är ett exempel på hur vissa problem inte upptäcks förrän du testar på de enheter som appen är avsedd att användas på. Med Azure Dev Spaces kan du snabbt omarbeta koden och kontrollera ändringarna på målenheterna.

### <a name="update-a-code-file"></a>Uppdatera en kodfil
Uppdateringar av kodfiler på serversidan kräver lite mer arbete eftersom en Node.js-app måste startas om.

1. Tryck på `Ctrl+C` (för att stoppa `azds up`) i terminalfönstret.
1. Öppna kodfilen `server.js` och redigera tjänstens välkomstmeddelande: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Spara filen.
1. Kör `azds up` i terminalfönstret. 

När du gör det återskapas behållaravbildningen och Helm-diagrammet distribueras på nytt. Bekräfta att kodändringarna har tillämpats genom att läsa in sidan i webbläsaren igen.

Det finns dock en ännu *snabbare kodutvecklingsmetod*, som vi ska titta närmare på i nästa avsnitt. 

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en behållare i Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Välj AZDS-felsökningskonfigurationen
1. Du öppnar felsökningsvyn genom att klicka på felsökningsikonen i **aktivitetsfältet** längs kanten i VS Code.
1. Välj **Start program (AZDS)** (Starta program (AZDS)) som aktiv felsökningskonfiguration.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon på kommandopaletten kontrollerar du att du har [installerat VS Code-tillägget för Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Felsöka behållaren i Kubernetes
Tryck på **F5** för att felsöka koden i Kubernetes.

På liknande sätt som med `up`-kommandot synkroniseras koden med utvecklingsmiljön när du startar felsökningen, och en behållare skapas och distribueras till Kubernetes. Den här gången är felsökaren kopplad till fjärrbehållaren.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Lägg till en brytpunkt i en kodfil på serversidan, t.ex. i `app.get('/api'...` i `server.js`. Uppdatera sidan i webbläsaren eller tryck på knappen ”Say It Again” (Säg det igen), så kommer du till brytpunkten och kan börja stega igenom koden.

Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

### <a name="edit-code-and-refresh-the-debug-session"></a>Redigera koden och uppdatera felsökningssessionen
Gör en kodändring medan felsökaren är aktiv. Du kan till exempel ändra hello-meddelandet igen:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Spara filen och klicka på knappen **Uppdatera** i **fönstret Felsökningsåtgärder**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

I stället för att skapa och distribuera om en ny behållaravbildning varje gång koden ändras, vilket ofta tar lång tid, startar Azure Dev Spaces om Node.js-processen mellan felsökningssessioner för att snabba upp redigerings- och felsökningsförloppet.

Uppdatera webbappen i webbläsaren eller tryck på knappen *Say It Again* (Säg det igen). Nu bör ditt anpassade meddelande visas i användargränssnittet.

### <a name="use-nodemon-to-develop-even-faster"></a>Snabba upp utvecklingen ännu mer med NodeMon
*Nodemon* är ett populärt verktyg som Node.js-utvecklare använder för snabb utveckling. I stället för att starta om Node-processen manuellt varje gång en kodredigering på serversidan görs, konfigurerar utvecklare ofta Node-projektet så att *nodemon* övervakar filändringar och startar om serverprocessen automatiskt. På så sätt behöver utvecklaren bara uppdatera webbläsaren efter en kodändring.

Med Azure Dev Spaces kan du använda många av de utvecklingsarbetsflöden som du använder när du utvecklar lokalt. För att illustrera detta har `webfrontend`-exempelprojektet konfigurerats att använda *nodemon* (det har konfigurerats som ett utvecklingsberoende i `package.json`).

Prova följande steg:
1. Stoppa VS Code-felsökaren.
1. Klicka på ikonen Felsök i **aktivitetsfältet** längs kanten i VS-kod. 
1. Välj **Attach (AZDS)** (Koppla (AZDS)) som aktiv felsökningskonfiguration.
1. Tryck på F5.

I den här konfigurationen konfigureras behållaren att starta *nodemon*. När kodredigeringar görs på servern startar *nodemon* automatiskt om Node-processen, precis som när du utvecklar lokalt. 
1. Redigera hello-meddelande igen i `server.js` och spara filen.
1. Bekräfta att ändringarna har tillämpats genom att uppdatera webbläsaren eller genom att klicka på knappen *Say It Again* (Säg det igen).

**Nu vet du hur du snabbt kan arbeta med kod och felsöka direkt i Kubernetes!** Nu ska vi gå vidare och se hur du kan skapa och anropa en andra behållare.

## <a name="call-a-service-running-in-a-separate-container"></a>Anropa en tjänst som körs i en separat behållare

I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat behållare. Du ska sedan felsöka båda behållarna.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Öppna exempelkod för *mywebapi*
Du bör redan ha exempelkoden för `mywebapi` för den här guiden under en mapp med namnet `samples` (om inte går du till https://github.com/Azure/dev-spaces och väljer **Klona eller Ladda ned** för att ladda ned GitHub-databasen). Koden för det här avsnittet finns i `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna mappen `mywebapi` i ett *separat VS Code-fönster*.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Felsökningsfältet i VS Code visas när åtgärden har slutförts.
1. Skriv ned slutpunktens URL, som ser ut ungefär så här: http://localhost:\<portnumber\>. **Tips! Statusfältet i VS Code innehåller en klickbar URL.** Det kan verka som om behållaren körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure. localhost-adressen beror på att `mywebapi` inte har definierat några offentliga slutpunkter och endast kan nås från Kubernetes-instansen. För enkelhetens skull, och för att underlätta interaktionen med den privata tjänsten från den lokala datorn, skapar Azure Dev Spaces en tillfällig SSH-tunnel för behållaren som körs i Azure.
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

I föregående kodexempel vidarebefordras rubriken `azds-route-as` från den inkommande till den utgående begäran. Lite längre fram ser du hur detta kan underlätta utvecklingsarbetet i ett team.

### <a name="debug-across-multiple-services"></a>Felsöka över flera tjänster
1. I detta läge bör `mywebapi` fortfarande köras med felsökaren. Om inte trycker du på F5 i `mywebapi`-projektet.
1. Lägg till en brytpunkt i `/`-GET-standardhanteraren.
1. I `webfrontend`-projektet lägger du till en brytpunkt precis innan en GET-begäran skickas till `http://mywebapi`.
1. Tryck på F5 i `webfrontend`-projektet.
1. Öppna webbappen och stega igenom koden i båda tjänsterna. Webbappen bör visa ett sammanslaget meddelande för de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi”.

Bra gjort! Nu har du ett program med flera behållare där varje behållare kan utvecklas och distribueras separat.

## <a name="learn-about-team-development"></a>Lär dig mer om utveckling i team

[!INCLUDE[](includes/team-development-1.md)]

Nu ska vi se hur det fungerar i praktiken:
1. Gå till VS Code-fönstret för `mywebapi` och gör en kodändring i `/`-GET-standardhanteraren, till exempel:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]





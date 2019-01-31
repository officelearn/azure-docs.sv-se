---
title: Skapa en molnbaserad utvecklingsmiljö för Node.js och Kubernetes med VS-kod | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 91c4bc76a7753c7d51310fbe9b807f0969ca900c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467296"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Komma igång med Azure Dev Spaces med Node.js

I den här guiden får du lära dig hur du:

- Skapa en Kubernetes-baserad miljö i Azure som är optimerad för utveckling, en _utvecklarmiljö_.
- Utveckla kod iterativt i containrar med VS Code och kommandoraden.
- Effektivt utvecklar och testar din kod i en teammiljö.

> [!Note]
> **Om du fastnar** du kan när som helst referera till avsnittet [Felsökning](troubleshooting.md) eller lägga upp en kommentar på den här sidan.

Nu är du redo att skapa en Kubernetes-baserad utvecklingsmiljö i Azure.

## <a name="install-the-azure-cli"></a>Installera Azure CLI
Azure Dev Spaces kräver minimal konfiguration av den lokala datorn. Merparten av utvecklarmiljöns konfiguration lagras i molnet och kan delas med andra användare. Den lokala datorn kan köra Windows, Mac eller Linux. För Linux stöds följande distributioner: Ubuntu (18.04, 16.04 och 14.04), Debian 8 och 9, RHEL 7, Fedora 26+, CentOS 7, openSUSE 42.2 och SLES 12.

Börja genom att ladda ned och köra [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Om du redan har installerat Azure CLI kontrollerar du att du använder version 2.0.43 eller senare.

### <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI
Logga in i Azure. Skriv in följande kommando i ett terminalfönster:

```cmd
az login
```

> [!Note]
> Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Om du har flera Azure-prenumerationer ...
Du kan visa dina prenumerationer genom att köra: 

```cmd
az account list
```
Leta upp den prenumeration som har `isDefault: true` i JSON-utdata.
Om det här inte är den prenumeration som du vill använda kan du ändra standardprenumerationen:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Skapa ett Kubernetes-kluster som är aktiverat för Azure Dev Spaces

I kommandotolken skapar du resursgruppen. Använd någon av regionerna som stöds för närvarande (USA, östra; USA, östra 2; USA, centrala; USA, västra 2; Europa, västra; Asien, sydöstra; Kanada, centrala eller Kanada, östra).

```cmd
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
```

Det tar några minuter att skapa klustret.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera ditt AKS-kluster för att använda Azure Dev Spaces

Ange följande Azure CLI-kommando med hjälp av den resursgrupp som innehåller ditt AKS-kluster och AKS-klusternamn. Kommandot konfigurerar ditt kluster med stöd för Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Azure Dev Spaces-konfigurationsprocessen tar bort namnområdet `azds` i klustret, om det finns.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Använda Kubernetes-felsökning för VS Code
Avancerade funktioner som Kubernetes-felsökning är tillgängliga för .NET Core- och Node.js-utvecklare som använder VS Code.

1. Installera [VS Code](https://code.visualstudio.com/Download) om du inte redan har det.
1. Ladda ned och installera [VS Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Klicka på Installera en gång på tilläggets Marketplace-sida och igen i VS Code. 

## <a name="create-a-nodejs-container-in-kubernetes"></a>Skapa en Node.js-container i Kubernetes

I det här avsnittet ska du skapa en Node.js-webbapp och köra den i en container i Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Skapa en Node.js-webbapp
Ladda ned koden från GitHub genom att gå till https://github.com/Azure/dev-spaces och välja **Klona eller Ladda ned** för att ladda ned GitHub-databasen till den lokala miljön. Koden för den här guiden finns i `samples/nodejs/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Förbereda kod för Docker- och Kubernetes-utveckling
Hittills har du en grundläggande webbapp som kan köras lokalt. Du kommer nu använda den i en container genom att skapa tillgångar som definierar appens container och hur den kommer att distribueras till Kubernetes. Den här uppgiften är enkel att utföra med Azure Dev Spaces: 

1. Starta VS Code och öppna mappen `webfrontend`. (Du kan ignorera eventuella standarduppmaningar om att lägga till felsökningstillgångar eller återställa projektet.)
1. Öppna den integrerade terminalen i VS Code (med hjälp av menyn **Visa > Integrerade terminal**).
1. Kör det här kommandot (se till att **webfrontend** är din aktuella mapp):

    ```cmd
    azds prep --public
    ```

Kommandot `azds prep` i Azure CLI genererar Docker- och Kubernetes-tillgångar med standardinställningarna:
* `./Dockerfile` beskriver appens containeravbildning och hur källkoden byggs och körs i containern.
* Ett [Helm-diagram](https://docs.helm.sh) under `./charts/webfrontend` beskriver hur du distribuerar containern till Kubernetes.

För tillfället är det inte nödvändigt att förstå det fullständiga innehållet i dessa filer. Det är dock värt att påpeka att **samma Kubernetes- och Docker-konfiguration som kod-tillgångar kan användas från utveckling till produktion, vilket ger bättre konsekvens mellan olika miljöer.**
 
En fil med namnet `./azds.yaml` genereras även av kommandot `prep`, och det är konfigurationsfilen för Azure Dev Spaces. Den kompletterar Docker- och Kubernetes-artefakterna med ytterligare konfiguration som möjliggör en iterativ utvecklingsprocess i Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes
Nu kör vi vår kod! Kör det här kommandot från webbklientdelen **rotmappen för kod** i terminalfönstret:

```cmd
azds up
```

Håll ett öga på kommandots utdata – du kommer att se flera saker under förloppet:
- Källkoden synkroniseras till utvecklarmiljön i Azure.
- En containeravbildning skapas i Azure, enligt Docker-tillgångarna i kodmappen.
- Kubernetes-objekt som använder containeravbildningen enligt Helm-diagrammet i kodmappen skapas.
- Information om containerns slutpunkt(er) visas. I det här fallet förväntar vi oss en offentlig HTTP-URL.
- Förutsatt att ovanstående steg slutförs ordentligt kan du börja se `stdout`- (och `stderr`-) utdata när containern startas.

> [!Note]
> De här stegen tar längre tid första gången kommandot `up` körs, men efterföljande körningar bör gå snabbare.

### <a name="test-the-web-app"></a>Testa webbappen
Skanna konsolens utdata för information om den offentliga webbadressen som skapades av kommandot `up`. Den kommer att vara i formen: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
```

Öppna webbadressen i ett webbläsarfönster. Du bör nu se hur webbappen läses in. När containern körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.

> [!Note]
> Första gången det körs kan det ta några minuter innan DNS är redo. Om den offentliga URL:en inte fungerar kan du använda den alternativa http://localhost:<portnumber>-URL:en som visas i konsolens utdata. Om du använder localhost-URL:en kan det verka som om containern körs lokalt, men i själva verket körs den i AKS. För enkelhetens skull och för att underlätta interaktionen med tjänsten från den lokala datorn skapar Azure Dev Spaces en tillfällig SSH-tunnel för containern som körs i Azure. Du kan komma tillbaka och testa den offentliga URL:en senare när DNS-posten är färdig.

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil
Azure Dev Spaces handlar om mer än att bara få kod att köra i Kubernetes – det handlar om att du snabbt och löpande kan se effekten av dina kodändringar i en Kubernetes-miljö i molnet.

1. Leta upp filen `./public/index.html` och gör en ändring i HTML-koden. Ändra till exempel sidans bakgrundsfärg till en blå nyans:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Spara filen. Efter en liten stund visas ett meddelande i terminalfönstret som meddelar att en fil i den aktiva containern har uppdaterats.
1. Gå till webbläsaren och uppdatera sidan. Nu bör du se färguppdateringen som du gjorde.

Vad hände? Ändringar av innehållsfiler som HTML och CSS kräver inte att Node.js-processen startas om. Ett aktivt `azds up`-kommando synkroniserar i stället automatiskt modifierade innehållsfiler direkt i den aktiva containern i Azure, så att du snabbt ser dina innehållsändringar.

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

Detta exempel visar att vissa problem inte upptäcks förrän du testar på de enheter som appen är avsedd att användas på. Med Azure Dev Spaces kan du snabbt omarbeta koden och kontrollera ändringarna på målenheterna.

### <a name="update-a-code-file"></a>Uppdatera en kodfil
Uppdateringar av kodfiler på serversidan kräver lite mer arbete eftersom en Node.js-app måste startas om.

1. Tryck på `Ctrl+C` (för att stoppa `azds up`) i terminalfönstret.
1. Öppna kodfilen `server.js` och redigera tjänstens välkomstmeddelande: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Spara filen.
1. Kör `azds up` i terminalfönstret. 

Det här kommandot återskapar containeravbildningen och distribuerar Helm-diagrammet på nytt. Bekräfta att kodändringarna har tillämpats genom att läsa in sidan i webbläsaren igen.

Det finns dock en ännu *snabbare kodutvecklingsmetod*, som vi ska titta närmare på i nästa avsnitt. 

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en container i Kubernetes

I det här avsnittet ska du använda VS Code för att direkt felsöka våra containrar som körs i Azure. Du får också lära dig hur du kan få en snabbare redigera-kör-test-loop.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Om du fastnar** du kan när som helst referera till avsnittet [Felsökning](troubleshooting.md) eller lägga upp en kommentar på den här sidan.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initiera felsökningstillgångar med VS Code-tillägget
Du måste först konfigurera kodprojektet så att VS Code kommunicerar med vår utvecklarmiljö i Azure. VS Code-tillägget för Azure Dev Spaces har ett hjälpkommando för att konfigurera felsökningskonfigurationen. 

Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Då läggs felsökningskonfigurationen för Azure Dev Spaces till under mappen `.vscode`. Det här kommandot ska inte förväxlas med kommandot `azds prep` som konfigurerar projektet för distribution.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Välj AZDS-felsökningskonfigurationen
1. Du öppnar felsökningsvyn genom att klicka på felsökningsikonen i **aktivitetsfältet** längs kanten i VS Code.
1. Välj **Start program (AZDS)** (Starta program (AZDS)) som aktiv felsökningskonfiguration.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon på kommandopaletten kontrollerar du att du har [installerat VS Code-tillägget för Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Felsöka containern i Kubernetes
Tryck på **F5** för att felsöka koden i Kubernetes.

På liknande sätt som med `up`-kommandot synkroniseras koden med utvecklingsmiljön när du startar felsökningen, och en container skapas och distribueras till Kubernetes. Den här gången är felsökaren kopplad till fjärrcontainern.

> [!Tip]
> Statusfältet i VS Code innehåller en klickbar URL.

![](media/common/vscode-status-bar-url.png)

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

I stället för att skapa och distribuera om en ny containeravbildning varje gång koden ändras, vilket ofta tar lång tid, startar Azure Dev Spaces om Node.js-processen mellan felsökningssessioner för att snabba upp redigerings- och felsökningsförloppet.

Uppdatera webbappen i webbläsaren eller tryck på knappen *Say It Again* (Säg det igen). Nu bör ditt anpassade meddelande visas i användargränssnittet.

### <a name="use-nodemon-to-develop-even-faster"></a>Snabba upp utvecklingen ännu mer med NodeMon
*Nodemon* är ett populärt verktyg som Node.js-utvecklare använder för snabb utveckling. I stället för att starta om Node-processen manuellt varje gång en kodredigering på serversidan görs, konfigurerar utvecklare ofta Node-projektet så att *nodemon* övervakar filändringar och startar om serverprocessen automatiskt. På så sätt behöver utvecklaren bara uppdatera webbläsaren efter en kodändring.

Med Azure Dev Spaces kan du använda många av de utvecklingsarbetsflöden som du använder när du utvecklar lokalt. För att illustrera detta konfigurerades `webfrontend`-exempelprojektet för att använda *nodemon* (det är konfigurerat som ett utvecklingsberoende i `package.json`).

Prova följande steg:
1. Stoppa VS Code-felsökaren.
1. Klicka på ikonen Felsök i **aktivitetsfältet** längs kanten i VS-kod. 
1. Välj **Attach (AZDS)** (Koppla (AZDS)) som aktiv felsökningskonfiguration.
1. Tryck på F5.

I den här konfigurationen konfigureras containern att starta *nodemon*. När kodredigeringar görs på servern startar *nodemon* automatiskt om Node-processen, precis som när du utvecklar lokalt. 
1. Redigera hello-meddelande igen i `server.js` och spara filen.
1. Bekräfta att ändringarna har tillämpats genom att uppdatera webbläsaren eller genom att klicka på knappen *Say It Again* (Säg det igen).

**Nu vet du hur du snabbt kan arbeta med kod och felsöka direkt i Kubernetes!** Nu ska vi gå vidare och se hur du kan skapa och anropa en andra container.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om teamutveckling](team-development-nodejs.md)


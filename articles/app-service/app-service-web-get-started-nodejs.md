---
title: 'Snabb start: skapa en Node.js webbapp'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter. Du distribuerar med Visual Studio Code, som är en av många sätt att distribuera till App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18, devx-track-javascript
ms.openlocfilehash: 0e72c17ab20d092a710bb21b1ff6d3d6418e452f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170269"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure 

Kom igång med Azure App Service genom att skapa en Node.js/Express-app lokalt med Visual Studio Code och sedan distribuera appen till molnet. Eftersom du använder en kostnads fri App Service nivå debiteras du inga kostnader för att slutföra den här snabb starten.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js och NPM](https://nodejs.org). Kör kommandot `node --version` för att kontrol lera att Node.js är installerat.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) för Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klona och köra ett lokalt Node.js program

1. Öppna en terminal på den lokala datorn och klona exempel lagrings platsen:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigera till den nya mappen app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Starta appen för att testa den lokalt:

    ```bash
    npm start
    ```
    
1. Öppna webbläsaren och gå till `http://localhost:1337` . Webbläsaren ska Visa "Hello World!".

1. Tryck på **CTRL** + **C** i terminalen för att stoppa servern.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här avsnittet ska du distribuera din Node.js-app till Azure med VS Code och Azure App Service tillägget.

1. I terminalen kontrollerar du att du är i mappen *NodeJS-dok-Hello-World* och startar sedan Visual Studio Code med följande kommando:

    ```bash
    code .
    ```

1. I aktivitets fältet VS Code väljer du Azure-logotypen för att visa **Azure App Service** Explorer. Välj **Logga in på Azure...** och följ instruktionerna. (Mer information finns i [fel sökning av Azure-inloggning](#troubleshooting-azure-sign-in) nedan om du stöter på fel.) När du har loggat in ska Explorer Visa namnet på din Azure-prenumeration.

    ![Logga in på Azure](containers/media/quickstart-nodejs/sign-in.png)

1. I **Azure App Service** Explorer med vs Code väljer du ikonen blå UPPIL för att distribuera appen till Azure. (Du kan också anropa samma kommando från **kommando rads verktyget** (**CTRL** + **Shift** + **P**) genom att skriva "distribuera till webbapp" och välja **Azure App Service: distribuera till Web App**).

    ![Distribuera till webbapp](containers/media/quickstart-nodejs/deploy.png)
        
1. Välj mappen *NodeJS-dok-Hello-World* .

1. Välj ett alternativ för att skapa baserat på det operativ system som du vill distribuera till:

    - Linux: Välj **Skapa ny webbapp**
    - Windows: Välj **Skapa ny webbapp... Avancerad**

1. Ange ett globalt unikt namn för din webbapp och tryck på **RETUR**. Namnet måste vara unikt för alla Azure och använder endast alfanumeriska tecken ("A-Z", "a-z" och "0-9") och bindestreck (-).

1. Om du anger Linux-mål väljer du en Node.js version när du uppmanas till det. En **LTS** -version rekommenderas.

1. Om du riktar in dig på Windows följer du ytterligare prompter:
    1. Välj **skapa en ny resurs grupp**och ange sedan ett namn för resurs gruppen, till exempel `AppServiceQS-rg` .
    1. Välj **Windows** för operativ systemet.
    1. Välj **Skapa ny app service plan**och ange sedan ett namn för planen (till exempel `AppServiceQS-plan` ) och välj **F1 kostnads fritt** för pris nivån.
    1. Välj **hoppa över för tillfället** när du tillfrågas om Application Insights.
    1. Välj en region nära dig eller nära resurser som du vill ha åtkomst till.

1. När du har svarat på alla prompter visar VS Code de Azure-resurser som skapas för din app i popup-fönstret för meddelanden.

    När du distribuerar till Linux väljer du **Ja** när du uppmanas att uppdatera konfigurationen så att den körs `npm install` på mål-Linux-servern.

    ![Prompt för att uppdatera konfigurationen på mål servern för Linux](containers/media/quickstart-nodejs/server-build.png)

1. Välj **Ja** när du uppmanas **att alltid distribuera arbets ytan "NodeJS-dok-Hello-World" till (App Name) "**. Välj **Ja** om du vill att vs-kod automatiskt ska rikta in samma app service-webbapp med efterföljande distributioner.

1. Om du distribuerar till Linux väljer du **Bläddra på webbplats** i prompten för att visa din nyligen distribuerade webbapp när distributionen är klar. Webbläsaren ska Visa "Hello World!"

1. Om du distribuerar till Windows måste du först ange Node.js versions nummer för webbappen:

    1. I VS Code expanderar du noden för den nya app service, högerklickar på **program inställningar**och väljer **Lägg till ny inställning...**:

        ![Lägg till app Setting-kommando](containers/media/quickstart-nodejs/add-setting.png)

    1. Ange `WEBSITE_NODE_DEFAULT_VERSION` för inställnings nyckeln.
    1. Ange `10.15.2` som inställnings värde.
    1. Högerklicka på noden för app service och välj **starta om**

        ![Starta om App Service-kommando](containers/media/quickstart-nodejs/restart.png)

    1. Högerklicka på noden för App Service en gång till och välj **Bläddra webbplats**.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Felsöka Azure-inloggning

Om du ser felet **"det går inte att hitta en prenumeration med namnet [prenumerations-ID]"** när du loggar in på Azure kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om du ställer in miljövariablerna inte löser problemet kan du kontakta oss genom att välja knappen **jag körde i ett ärende** ovan.

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att göra redigeringar i VS Code, spara filer och sedan använda samma process som innan du väljer den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

Du kan visa loggens utdata (anrop till `console.log` ) från appen direkt i fönstret vs-kod.

1. I **Azure App Service** Explorer högerklickar du på noden app och väljer **Starta strömmande loggar**.

    ![Starta strömmande loggar](containers/media/quickstart-nodejs/view-logs.png)

1. När du uppmanas väljer du att aktivera loggning och starta om programmet. När appen har startats om öppnas VS Code-utdatafönstret med en anslutning till logg strömmen. 

    ![Aktivera loggning och omstart](containers/media/quickstart-nodejs/enable-restart.png)

1. Efter några sekunder visar utdatafönstret ett meddelande som anger att du är ansluten till logg strömnings tjänsten. Du kan generera fler utdata-aktiviteter genom att uppdatera sidan i webbläsaren.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

> [!div class="nextstepaction"]
> [Självstudie: Node.js app med MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Node.js app](configure-language-nodejs.md)

Kolla in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera [Node Pack för Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.


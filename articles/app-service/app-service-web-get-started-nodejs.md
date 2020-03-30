---
title: 'Snabbstart: Skapa en nod.js-webbapp'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter. Du distribuerar med Visual Studio-kod, vilket är ett av många sätt att distribuera till App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047122"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure 

Kom igång med Azure App Service genom att skapa en Node.js/Express-app lokalt med Visual Studio-kod och sedan distribuera appen till molnet. Eftersom du använder en kostnadsfri App Service-nivå ådrar du dig inga kostnader för att slutföra den här snabbstarten.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js och npm](https://nodejs.org). Kör kommandot `node --version` för att kontrollera att Node.js är installerat.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service-tillägget](vscode:extension/ms-azuretools.vscode-azureappservice) för Visual Studio-kod.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klona och kör ett lokalt Node.js-program

1. Öppna en terminal och klona exempeldatabasen på den lokala datorn:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigera till den nya appmappen:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Starta appen för att testa den lokalt:

    ```bash
    npm start
    ```
    
1. Öppna webbläsaren och [http://localhost:1337](http://localhost:1337)navigera till . Webbläsaren ska visa "Hello World!".

1. Tryck på **Ctrl**+**C** i terminalen för att stoppa servern.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här avsnittet distribuerar du node.js-appen till Azure med VS-kod och Azure App Service-tillägget.

1. I terminalen kontrollerar du att du är i mappen *nodejs-docs-hello-world* och starta sedan Visual Studio-kod med följande kommando:

    ```bash
    code .
    ```

1. I aktivitetsfältet VS-kod väljer du Azure-logotypen för att visa **AZURE APP SERVICE-utforskaren.** Välj **Logga in på Azure...** och följ instruktionerna. (Se [Felsöka Azure-inloggning](#troubleshooting-azure-sign-in) nedan om du stöter på fel.) När utforskaren har loggat in ska den visa namnet på din Azure-prenumeration.

    ![Logga in på Azure](containers/media/quickstart-nodejs/sign-in.png)

1. I **AZURE APP SERVICE explorer** av VS-kod väljer du ikonen för blå uppil för att distribuera din app till Azure. (Du kan också anropa samma kommando från **kommandopaletten** (**Ctrl**+**Shift**+**P**) genom att skriva "distribuera till webbapp" och välja Azure **App Service: Deploy to Web App**).

    ![Distribuera till Web App](containers/media/quickstart-nodejs/deploy.png)
        
1. Välj *mappen nodejs-docs-hello-world.*

1. Välj ett skapandealternativ baserat på det operativsystem som du vill distribuera:

    - Linux: Välj **Skapa ny webbapp**
    - Windows: Välj **Skapa ny webbapp... Avancerad**

1. Skriv ett globalt unikt namn för webbappen och tryck på **Retur**. Namnet måste vara unikt i alla Azure och endast använda alfanumeriska tecken ("A-Z", "a-z" och "0-9") och bindestreck ('-').

1. Om du inriktar dig på Linux väljer du en Node.js-version när du uppmanas att göra det. En **LTS-version** rekommenderas.

1. Om du inriktar dig på Windows följer du de ytterligare anvisningarna:
    1. Välj **Skapa en ny resursgrupp**och ange sedan ett `AppServiceQS-rg`namn för resursgruppen, till exempel .
    1. Välj **Windows** för operativsystemet.
    1. Välj **Skapa ny apptjänstplan**och ange sedan ett `AppServiceQS-plan`namn för planen (till exempel ) och välj sedan **F1 Gratis** för prisnivån.
    1. Välj **Hoppa över för tillfället** när du uppmanas att göra om Application Insights.
    1. Välj en region nära dig eller nära resurser som du vill komma åt.

1. När du har svarat på alla frågor visar VS-koden de Azure-resurser som skapas för din app i meddelande popup.

    När du distribuerar **Yes** till Linux väljer du Ja `npm install` när du uppmanas att uppdatera konfigurationen så att den körs på mål-Linux-servern.

    ![Fråga om att uppdatera konfigurationen på mål-Linux-servern](containers/media/quickstart-nodejs/server-build.png)

1. Välj **Ja** när du uppmanas att **alltid distribuera arbetsytan "nodejs-docs-hello-world" till (appnamn)"**. Om du väljer **Ja** får VS-koden vs-kod att automatiskt rikta in sig på samma App Service Web App med efterföljande distributioner.

1. Om du distribuerar till Linux väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp när distributionen är klar. Webbläsaren ska visa "Hello World!"

1. Om du distribuerar till Windows måste du först ange versionsnumret för Node.js för webbappen:

    1. Expandera noden för den nya apptjänsten i VS-kod, högerklicka på **Programinställningar**och välj **Lägg till ny inställning...**

        ![Kommandot Lägg till appinställning](containers/media/quickstart-nodejs/add-setting.png)

    1. Ange `WEBSITE_NODE_DEFAULT_VERSION` för inställningsnyckeln.
    1. Ange `10.15.2` för inställningsvärdet.
    1. Högerklicka på noden för apptjänsten och välj **Starta om**

        ![Kommandot Starta om apptjänsten](containers/media/quickstart-nodejs/restart.png)

    1. Högerklicka på noden för apptjänsten en gång till och välj **Bläddra webbplats**.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Felsöka Azure-inloggning

Om du ser felet **"Det går inte att hitta prenumeration med namn [prenumerations-ID]"** när du loggar in på Azure kan det bero på att du ligger bakom en proxy och inte kan nå Azure API. Konfigurera `HTTP_PROXY` `HTTPS_PROXY` och miljövariabler med proxyinformationen i terminalen med . `export`

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om inställningen av miljövariablerna inte korrigerar problemet kontaktar du oss genom att välja knappen **Jag körde in i en problemknapp** ovan.

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att göra ändringar i VS-kod, spara dina filer och sedan använda samma process som innan du bara väljer den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

Du kan visa loggutdata (anrop till) `console.log`från appen direkt i utdatafönstret VS-kod.

1. Högerklicka på appnoden i **AZURE APP SERVICE-utforskaren** och välj Starta **strömningsloggar**.

    ![Starta strömmande loggar](containers/media/quickstart-nodejs/view-logs.png)

1. När du uppmanas att aktivera loggning och starta om programmet. När appen har startats om öppnas utdatafönstret VS-kod med en anslutning till loggströmmen. 

    ![Aktivera loggning och omstart](containers/media/quickstart-nodejs/enable-restart.png)

1. Efter några sekunder visas ett meddelande i utdatafönstret som anger att du är ansluten till loggströmningstjänsten. Du kan generera mer utdataaktivitet genom att uppdatera sidan i webbläsaren.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört denna snabbstart!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem alla genom att installera [nodepaketet för](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Azure-tilläggspaketet.

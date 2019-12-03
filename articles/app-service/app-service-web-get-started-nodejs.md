---
title: 'Snabb start: skapa en Node. js-webbapp'
description: Distribuera din första Node. js-Hello World Azure App Service på några minuter. Du distribuerar med Visual Studio Code, som är en av många sätt att distribuera till App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/30/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 91494cc9c1e3a1fc159702bdbb7f68a4423b604c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671368"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure 

Med Azure App Service får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här snabbstarten visar hur du distribuerar en Node.js-app till Azure App Service.

## <a name="prerequisites"></a>Krav

Om du inte har ett Azure-konto kan du [Registrera dig](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) för ett kostnads fritt konto med $200 i Azure-krediter för att testa en kombination av tjänster.

Du behöver [Visual Studio Code](https://code.visualstudio.com/) installerat tillsammans med [Node. js och NPM](https://nodejs.org/en/download), Package Manager för Node. js.

Du måste också installera [Azure App Service tillägget](vscode:extension/ms-azuretools.vscode-azureappservice), som du kan använda för att skapa, hantera och distribuera Linux-Web Apps på Azure-plattformen som en tjänst (PaaS).

### <a name="sign-in"></a>Logga in

När tillägget har installerats loggar du in på ditt Azure-konto. I aktivitets fältet väljer du Azure-logotypen för att visa **Azure App Service** Explorer. Välj **Logga in på Azure...** och följ instruktionerna.

![Logga in på Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Felsöka

Om du ser felet **"det går inte att hitta prenumerationen med namnet [prenumerations-ID]"** kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om du ställer in miljövariablerna inte löser problemet kan du kontakta oss genom att välja knappen **jag körde i ett ärende** nedan.

### <a name="prerequisite-check"></a>Krav kontroll

Innan du fortsätter måste du kontrol lera att alla nödvändiga komponenter är installerade och konfigurerade.

I VS Code bör du se din Azure-e-postadress i statusfältet och din prenumeration i **Azure App Service** Explorer.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Skapa ett Node. js-program

Skapa sedan ett Node. js-program som kan distribueras till molnet. Den här snabb starten använder en program generator för att snabbt Autogenerera ut programmet från en Terminal.

> [!TIP]
> Om du redan har slutfört [självstudien Om Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)kan du gå vidare till [distribuera till Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Autogenerera ett nytt program med Express generatorn

[Express](https://www.expressjs.com) är ett populärt ramverk för att skapa och köra Node. js-program. Du kan Autogenerera (skapa) ett nytt Express-program med hjälp av verktyget [Express Generator](https://expressjs.com/en/starter/generator.html) . Express generatorn levereras som en NPM-modul och kan köras direkt (utan installation) med hjälp av kommando rads verktyget NPM `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git` parametrar anger att generatorn ska använda [pug](https://pugjs.org/api/getting-started.html) -mall (tidigare `jade`) och för att skapa en `.gitignore`-fil.

Om du vill installera alla program beroenden går du till den nya mappen och kör `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Köra programmet

Se sedan till att programmet körs. Starta-programmet från terminalen med hjälp av kommandot `npm start` för att starta servern.

```bash
npm start
```

Öppna din webbläsare och gå till [http://localhost:3000](http://localhost:3000), där du bör se något som liknar detta:

![Kör Express-program](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här avsnittet ska du distribuera Node. js-appen med VS Code och Azure App Service-tillägget. Den här snabb starten använder den mest grundläggande distributions modellen där appen är zippad och distribuerad till en Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Distribuera med Azure App Service

Öppna först mappen Application i VS Code.

```bash
code .
```

I **Azure App Service** Explorer väljer du ikonen blå UPPIL för att distribuera appen till Azure.

![Distribuera till webbapp](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> Du kan också distribuera från **kommando paletten** (Ctrl + Shift + P) genom att skriva "distribuera till webbapp" och köra kommandot **Azure App Service: distribuera till** webbapp.

1. Välj den katalog som du för närvarande är öppen `myExpressApp`.

1. Välj ett alternativ för att skapa baserat på det operativ system som du vill distribuera till:

    - Linux: Välj **Skapa ny webbapp**.
    - Windows: Välj **Skapa ny webbapp** och välj alternativet **Avancerat** .

1. Ange ett globalt unikt namn för din webbapp och tryck på RETUR. Giltiga tecken för ett app-namn är "a-z", "0-9" och "-".

1. Om du anger Linux-mål väljer du en Node. js-version när du uppmanas till det. En **LTS** -version rekommenderas.

1. Om du aktiverar Windows med alternativet **Avancerat** följer du de ytterligare anvisningarna:
    1. Välj **skapa en ny resurs grupp**och ange sedan ett namn för resurs gruppen.
    1. Välj **Windows** för operativ systemet.
    1. Välj en befintlig App Service plan eller skapa en ny. Du kan välja en pris nivå när du skapar en ny plan.
    1. Välj **hoppa över för tillfället** när du tillfrågas om Application Insights.
    1. Välj en region nära dig eller nära resurser som du vill ha åtkomst till.

1. När du har svarat på alla prompter visar meddelande kanalen de Azure-resurser som skapas för din app.

1. Välj **Ja** när du uppmanas att uppdatera konfigurationen för att köra `npm install` på mål servern. Din app distribueras sedan.

    ![Konfigurerad distribution](containers/media/quickstart-nodejs/server-build.png)

1. När distributionen startar uppmanas du att uppdatera arbets ytan så att senare distributioner automatiskt riktar sig till samma App Service-webbapp. Välj **Ja** för att se till att ändringarna distribueras till rätt app.

    ![Konfigurerad distribution](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Se till att ditt program lyssnar på porten som tillhandahålls av PORT miljö variabeln: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Bläddra i appen i Azure

När distributionen är klar väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp.

### <a name="troubleshooting"></a>Felsöka

Om du ser felet **"du har inte behörighet att visa den här katalogen eller sidan."** , kunde programmet antagligen inte starta korrekt. Gå till nästa avsnitt och Visa loggens utdata för att hitta och åtgärda felet. Om du inte kan åtgärda det, kontakta oss genom att välja knappen **jag körde i ett ärende** nedan. Vi är glada att hjälpa!

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att använda samma process och välja den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

I det här avsnittet får du lära dig att visa (eller "pilslut") loggarna från den App Service appen som körs. Alla anrop till `console.log` i appen visas i fönstret utdata i Visual Studio Code.

Hitta appen i **Azure App Service** Explorer, högerklicka på appen och välj **Visa strömmande loggar**.

När du uppmanas väljer du att aktivera loggning och starta om programmet. När appen har startats om öppnas VS Code-utdatafönstret med en anslutning till logg strömmen.

![Visa strömmande loggar](containers/media/quickstart-nodejs/view-logs.png)

![Aktivera loggning och omstart](containers/media/quickstart-nodejs/enable-restart.png)

Efter några sekunder visas ett meddelande som anger att du är ansluten till logg strömnings tjänsten. Uppdatera sidan några gånger för att se mer aktivitet.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera [Node Pack för Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.

---
title: Skapa en Node. js-webbapp – Azure App Service
description: Så här distribuerar du en Node. js-app till Azure App Service
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390980"
---
# <a name="create-a-nodejs-app-in-azure"></a>Skapa en Node. js-app i Azure

Med Azure App Service får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här snabbstarten visar hur du distribuerar en Node.js-app till Azure App Service.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har ett Azure-konto kan du [Registrera dig](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) för ett kostnads fritt konto med $200 i Azure-krediter för att testa en kombination av tjänster.

Du behöver [Visual Studio Code](https://code.visualstudio.com/) installerat tillsammans med [Node. js och NPM](https://nodejs.org/en/download), Package Manager för Node. js.

Du måste också installera [Azure App Service tillägget](vscode:extension/ms-azuretools.vscode-azureappservice), som du kan använda för att skapa, hantera och distribuera Linux-Web Apps på Azure-plattformen som en tjänst (PaaS).

### <a name="sign-in"></a>Logga in

När tillägget har installerats loggar du in på ditt Azure-konto. I aktivitets fältet klickar du på Azure-logotypen för att visa **Azure App Service** Explorer. Klicka på **Logga in på Azure...** och följ instruktionerna.

![Logga in på Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Felsökning

Om du ser felet **"det går inte att hitta prenumerationen med namnet [prenumerations-ID]"** kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` `export`och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om du ställer in miljövariablerna inte löser problemet kan du kontakta oss genom att klicka på knappen **jag körde i ett ärende** nedan.

### <a name="prerequisite-check"></a>Krav kontroll

Innan du fortsätter måste du kontrol lera att alla nödvändiga komponenter är installerade och konfigurerade.

I VS Code bör du se din Azure-e-postadress i statusfältet och din prenumeration i **Azure App Service** Explorer.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Skapa ett Node. js-program

Skapa sedan ett Node. js-program som kan distribueras till molnet. Den här snabb starten använder en program generator för att snabbt Autogenerera ut programmet från en Terminal.

> [!TIP]
> Om du redan har slutfört [självstudien Om Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)kan du gå vidare till [distribuera webbplatsen](#deploy-the-website).

### <a name="install-the-express-generator"></a>Installera Express Generator

[Express](https://www.expressjs.com) är ett populärt ramverk för att skapa och köra Node. js-program. Du kan Autogenerera (skapa) ett nytt Express-program med hjälp av verktyget [Express Generator](https://expressjs.com/en/starter/generator.html) . Express generatorn levereras som en NPM-modul och installeras med hjälp av kommando rads verktyget `npm`NPM.

```bash
npm install -g express-generator
```

`-g` Växeln installerar Express generatorn globalt på din dator så att du kan köra den var som helst.

### <a name="scaffold-a-new-application"></a>Autogenerera ett nytt program

Sedan Autogenerera ett nytt Express-program som `myExpressApp` kallas genom att köra:

```bash
express myExpressApp --view pug --git
```

Parametrarna anger att generatorn ska använda [pug](https://pugjs.org/api/getting-started.html) `jade`-mall (tidigare kallat) och för att skapa en `.gitignore` fil. `--view pug --git`

Om du vill installera alla program beroenden går du till den nya mappen och kör `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Köra programmet

Se sedan till att programmet körs. Starta-programmet från terminalen med hjälp `npm start` av kommandot för att starta servern.

```bash
npm start
```

Öppna din webbläsare och gå till [http://localhost:3000](http://localhost:3000), där du bör se något som liknar detta:

![Kör Express-program](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Distribuera webbplatsen

I det här avsnittet ska du distribuera Node. js-webbplatsen med VS Code och Azure App Service-tillägget. Den här snabb starten använder den mest grundläggande distributions modellen där appen är zippad och distribuerad till en Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Distribuera med Azure App Service

Öppna först mappen Application i VS Code.

```bash
code .
```

I **Azure App Service** Explorer klickar du på ikonen blå UPPIL för att distribuera appen till Azure.

![Distribuera till webbapp](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Du kan också distribuera från **kommando paletten** (Ctrl + Shift + P) genom att skriva "Deploy to Web App" och köra **Azure App Service:**  Kommandot distribuera till webbapp.

1. Välj den katalog som för närvarande är öppen `myExpressApp`.

2. Välj **Skapa ny webbapp**.

3. Ange ett globalt unikt namn för din webbapp och tryck på RETUR. Giltiga tecken för ett app-namn är "a-z", "0-9" och "-".

4. Välj **Node. js-versionen**, LTS rekommenderas.

    Meddelande kanalen visar de Azure-resurser som skapas för din app.

Klicka på **Ja** när du uppmanas att uppdatera konfigurationen `npm install` så att den körs på mål servern. Din app distribueras sedan.

![Konfigurerad distribution](./media/quickstart-nodejs/server-build.png)

När distributionen startar uppmanas du att uppdatera arbets ytan så att senare distributioner automatiskt riktar sig till samma App Service-webbapp. Välj **Ja** för att se till att ändringarna distribueras till rätt app.

![Konfigurerad distribution](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Se till att ditt program lyssnar på porten som tillhandahålls av PORT miljö variabeln: `process.env.PORT`.

### <a name="browse-the-website"></a>Bläddra på webbplatsen

När distributionen är klar klickar du på **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbplats.

### <a name="troubleshooting"></a>Felsökning

Om du ser felet **"du har inte behörighet att visa den här katalogen eller sidan."** , kunde programmet antagligen inte starta korrekt. Gå till nästa avsnitt och Visa loggens utdata för att hitta och åtgärda felet. Om du inte kan åtgärda det kan du kontakta oss genom att klicka på knappen **jag körde ett ärende** nedan. Vi är glada att hjälpa!

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Webbplatsen uppdateras

Du kan distribuera ändringar i den här appen genom att använda samma process och välja den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

I det här avsnittet får du lära dig att visa (eller "pilslut") loggarna från den webbplats som körs. Alla anrop till `console.log` på platsen visas i fönstret utdata i Visual Studio Code.

Hitta appen i **Azure App Service** Explorer, högerklicka på appen och välj **Visa strömmande loggar**.

När du uppmanas väljer du att aktivera loggning och starta om programmet. När appen har startats om öppnas VS Code-utdatafönstret med en anslutning till logg strömmen.

![Visa strömmande loggar](./media/quickstart-nodejs/view-logs.png)

![Aktivera loggning och omstart](./media/quickstart-nodejs/enable-restart.png)

Efter några sekunder visas ett meddelande som anger att du är ansluten till logg strömnings tjänsten.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Uppdatera sidan några gånger i webbläsaren för att se logg resultatet.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
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

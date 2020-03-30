---
title: 'Snabbstart: Skapa en Linux Node.js-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första Node.js-app till en Linux-behållare i App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: fb79f4b87d8ddc5303fea834c81329315401464a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74688977"
---
# <a name="create-a-nodejs-app-in-azure"></a>Skapa en nod.js-app i Azure

Med Azure App Service får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här snabbstarten visar hur du distribuerar en Node.js-app till Azure App Service.

## <a name="prerequisites"></a>Krav

Om du inte har ett Azure-konto [registrerar du dig idag](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) för ett kostnadsfritt konto med $200 i Azure-krediter för att prova en kombination av tjänster.

Du behöver [Visual Studio-kod](https://code.visualstudio.com/) installerad tillsammans med [Node.js och npm](https://nodejs.org/en/download), Node.js pakethanteraren.

Du måste också installera [Azure App Service-tillägget](vscode:extension/ms-azuretools.vscode-azureappservice), som du kan använda för att skapa, hantera och distribuera Linux Web Apps på Azure Platform as a Service (PaaS).

### <a name="sign-in"></a>Logga in

När tillägget har installerats loggar du in på ditt Azure-konto. I aktivitetsfältet väljer du Azure-logotypen för att visa **AZURE APP SERVICE-utforskaren.** Välj **Logga in på Azure...** och följ instruktionerna.

![logga in på Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Felsökning

Om du ser felet **"Det går inte att hitta prenumeration med namn [prenumerations-ID]"** kan det bero på att du ligger bakom en proxy och inte kan nå Azure-API:et. Konfigurera `HTTP_PROXY` `HTTPS_PROXY` och miljövariabler med proxyinformationen i terminalen med . `export`

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om inställningen av miljövariablerna inte korrigerar problemet kontaktar du oss genom att välja knappen **Jag stötte på ett problem** nedan.

### <a name="prerequisite-check"></a>Förutsättningskontroll

Innan du fortsätter kontrollerar du att du har alla förutsättningar installerade och konfigurerade.

I VS-kod bör du se din Azure-e-postadress i statusfältet och din prenumeration i **AZURE APP SERVICE explorer.**

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Skapa nod.js-programmet

Skapa sedan ett Node.js-program som kan distribueras till molnet. Denna snabbstart använder en applikationsgenerator för att snabbt byggnadsställningar ut programmet från en terminal.

> [!TIP]
> Om du redan har slutfört [nod.js-självstudien](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)kan du hoppa vidare till [Distribuera till Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Byggnadsställning ett nytt program med Express Generator

[Express](https://www.expressjs.com) är ett populärt ramverk för att bygga och köra Node.js-program. Du kan byggnadsställning (skapa) ett nytt Express-program med [expressgeneratorverktyget.](https://expressjs.com/en/starter/generator.html) Expressgeneratorn levereras som en npm-modul och kan köras direkt (utan `npx`installation) med hjälp av kommandoradsverktyget npm .

```bash
npx express-generator myExpressApp --view pug --git
```

Parametrarna `--view pug --git` talar om för generatorn att använda `jade` [mops](https://pugjs.org/api/getting-started.html) mallmotorn (tidigare känd som) och att skapa en `.gitignore` fil.

Om du vill installera alla programmets beroenden går `npm install`du till den nya mappen och kör .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Köra appen

Kontrollera sedan att programmet körs. Från terminalen startar du `npm start` programmet med kommandot för att starta servern.

```bash
npm start
```

Nu öppnar du webbläsaren [http://localhost:3000](http://localhost:3000)och navigerar till , där du ska se något liknande:

![Köra Express-program](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här avsnittet distribuerar du node.js-appen med VS-kod och Azure App Service-tillägget. Den här snabbstarten använder den mest grundläggande distributionsmodellen där din app zippas och distribueras till en Azure Web App på Linux.

### <a name="deploy-using-azure-app-service"></a>Distribuera med Azure App Service

Öppna först programmappen i VS-kod.

```bash
code .
```

I **AZURE APP SERVICE-utforskaren** väljer du ikonen för blå uppil för att distribuera din app till Azure.

![Distribuera till Web App](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Du kan också distribuera från **kommandopaletten** (CTRL + SKIFT + P) genom att skriva "distribuera till webbapp" och köra Azure **App Service: Distribuera till Web App.**

1. Välj den katalog som du `myExpressApp`för närvarande har öppen.

1. Välj **Skapa ny web app**, som distribuerar till App Service på Linux som standard.

1. Skriv ett globalt unikt namn för webbappen och tryck på RETUR. Giltiga tecken för ett appnamn är "a-z", "0-9" och '-'.

1. Välj **node.js-version**, LTS rekommenderas.

    Meddelandekanalen visar de Azure-resurser som skapas för din app.

1. Välj **Ja** när du uppmanas `npm install` att uppdatera konfigurationen så att den körs på målservern. Appen distribueras sedan.

    ![Konfigurerad distribution](./media/quickstart-nodejs/server-build.png)

1. När distributionen startar uppmanas du att uppdatera arbetsytan så att senare distributioner automatiskt inriktas på samma App Service Web App. Välj **Ja** för att se till att ändringarna distribueras till rätt app.

    ![Konfigurerad distribution](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Se till att ditt program lyssnar på porten som `process.env.PORT`tillhandahålls av PORT-miljövariabeln: .

### <a name="browse-the-app-in-azure"></a>Bläddra i appen i Azure

När distributionen är klar väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp.

### <a name="troubleshooting"></a>Felsökning

Om felet **"Du har inte behörighet att visa den här katalogen eller sidan."** så gick det förmodligen inte att starta korrekt i programmet. Gå till nästa avsnitt och visa loggutdata för att hitta och åtgärda felet. Om du inte kan fixa det, kontakta oss genom att välja **jag sprang in i ett problem** knappen nedan. Vi hjälper gärna till!

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att använda samma process och välja den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

I det här avsnittet får du lära dig hur du visar (eller "svans") loggarna från appen som körs. Alla anrop som tas `console.log` till i appen visas i utdatafönstret i Visual Studio-kod.

Hitta appen i **AZURE APP SERVICE** explorer, högerklicka på appen och välj Visa **strömmande loggar**.

När du uppmanas att aktivera loggning och starta om programmet. När appen har startats om öppnas utdatafönstret VS-kod med en anslutning till loggströmmen.

![Visa strömmande loggar](./media/quickstart-nodejs/view-logs.png)

![Aktivera loggning och omstart](./media/quickstart-nodejs/enable-restart.png)

Efter några sekunder visas ett meddelande om att du är ansluten till loggströmningstjänsten. Uppdatera sidan några gånger för att se mer aktivitet.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

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

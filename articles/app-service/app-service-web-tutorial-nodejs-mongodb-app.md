---
title: Skapa en Node.js- och MongoDB-webbapp i Azure | Microsoft Docs
description: Lär dig att få en Node.js-app att fungera i Azure med anslutning till en Cosmos DB-databas med en MongoDB-anslutningssträng.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8fdad8d8e62365c33b47e67b483c929aaab0083e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38318022"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-web-app-in-azure"></a>Självstudie: Skapa en Node.js- och MongoDB-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en Node.js- och MongoDB-webbapp i Azure App Service på Linux](./containers/tutorial-nodejs-mongodb-app.md).
>

Med Azure Web Apps får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. I den här självstudien visas hur du skapar en Node.js-webbapp i Azure och ansluter den till en MongoDB-databas. När du är klar har du ett MEAN-program (MongoDB, Express, AngularJS och Node.js) som körs i [Azure App Service](app-service-web-overview.md). För enkelhetens skull använder exempelprogrammet [MEAN.js-webbramverket](http://meanjs.org/).

![MEAN.js-app som körs i Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * skapa en MongoDB-databas i Azure
> * ansluta en Node.js-app till MongoDB
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Node.js och NPM](https://nodejs.org/)
1. [Installera Bower](https://bower.io/) (krävs för [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Installera Gulp.js](http://gulpjs.com/) (krävs för [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Installera och kör MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>Testa lokal MongoDB

Öppna terminalfönstret och `cd` till `bin`-katalogen i MongoDB-installationen. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

Kör `mongo` i terminalen för att ansluta till din lokala MongoDB-server.

```bash
mongo
```

Om anslutningen lyckas körs redan MongoDB-databasen. Om inte ska du kontrollera att din lokala MongoDB-databas startar genom att följa stegen i [Installera MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Ofta installeras MongoDB, men du måste ändå starta det genom att köra `mongod`. 

När du har testat MongoDB-databasen skriver du `Ctrl+C` i terminalen. 

## <a name="create-local-nodejs-app"></a>Skapa lokal Node.js-app

I det här steget konfigurerar du det lokala Node.js-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.  

Klona exempellagringsplatsen med följande kommando. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Den här exempellagringsplatsen innehåller en kopia av [MEAN.js-lagringsplatsen](https://github.com/meanjs/mean). Den ändras för att köras på App Service (mer information finns [filen Viktigt](https://github.com/Azure-Samples/meanjs/blob/master/README.md) för MEAN.js-lagringsplatsen).

### <a name="run-the-application"></a>Köra programmet

Kör följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd meanjs
npm install
npm start
```

När appen har lästs in helt ser du något som liknar följande meddelande:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Gå till `http://localhost:3000` i en webbläsare. Klicka på alternativet för att **registrera** på den översta menyn och skapa en testanvändare. 

MEAN.js-exempelprogrammet lagrar användardata i databasen. Om du lyckas skapa en användare och logga in skriver appen data till den lokala MongoDB-databasen.

![MEAN.js ansluter till MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Välj **Administratör > Hantera artiklar** för att lägga till några artiklar.

Du kan när som helst stoppa Node.js genom att trycka på `Ctrl+C` i terminalen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Skapa produktions-MongoDB

I det här steget skapar du en MongoDB-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För MongoDB använder den här självstudien [Azure Cosmos DB](/azure/documentdb/). Cosmos DB stöder MongoDB-klientanslutningar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto

Skapa ett Cosmos DB-konto i Cloud Shell med kommandot [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create).

I följande kommando ersätter du platshållaren *\<cosmosdb_name>* med ett unikt Cosmos DB-namn. Det här namnet används som en del av Cosmos DB-slutpunkten `https://<cosmosdb_name>.documents.azure.com/`, så namnet måste vara unikt för alla Cosmos DB-konton i Azure. Namnet får endast innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken långt.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Parametern *--kind MongoDB* aktiverar MongoDB-klientanslutningar.

När Cosmos DB-kontot har skapats, visar Azure CLI information liknande följande exempel:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Ansluta en app till produktions-MongoDB

I det här steget, ansluter du ditt MEAN.js-exempelprogram till en Cosmos DB-databas som du just skapade med en MongoDB-anslutningssträng. 

### <a name="retrieve-the-database-key"></a>Hämta databasnyckeln

För att ansluta till en Cosmos DB-databas behöver du databasnyckeln. Använd kommandot [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys) i Cloud Shell för att hämta primärnyckeln.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Azure CLI visar information som liknar följande exempel:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Kopiera värdet för `primaryMasterKey`. Du behöver den här informationen i nästa steg.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurera anslutningssträngen i ditt Node.js-program

I din lokala MEAN.js-lagringsplats skapar du en fil som heter _local-production.js_ i mappen _config/env/_. Som standard konfigureras _.gitignore_ för att hålla filen utanför lagringsplatsen. 

Kopiera följande kod till den. Se till att ersätta de två platshållarna *\<cosmosdb_name>* med ditt Cosmos DB-databasnamn och ersätt platshållaren *\<primary_master_key>* med nyckeln som du kopierade i föregående steg.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Alternativet `ssl=true` krävs eftersom [Cosmos DB kräver SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Spara ändringarna.

### <a name="test-the-application-in-production-mode"></a>Testa programmet i produktionsläge 

Kör följande kommando för att minimera och paketera skript för produktionsmiljön. Den här processen genererar de filer som behövs i produktionsmiljön.

```bash
gulp prod
```

Kör följande kommando för att använda anslutningssträngen du konfigurerade i _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` ställer in miljövariabeln som anvisar Node.js att köra i produktionsmiljön.  `node server.js` startar Node.js-servern med `server.js` i roten för lagringsplatsen. Så här läses Node.js-programmet in i Azure. 

När appen är inläst ska du kontrollera att den körs i produktionsmiljön:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Gå till `http://localhost:8443` i en webbläsare. Klicka på alternativet för att **registrera** på den översta menyn och skapa en testanvändare. Om du lyckas skapa en användare och logga in skriver appen data till Cosmos DB-databasen i Azure. 

I terminalen stoppar du Node.js genom att skriva `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Distribuera appen till Azure

I det här steget distribuerar du ditt MongoDB-anslutna Node.js-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurera en miljövariabel

Som standard håller MEAN.js-projektet _config/env/local-production.js_ utanför Git-lagringsplatsen. Så för Azure-webbappen använder du appinställningar för att definiera MongoDB-anslutningssträngen.

Om du vill konfigurera appinställningar använder du kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell. 

I följande exempel konfigureras appinställningen `MONGODB_URI` i Azure-webbappen. Ersätt platshållarna *\<app_name>*, *\<cosmosdb_name>* och *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

I Node.js-koden får du åtkomst till den här appinställningen med `process.env.MONGODB_URI`, precis som för andra miljövariabler. 

På din lokala MEAN.js-lagringsplats öppnar du _config/env/production.js_ (inte _config/env/local-production.js_), som har en specifik konfiguration för produktion-miljö. MEAN.js-standardappen är redan konfigurerade för att använda `MONGODB_URI`-miljövariabeln du har skapat.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

Du kanske märker att distributionsprocessen kör [Gulp](http://gulpjs.com/) efter `npm install`. App Service kör inte Gulp- eller Grunt-uppgifter under distributionen. Den här exempellagringsplatsen har två extra filer i rotkatalogen för detta: 

- _.deployment_ – Den här filen skickar ett meddelande till App Service om att köra `bash deploy.sh` som anpassat distributionsskript.
- _deploy.sh_ – Det anpassade distributionsskriptet. Om du granskar filen ser du att den kör `gulp prod` efter `npm install` och `bower install`. 

Du kan använda den här metoden för att lägga till steg i din Git-baserade distributionen. När som helst när du startar om Azure-webbappen kör inte App Service om dessa automatiserade uppgifter.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen 

Bläddra till den distribuerade webbappen via webbläsaren. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Klicka på alternativet för att **registrera** på den översta menyn och skapa en låtsasanvändare. 

Om du lyckas och appen loggar in automatiskt till den skapade användaren har din MEAN.jsapp i Azure anslutning till MongoDB- databasen (Cosmos DB). 

![MEAN.js-app som körs i Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Välj **Administratör > Hantera artiklar** för att lägga till några artiklar. 

**Grattis!** Du kör en datadriven Node.js-app i Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget ändrar du datamodellen `article` och publicerar din ändring i Azure.

### <a name="update-the-data-model"></a>Uppdatera datamodellen

Öppna _modules/articles/server/models/article.server.model.js_.

I `ArticleSchema` lägger du till `String`-typ med namnet `comment`. När du är klar bör schemakoden se ut så här:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Uppdatera artiklarnas kod

Uppdatera resten av din `articles`-kod för att använda `comment`.

Det finns fem filer som du måste ändra: serverkontrollanten och de fyra klientvyerna. 

Öppna _modules/articles/server/controllers/articles.server.controller.js_.

I funktionen `update` lägger du till en uppgift för `article.comment`. Följande kod visar den slutförda `update`-funktionen:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Öppna _modules/articles/client/views/view-article.client.view.html_.

Strax ovanför den avslutande `</section>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Öppna _modules/articles/client/views/list-articles.client.view.html_.

Strax ovanför den avslutande `</a>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Öppna _modules/articles/client/views/admin/list-articles.client.view.html_.

I elementet `<div class="list-group">` och strax ovanför den avslutande `</a>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Öppna _modules/articles/client/views/admin/form-article.client.view.html_.

Leta reda på elementet `<div class="form-group">` som innehåller skickaknappen som ser ut så här:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Precis ovanför taggen lägger du till ytterligare ett `<div class="form-group">`-element som gör att människor kan redigera fältet `comment`. Det nya elementet bör se ut ungefär så här:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Spara alla ändringar.

Gå till det lokala terminalfönstret och testa dina ändringar i produktionsläget igen.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Gå till `http://localhost:8443` i en webbläsare och kontrollera att du är inloggad.

Välj **Administratör > Hantera artiklar** och lägg sedan till en artikel genom att välja knappen **+**.

Nu ser du den nya textrutan `Comment`.

![Tillagda kommentarsfält till artiklar](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

I terminalen stoppar du Node.js genom att skriva `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I det lokala terminalfönstret sparar du ändringarna i Git och push-överför sedan kodändringarna till Azure.

```bash
git commit -am "added article comment"
git push azure master
```

När `git push` har slutförts kan du gå till Azure-webbappen och prova att använda de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Om du lade till några artiklar tidigare kan du fortfarande se dem. Befintliga data i din Cosmos DB förloras inte. Dina uppdateringar till dataschemat håller dessutom dina befintliga data intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar 

När Node.js-appen körs i Azure App Service kan du skicka konsolloggarna till din terminal. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Starta loggströmningen genom att använda kommandot [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Uppdatera Azure-webbappen i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Nu ser du konsolloggarna som skickas till terminalen.

Stoppa loggströmningen när som helst genom att skriva `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Hantera din Azure-webbapp

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Som standard visar portalen dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * skapa en MongoDB-databas i Azure
> * ansluta en Node.js-app till MongoDB
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"] 
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)

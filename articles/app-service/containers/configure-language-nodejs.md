---
title: Konfigurera Node.js - appar i Azure App Service | Microsoft Docs
description: Lär dig att konfigurera Node.js-appar att fungera i Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850256"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurera en Linux-Node.js-app för Azure App Service

Node.js-appar måste distribueras med alla nödvändiga NPM-beroenden. App Service-distributionsmotorn (Kudu) körs automatiskt `npm install --production` åt dig när du distribuerar en [Git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), eller en [Zip-paketet](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med produktionsprocesser påslaget. Om du distribuerar dina filer med [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), men du behöver ladda upp de nödvändiga paketen manuellt.

Den här guiden innehåller huvudbegrepp och instruktioner för Node.js-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du de [Snabbstart för Node.js](quickstart-nodejs.md) och [Node.js med MongoDB-självstudien](tutorial-nodejs-mongodb-app.md) första.

## <a name="show-nodejs-version"></a>Visa Node.js-version

För att visa den aktuella versionen av Node.js, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Kör följande kommando för att visa alla Node.js-versioner som stöds, den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Ange Node.js-version

Att ställa in din app på en [Node.js-version som stöds](#show-nodejs-version), kör du följande kommando i den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Den här inställningen anger Node.js-version du använder både vid körning och under automatisk paketåterställning i Kudu.

> [!NOTE]
> Konfigurera Node.js-version i projektets `package.json`. Distributionsmotorn körs i en separat behållare som innehåller alla Node.js-versioner som stöds.

## <a name="configure-nodejs-server"></a>Konfigurera Node.js-server

Node.js-behållare som medföljer [PM2](http://pm2.keymetrics.io/), en Processhanteraren för produktion. Du kan konfigurera din app för att starta PM2, eller med NPM eller med ett anpassat kommando.

- [Kör kommando](#run-custom-command)
- [Kör npm start](#run-npm-start)
- [Kör med PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Kör kommando

App Service kan påbörja din app med ett anpassat kommando, t.ex. en körbar fil som *run.sh*. Till exempel för att köra `npm run start:prod`, kör du följande kommando i den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Kör npm start

Starta din app med `npm start`, se bara till en `start` skriptet finns på den *package.json* fil. Exempel:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Att använda en anpassad *package.json* i projektet, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Kör med PM2

Behållaren startas automatiskt din app med PM2 när något av de vanliga Node.js-filerna finns i projektet:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Något av följande [PM2 filer](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* och *ecosystem.config.js*

Du kan också konfigurera en anpassad start-fil med följande filtillägg:

- En *.js* fil
- En [PM2 filen](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) med tillägget *.json*, *. config.js*, *.yaml*, eller *.yml*

Om du vill lägga till en anpassad start-fil, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Felsöka via en fjärranslutning

> [!NOTE]
> Fjärrfelsökning är för närvarande i förhandsversion.

Du kan felsöka dina Node.js-app via fjärranslutning i [Visual Studio Code](https://code.visualstudio.com/) om du konfigurerar den att [kör med PM2](#run-with-pm2), utom när du kör den med hjälp av en *. config.js, *.yml, eller *.yaml*.

I de flesta fall krävs ingen extra konfiguration för din app. Om din app körs med en *process.json* fil (standard eller anpassad), måste den ha en `script` egenskap i JSON-roten. Exempel:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Om du vill konfigurera Visual Studio Code för fjärrfelsökning, installera den [App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Följ instruktionerna på tilläggssidan och logga in på Azure i Visual Studio Code.

Hitta den app som du vill felsöka, högerklicka på den och välj i Azure explorer **Starta fjärrfelsökning**. Klicka på **Ja** att aktivera den för din app. App Service startar en tunnel proxy för dig och bifogar felsökningsprogrammet. Du kan göra begäranden till appen och se felsökningsprogrammet pausa vid break punkter.

När du är klar med felsökning av stoppar felsökningsverktyget genom att välja **Disconnect**. När du uppmanas du på **Ja** att inaktivera fjärrfelsökning. Om du vill inaktivera senare, högerklicka på din app igen i Azure explorer och välj **inaktivera fjärrfelsökning**.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [konfigurera appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) utanför din Appkod. Du kan använda dem med hjälp av Standardmönstret för Node.js. Om du till exempel vill få åtkomst till en appinställning med namnet `NODE_ENV` använder du följande kod:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Run Grunt/Bower/Gulp

Som standard körs Kudu `npm install --production` när den identifierar en Node.js-app har distribuerats. Om din app kräver någon av de populära verktyg för automatisering av till exempel trista, Bower eller Gulp, måste du ange en [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) att köra den.

Om du vill aktivera lagringsplatsen för att köra dessa verktyg du behöver lägga till dem i beroenden i *package.json.* Exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Byt katalog till Lagringsplatsens rot och kör följande kommandon från ett lokalt terminalfönster:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Nu har två extra filer i Lagringsplatsens rot: *.deployment* och *deploy.sh*.

Öppna *deploy.sh* och hitta den `Deployment` avsnittet, som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Det här avsnittet som slutar med körs `npm install --production`. Lägg till avsnitt med exempelkod som du behöver köra verktyget krävs *i slutet* av den `Deployment` avsnittet:

- [Bower](#bower)
- [Gulp](#gulp)
- [Trista](#grunt)

Se en [exempel i exemplet MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), där distributionsskriptet också körs en anpassad `npm install` kommando.

### <a name="bower"></a>Bower

Det här kodfragmentet körs `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Det här kodfragmentet körs `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodfragmentet körs `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [Express](https://expressjs.com/), du kan använda [förtroende proxyservrar](http://expressjs.com/guide/behind-proxies.html). Exempel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

När en fungerande Node.js-app fungerar annorlunda i App Service eller innehåller fel, provar du följande:

- [Få åtkomst till loggström](#access-diagnostic-logs).
- Testa appen lokalt i Produktionsläge. App Service kör Node.js-appar i Produktionsläge, så du måste se till att projektet fungerar som förväntat i Produktionsläge lokalt. Exempel:
    - Beroende på din *package.json*, olika paket kan installeras för Produktionsläge (`dependencies` jämfört med `devDependencies`).
    - Vissa webbramverk kan distribuera statiska filer på olika sätt i Produktionsläge.
    - Vissa webbramverk kan använda anpassade startskript vid körning i Produktionsläge.
- Kör din app i App Service i utvecklingsläge. Till exempel i [MEAN.js](http://meanjs.org/), du kan ställa in din app till utvecklingsläge under körning av [inställningen den `NODE_ENV` appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Node.js-app med MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux vanliga frågor och svar](app-service-linux-faq.md)
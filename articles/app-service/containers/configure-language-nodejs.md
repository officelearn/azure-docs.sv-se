---
title: Konfigurera Node.js-appar
description: Lär dig hur du konfigurerar en förbyggd Node.js-behållare för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252732"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurera en Linux Node.js-app för Azure App Service

Node.js-appar måste distribueras med alla nödvändiga NPM-beroenden. Distributionsmotorn för App Service (Kudu) körs `npm install --production` automatiskt åt dig när du distribuerar en [Git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)eller ett [Zip-paket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med byggprocesser påslagna. Om du distribuerar dina filer med [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)måste du dock ladda upp de nödvändiga paketen manuellt.

Den här guiden innehåller viktiga begrepp och instruktioner för Node.js-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du [snabbstarten Node.js](quickstart-nodejs.md) och [Node.js med MongoDB-självstudien](tutorial-nodejs-mongodb-app.md) först.

## <a name="show-nodejs-version"></a>Visa nod.js-version

Om du vill visa den aktuella nod.js-versionen kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla nod.js-versioner som stöds kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Ange nod.js-version

Om du vill ange appen till en [Node.js-version som stöds](#show-nodejs-version)kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Den här inställningen anger den nod.js-version som ska användas, både vid körning och under automatisk paketåterställning i Kudu.

> [!NOTE]
> Du bör ange nod.js-versionen i `package.json`projektets . Distributionsmotorn körs i en separat behållare som innehåller alla nod.js-versioner som stöds.

## <a name="customize-build-automation"></a>Anpassa byggautomation

Om du distribuerar din app med Git- eller zip-paket med build automation aktiverat, steg apptjänstens automatisering genom följande sekvens:

1. Kör anpassat skript om `PRE_BUILD_SCRIPT_PATH`det anges av .
1. Kör `npm install` utan flaggor, som `preinstall` innehåller `postinstall` npm och `devDependencies`skript och även installerar .
1. Kör `npm run build` om ett byggskript anges i *package.json*.
1. Kör `npm run build:azure` om ett build:azure-skript anges i *package.json*.
1. Kör anpassat skript om `POST_BUILD_SCRIPT_PATH`det anges av .

> [!NOTE]
> Som beskrivs i [npm docs](https://docs.npmjs.com/misc/scripts), skript som namnges `prebuild` och `postbuild` körs före och efter `build`, respektive, om det anges. `preinstall`och `postinstall` köra före `install`och efter, respektive.

`PRE_BUILD_COMMAND`och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Om du vill köra förbyggningskommandon definierar du `PRE_BUILD_COMMAND`. Om du vill köra kommandon `POST_BUILD_COMMAND`efter build definierar du .

I följande exempel anges de två variablerna till en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa byggautomatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service körs och bygger Node.js-appar i Linux finns i [Oryx-dokumentation: Hur Node.js-appar identifieras och skapas](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurera nod.js-server

Node.js-behållarna levereras med [PM2](https://pm2.keymetrics.io/), en produktionsprocesshanterare. Du kan konfigurera appen så att den börjar med PM2, med NPM eller med ett anpassat kommando.

- [Kör anpassat kommando](#run-custom-command)
- [Kör npm start](#run-npm-start)
- [Kör med PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Kör anpassat kommando

Apptjänsten kan starta appen med ett anpassat kommando, till exempel en körbar som *run.sh*. Om du till `npm run start:prod`exempel vill köra kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Kör npm start

Om du vill `npm start`starta appen `start` med kontrollerar du bara att ett skript finns i *filen package.json.* Ett exempel:

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

Om du vill använda ett anpassat *package.json* i projektet kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Kör med PM2

Behållaren startar automatiskt din app med PM2 när en av de vanliga Node.js-filerna finns i projektet:

- *bin/www*
- *server.js*
- *app.js (på andra)*
- *index.js*
- *hostingstart.js*
- En av följande [PM2 filer:](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *process.json* och *ecosystem.config.js*

Du kan också konfigurera en anpassad startfil med följande tillägg:

- En *.js-fil*
- En [PM2-fil](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) med tillägget *.json*, *.config.js*, *.yaml*eller *.yml*

Om du vill lägga till en anpassad startfil kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Felsöka via en fjärranslutning

> [!NOTE]
> Fjärrfelsökning finns för närvarande i förhandsversion.

Du kan felsöka nod.js-appen på distans i [Visual Studio-kod](https://code.visualstudio.com/) om du konfigurerar den så att [den körs med PM2,](#run-with-pm2)förutom när du kör den med en *.config.js, *.yml eller *.yaml*.

I de flesta fall krävs ingen extra konfiguration för din app. Om din app körs med en *process.json-fil* (standard `script` eller anpassad) måste den ha en egenskap i JSON-roten. Ett exempel:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Om du vill konfigurera Visual Studio-kod för fjärrfelsökning installerar du [tillägget App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Följ instruktionerna på tilläggssidan och logga in på Azure i Visual Studio Code.

Leta reda på den app som du vill felsöka i Utforskaren i Azure, högerklicka på den och välj **Starta fjärrfelsökning**. Klicka på **Ja** om du vill aktivera den för appen. App Service startar en tunnel proxy för dig och bifogar felsökaren. Du kan sedan göra förfrågningar till appen och se felsökningsfelet pausa vid brytpunkter.

När du är klar med felsökningen stoppar du felsökningen genom att välja **Koppla från**. När du uppmanas till det bör du klicka på **Ja** för att inaktivera fjärrfelsökning. Om du vill inaktivera den senare högerklickar du på appen igen i Utforskaren i Azure och väljer **Inaktivera fjärrfelsökning**.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ställa in appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appkoden. Sedan kan du komma åt dem med hjälp av standard nod.js mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `NODE_ENV` använder du följande kod:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Kör Grunt / Bower / Gulp

Som standard körs `npm install --production` Kudu när den känner igen en Node.js-app distribueras. Om din app kräver något av de populära automatiseringsverktygen, till exempel Grunt, Bower eller Gulp, måste du ange ett [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) för att köra det.

Om du vill att databasen ska kunna köra dessa verktyg måste du lägga till dem i beroendena i *package.json.* Ett exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Från ett lokalt terminalfönster ändrar du katalogen till databasroten och kör följande kommandon:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Databasroten har nu ytterligare två filer: *.deployment* och *deploy.sh*.

Öppna *deploy.sh* och hitta `Deployment` avsnittet, som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Det här avsnittet `npm install --production`slutar med att köra . Lägg till kodavsnittet du behöver för att `Deployment` köra det verktyg som krävs i *slutet* av avsnittet:

- [Bower](#bower)
- [Klunk](#gulp)
- [Grunt](#grunt)

Se ett [exempel i MEAN.js-exemplet](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), där `npm install` distributionsskriptet också kör ett anpassat kommando.

### <a name="bower"></a>Bower

Det här kodavsnittet körs `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Klunk

Det här kodavsnittet körs `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodavsnittet körs `grunt`.

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

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [Express](https://expressjs.com/)kan du använda [förtroende proxyservrar](https://expressjs.com/guide/behind-proxies.html). Ett exempel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

När en fungerande Node.js-app fungerar annorlunda i App Service eller har fel provar du följande:

- [Öppna loggströmmen](#access-diagnostic-logs).
- Testa appen lokalt i produktionsläge. App Service kör nod.js-apparna i produktionsläge, så du måste se till att projektet fungerar som förväntat i produktionsläge lokalt. Ett exempel:
    - Beroende på ditt *package.json*kan olika paket installeras`dependencies` för `devDependencies`produktionsläge ( vs. ).
    - Vissa webbramverk kan distribuera statiska filer på olika sätt i produktionsläge.
    - Vissa webbramverk kan använda anpassade startskript när de körs i produktionsläge.
- Kör appen i apptjänsten i utvecklingsläge. I [MEAN.js](https://meanjs.org/)kan du till exempel ställa in appen på utvecklingsläge under körning genom [att ange appinställningen `NODE_ENV` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Appen Node.js med MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)

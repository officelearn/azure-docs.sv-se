---
title: Konfigurera Windows Node.js-appar
description: Lär dig hur du konfigurerar en Node.js-app i de interna Windows-instanserna av App Service. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908144"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Konfigurera en Windows Node.js-app för Azure App Service

Node.js appar måste distribueras med alla nödvändiga NPM-beroenden. App Service distributions motorn körs automatiskt `npm install --production` åt dig när du distribuerar en [git-lagringsplats](deploy-local-git.md)eller ett [zip-paket](deploy-zip.md) med build-automatisering aktiverat. Om du distribuerar dina filer med [FTP/S](deploy-ftp.md)måste du dock ladda upp de nödvändiga paketen manuellt. Information om Linux-appar finns i [Konfigurera en Linux php-app för Azure App Service](containers/configure-language-nodejs.md).

Den här guiden innehåller viktiga begrepp och instruktioner för Node.js utvecklare som distribuerar till App Service. Om du aldrig har använt Azure App Service följer du själv studie kursen [Node.js snabb start](app-service-web-get-started-nodejs.md) och [Node.js med MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Visa Node.js version

Om du vill visa den aktuella Node.js versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Om du vill visa alla Node.js-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Ange Node.js version

Om du vill ställa in din app på en [Node.js-version som stöds](#show-nodejs-version)kör du följande kommando i [Cloud Shell](https://shell.azure.com) för att ange `WEBSITE_NODE_DEFAULT_VERSION` en version som stöds:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Den här inställningen anger vilken Node.js-version som ska användas, både vid körning och under automatisk återställning av paketet under App Service skapa automatisering.

> [!NOTE]
> Du bör ange Node.js versionen i projektets `package.json` . Distributions motorn körs i en separat process som innehåller alla Node.js-versioner som stöds.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](configure-common.md) utanför appens kod. Sedan kan du komma åt dem med standard Node.jss mönstret. Om du till exempel vill få åtkomst till en appinställning med namnet `NODE_ENV` använder du följande kod:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Kör grunt/Bower/Gulp

App Service Bygg automatisering körs som standard `npm install --production` när den identifierar en Node.js-app distribueras via git (eller zip-distribution med build-automatisering aktiverat). Om din app kräver något av de populära automatiserings verktygen, till exempel grunt, Bower eller Gulp, måste du ange ett [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) för att köra det.

Om du vill göra det möjligt för lagrings platsen att köra dessa verktyg måste du lägga till dem i beroenden i *package.jspå.* Ett exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

I ett lokalt terminalfönster ändrar du katalogen till lagrings platsens rot och kör följande kommandon:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Din databas rot har nu två ytterligare filer: *. distribution* och *Deploy.sh*.

Öppna *Deploy.sh* och hitta `Deployment` avsnittet som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Avsnittet slutar med att köras `npm install --production` . Lägg till kod avsnittet du måste köra det nödvändiga verktyget *i slutet* av `Deployment` avsnittet:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Se ett [exempel i MEAN.js-exemplet](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)där distributions skriptet också kör ett anpassat `npm install` kommando.

### <a name="bower"></a>Bower

Det här kodfragmentet körs `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Det här kodfragmentet körs `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodfragmentet körs `grunt` .

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

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [Express](https://expressjs.com/)kan du använda [betrodda proxyservrar](https://expressjs.com/guide/behind-proxies.html). Ett exempel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

Prova följande när en fungerande Node.js-app fungerar annorlunda i App Service eller innehåller fel:

- [Åtkomst till logg strömmen](#access-diagnostic-logs).
- Testa appen lokalt i produktions läge. App Service kör dina Node.js appar i produktions läge, så du måste se till att projektet fungerar som förväntat i produktions läge lokalt. Ett exempel:
    - Beroende på din *package.js*kan olika paket installeras i produktions läge ( `dependencies` vs. `devDependencies` ).
    - Vissa webb ramverk kan distribuera statiska filer på ett annat sätt i produktions läge.
    - Vissa webb ramverk kan använda anpassade Start skript när de körs i produktions läge.
- Kör din app i App Service i utvecklings läge. I [MEAN.js](https://meanjs.org/)kan du till exempel ställa in appen i utvecklings läge i körningen genom [att ställa in `NODE_ENV` appens inställning](configure-common.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Node.js app med MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)


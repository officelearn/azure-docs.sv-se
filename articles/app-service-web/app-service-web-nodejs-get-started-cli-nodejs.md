---
title: "Kom igång med Node.js-webbappar i Azure App Service | Microsoft Docs"
description: "Lär dig hur du distribuerar ett Node.js-program till en webbapp i Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9be7aec6d66c625be83b93bb840ad137d3c786d2
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Kom igång med Node.js-webbappar i Azure App Service
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen får du lära dig hur du skapar ett enkelt [Node.js]-program och distribuerar det till [Azure App Service] från en kommandoradmiljö, t.ex. cmd.exe eller bash. Anvisningarna i den här kursen kan användas för alla operativsystem som kan köra Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering
- [Azure CLI 2.0](app-service-web-nodejs-get-started.md) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="prerequisites"></a>Krav
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Azure CLI]
* Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion] eller [aktivera Visual Studio-prenumerantförmåner].

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Skapa och distribuera en enkel Node.js-webbapp
1. Öppna önskat kommandoradsverktyg och installera [Express generator for Yeoman].
   
        npm install -g generator-express
2. `CD` till en arbetskatalog och skapa en expressapp med följande syntax:
   
        yo express
   
    Välj följande alternativ när du uppmanas till detta:  
   
    `? Would you like to create a new directory for your project?` **Ja**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **Ingen**  
    `? Select a database to use:` **Ingen**  
    `? Select a build tool to use:` **Grunt**
3. `CD` till rotkatalogen för din nya app och starta den för att kontrollera att den kan köras i utvecklingsmiljön:
   
        npm start
   
    Gå till <http://localhost:3000> i webbläsaren och kontrollera att du kan se Express-startsidan. När du har bekräftat att appen körs korrekt kan du använda `Ctrl-C` för att stoppa den.
4. Ändra till ASM-läge och logga in på Azure (du behöver [Azure CLI](#prereq)):
   
        azure config mode asm
        azure login
   
    Följ anvisningarna för att fortsätta inloggningen i en webbläsare med ett Microsoft-konto som innehåller din Azure-prenumeration.


3. Ange distributionsanvändare för App Service. Du distribuerar kod med autentiseringsuppgifterna senare.
   
        azure site deployment user set --username <username> --pass <password>

5. Kontrollera att du fortfarande är i appens rotkatalog, och skapa sedan App Service-appresursen i Azure med ett unikt appnamn med nästa kommando. Till exempel: http://{appname}.azurewebsites.net
   
        azure site create --git {appname}
   
    Följ anvisningarna för att välja en Azure-region för distribution. 
6. Öppna filen ./config/config.js från roten för ditt program och ändra produktionsporten till `process.env.port`; din `production`-egenskap i `config`-objektet ska se ut som i följande exempel:
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > Som standard kör Azure App Service Node.js-program med `production`miljövariabler (`process.env.NODE_ENV="production"`.
    > Därför låter din konfigurering här din Node.js-app i Azure svara på webbförfrågningar på den standardport där iisnode lyssnar.
    >
    >

7. Öppna ./package.json och lägg till egenskapen `engines` för att [ange önskad Node.js-version](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 
8. Spara ändringarna och använd sedan git för att distribuera din app till Azure. När du uppmanas använder du de autentiseringsuppgifter som du skapade tidigare.
   
        git add .
        git add -f config
        git commit -m "{your commit message}"
        git push azure master
   
    Express generator tillhandahåller redan en .gitignore-fil, så `git push` förbrukar inte bandbredd med att försöka ladda upp katalogen node_modules/.
9. Starta till sist din aktiva Azure-app i webbläsaren:
   
        azure site browse
   
    Nu bör du kunna se att din Node.js-webbapp körs live i Azure App Service.
   
    ![Exempel på hur du bläddrar till det distribuerade programmet.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Uppdatera Node.js-webbappen
Om du vill uppdatera Node.js-webbappen som körs i App Service, kör du bara `git add`, `git commit` och `git push` precis som du gjorde när du distribuerade webbappen för första gången.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Så här distribuerar App Service Node.js-appen
Azure App Service använder [iisnode] för att köra Node.js-appar. Azure CLI och Kudu-motorn (Git-distribution) arbetar tillsammans för att göra det enklare när du utvecklar och distribuerar Node.js-appar från kommandoraden. 

* `azure site create --git` känner igen det vanliga Node.js-mönstret i server.js eller app.js och skapar iisnode.yml i rotkatalogen. Du kan använda den här filen för att anpassa iisnode.
* Vid `git push azure master` automatiserar Kudu följande distributionsåtgärder:
  
  * Om package.json är i lagringsplatsens rot kör du `npm install --production`.
  * Skapa en Web.config för iisnode som pekar på startskriptet i package.json (t.ex. server.js eller app.js).
  * Anpassa Web.config för att förbereda appen för felsökning med Node-Inspector.

## <a name="use-a-nodejs-framework"></a>Använda ett Node.js-ramverk
Om du använder ett populärt Node.js-ramverk, t.ex. [Sails.js][SAILSJS] eller [MEAN.js][MEANJS] för att utveckla appar kan du distribuera dessa till App Service. Populära Node.js-ramverk har specifika egenskaper, och deras paketberoenden uppdateras hela tiden. Men App Service tillgängliggör stdout- och stderr-loggarna så att du kan veta exakt vad som händer med din app och göra ändringar därefter. Mer information finns i [Hämta stdout- och stderr-loggar från iisnode](#iisnodelog).

Följande kurser visar hur du arbetar med ett specifikt ramverk i App Service:

* [Distribuera en Sails.js-webbapp till Azure App Service]
* [Skapa ett Node.js-chattprogram med Socket.IO i Azure App Service]
* [Använda io.js med Azure App Service Web Apps]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Använda en specifik Node.js-motor
I ditt vanliga arbetsflöde kan du ange att App Service ska använda en specifik Node.js-motor som du vanligtvis använder i package.json.
Exempel:

    "engines": {
        "node": "6.9.1"
    }, 

Kudu-distributionsmotorn avgör vilken Node.js-motor som ska användas i följande ordning:

* Kontrollera först i iisnode.yml om `nodeProcessCommandLine` har angetts. Om det har angetts kan du använda det.
* Kontrollera sedan i package.json om `"node": "..."` har angetts i objektet `engines`. Om det har angetts kan du använda det.
* Välj en Node.js-standardversion som standard.

För den uppdaterade listan med alla Node.js/NPM-versioner i Azure App Service som stöds, använd följande URL för din app:

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> Vi rekommenderar att du explicit definierar den Node.js-motor du önskar. Node.js-standardversionen kan ändras, och du kan stöta på fel i Azure-webbappen eftersom Node.js-standardversionen inte är lämplig för din app.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Hämta stdout- och stderr-loggar från iisnode
Utför dessa steg om du vill läsa iisnode-loggar.

> [!NOTE]
> När du har slutfört de här stegen finns eventuellt inte några loggfiler förrän ett fel inträffar.
> 
> 

1. Öppna filen iisnode.yml som Azure CLI tillhandahåller.
2. Ange följande två parametrar: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Tillsammans anger de att iisnode i App Service ska lägga stdout- och stderror-utdata i katalogen D:\home\site\wwwroot\**iisnode*.*
3. Spara ändringarna och push-överför sedan ändringarna till Azure med följande Git-kommandon:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode har nu konfigurerats. Nästa steg visar hur du kommer åt de här loggarna.
4. Gå till webbläsaren och öppna Kudu-felsökningskonsolen för din app som finns här:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Den här URL:en skiljer sig från webbappens URL eftersom *.scm.* har lagts till i DNS-namnet. Om du inte tar med tillägget i URL:en får du ett 404-fel.
5. Gå till D:\home\site\wwwroot\iisnode.
   
    ![Gå till platsen för iisnode-loggfilerna.][iislog-kudu-console-find]
6. Klicka på **redigeringsikonen** för den logg som du vill läsa. Du kan även klicka på **Hämta** eller **Ta bort** om du vill.
   
    ![Öppna en iisnode-loggfil.][iislog-kudu-console-open]
   
    Nu kan du se loggen som du kan använda till hjälp för att felsöka App Service-distributionen.
   
    ![Studera en iisnode-loggfil.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Felsöka appen med Node-Inspector
Om du använder Node-Inspector för att felsöka dina Node.js-appar, kan du använda det för din aktiva App Service-app. Node-Inspector förinstalleras i iisnode-installationen för App Service. Om du distribuerar via Git innehåller den automatiskt skapade Web.config från Kudu redan all konfiguration du behöver för att aktivera Node-Inspector.

Följ de här stegen om du vill aktivera Node-Inspector:

1. Öppna iisnode.yml i lagringsplatsens rot och ange följande parametrar: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Spara ändringarna och push-överför sedan ändringarna till Azure med följande Git-kommandon:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Nu behöver du bara gå till appens startfil som anges i startskriptet i package.json och lägga till /debug i URL:en. Exempel:
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    Eller:
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Fler resurser
* [Ange en Node.js-version i ett Azure-program](../nodejs-specify-node-version-azure-apps.md)
* [Bästa praxis och felsökningsguid för Node.js-program på Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Felsöka en Node.js-webbapp i Azure App Service](web-sites-nodejs-debug.md)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Node.js Developer Center](/develop/nodejs/)
* [Kom igång med Web Apps i Azure App Service](app-service-web-get-started.md)
* [Utforska superhemliga Kudu-felsökningskonsolen]

<!-- URL List -->

[Azure CLI]:../cli-install-nodejs.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[aktivera Visual Studio-prenumerantförmåner]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Skapa ett Node.js-chattprogram med Socket.IO i Azure App Service]: ./web-sites-nodejs-chat-app-socketio.md
[Distribuera en Sails.js-webbapp till Azure App Service]: ./app-service-web-nodejs-sails.md
[Utforska superhemliga Kudu-felsökningskonsolen]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express generator for Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Använda io.js med Azure App Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[registrera dig för en kostnadsfri utvärderingsversion]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png


<properties
    pageTitle="Kom igång med Node.js-webbappar i Azure Apptjänst"
    description="Lär dig hur du distribuerar ett Node.js-program till en webbapp i Azure Apptjänst."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# Kom igång med Node.js-webbappar i Azure Apptjänst

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen får du lära dig hur du skapar ett enkelt [Node.js][NODEJS]-program och distribuerar det till en [webbapp] i [Azure Apptjänst] från en kommandoradsmiljö, t.ex. cmd.exe eller bash. Anvisningarna i den här kursen kan användas för alla operativsystem som kan köra Node.js.

<a name="prereq"></a>
## Krav

- **Node.js** ([Klicka här för att installera][NODEJS])
- **Bower** ([Klicka här för att installera][BOWER])
- **Yeoman** ([Klicka här för att installera][YEOMAN])
- **Git** ([Klicka här för att installera][GIT])
- **Azure CLI** ([Klicka här för att installera][Azure CLI])
- Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion] eller [aktivera Visual Studio-prenumerantförmåner].

## Skapa och distribuera en enkel Node.js-webbapp

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
    
1. Ändra till ASM-läge och logga in på Azure (du behöver [Azure CLI](#prereq) för detta):

        azure config mode asm
        azure login

    Följ anvisningarna för att fortsätta inloggningen i en webbläsare med ett Microsoft-konto som innehåller din Azure-prenumeration.

2. Kontrollera att du fortfarande är i appens rotkatalog, och skapa sedan Apptjänst-appresursen i Azure med ett unikt appnamn med nästa kommando, t.ex. http://{appname}.azurewebsites.net.

        azure site create --git {appname}

    Följ anvisningarna för att välja en Azure-region för distribution. Om du aldrig har konfigurerat autentiseringsuppgifter för Git/FTP-distribution i din Azure-prenumeration uppmanas du även att skapa sådana.

3. Öppna filen ./config/config.js från roten för ditt program och ändra produktionsporten till `process.env.port`; din `production`-egenskap i `config`-objektet ska se ut som i följande exempel.

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Då kan din Node.js-app svara på webbförfrågningar på den standardport där iisnode lyssnar.
    
4. Spara ändringarna och använd sedan git för att distribuera din app till Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Express generator tillhandahåller redan en .gitignore-fil, så `git push` förbrukar inte bandbredd med att försöka ladda upp katalogen node_modules/.

5. Starta till sist din aktiva Azure-app i webbläsaren:

        azure site browse

    Nu bör du kunna se att din Node.js-webbapp körs live i Azure Apptjänst.
    
    ![Exempel på hur du bläddrar till det distribuerade programmet.][deployed-express-app]

## Uppdatera Node.js-webbappen

Om du vill uppdatera Node.js-webbappen som körs i Apptjänst, kör du bara `git add`, `git commit` och `git push` precis som du gjorde när du distribuerade webbappen för första gången.
     
## Så här distribuerar Apptjänst Node.js-appen

Azure Apptjänst använder [iisnode] för att köra Node.js-appar. Azure CLI och Kudu-motorn (Git-distribution) arbetar tillsammans för att göra det enklare när du utvecklar och distribuerar Node.js-appar från kommandoraden. 

- `azure site create --git` känner igen det vanliga Node.js-mönstret i server.js eller app.js och skapar iisnode.yml i rotkatalogen. Du kan använda den här filen för att anpassa iisnode.
- Vid `git push azure master` automatiserar Kudu följande distributionsåtgärder:

    - Om package.json är i lagringsplatsens rot kör du `npm install --production`.
    - Skapa en Web.config för iisnode som pekar på startskriptet i package.json (t.ex. server.js eller app.js).
    - Anpassa Web.config för att förbereda appen för felsökning med Node-Inspector.
    
## Använda ett Node.js-ramverk

Om du använder ett populärt Node.js-ramverk, t.ex. [Sails.js][SAILSJS] eller [MEAN.js][MEANJS], för att utveckla appar kan du distribuera dessa till Apptjänst. Populära Node.js-ramverk har specifika egenskaper, och deras paketberoenden uppdateras hela tiden. Men Apptjänst tillgängliggör stdout- och stderr-loggarna så att du kan veta exakt vad som händer med din app och göra ändringar därefter. Mer information finns i [Hämta stdout- och stderr-loggar från iisnode](#iisnodelog).

Följande kurser visar hur du arbetar med ett specifikt ramverk i Apptjänst:

- [Distribuera en Sails.js-webbapp till Azure Apptjänst]
- [Skapa ett Node.js-chattprogram med Socket.IO i Azure Apptjänst]
- [Använda io.js med Webbappar i Azure Apptjänst]

## Använda en specifik Node.js-motor

I ditt vanliga arbetsflöde kan du ange att Apptjänst ska använda en specifik Node.js-motor som du vanligtvis använder i package.json.
Exempel:

    "engines": {
        "node": "5.5.0"
    }, 

Kudu-distributionsmotorn avgör vilken Node.js-motor som ska användas i följande ordning:

- Kontrollera först i iisnode.yml om `nodeProcessCommandLine` har angetts. Om det har angetts kan du använda det.
- Kontrollera sedan i package.json om `"node": "..."` har angetts i objektet `engines`. Om det har angetts kan du använda det.
- Välj en Node.js-standardversion som standard.

<a name="iisnodelog"></a>
## Hämta stdout- och stderr-loggar från iisnode

Använd följande steg om du vill läsa iisnode-loggar.

> [AZURE.NOTE] När du har slutfört de här stegen finns eventuellt inte några loggfiler förrän ett fel inträffar.

1. Öppna filen iisnode.yml som Azure CLI tillhandahåller.

2. Ange följande två parametrar: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Tillsammans anger de att iisnode i Apptjänst ska lägga stdout- och stderror-utdata i katalogen D:\home\site\wwwroot\**iisnode*.*

3. Spara ändringarna och push-överför sedan ändringarna till Azure med följande Git-kommandon:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
   iisnode har nu konfigurerats. Nästa steg visar hur du kommer åt de här loggarna.
     
4. Gå till webbläsaren och öppna Kudu-felsökningskonsolen för din app som finns här:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Observera att den här URL:en skiljer sig från webbappens URL eftersom *.scm.* har lagts till i DNS-namnet. Om du inte tar med tillägget i URL:en får du ett 404-fel.

5. Gå till D:\home\site\wwwroot\iisnode.

    ![Gå till platsen för iisnode-loggfilerna.][iislog-kudu-console-find]

6. Klicka på **redigeringsikonen** för den logg som du vill läsa. Du kan även klicka på **Hämta** eller **Ta bort** om du vill.

    ![Öppna en iisnode-loggfil.][iislog-kudu-console-open]

    Nu kan du se loggen som du kan använda till hjälp för att felsöka Apptjänst-distributionen.
    
    ![Studera en iisnode-loggfil.][iislog-kudu-console-read]

## Felsöka appen med Node-Inspector

Om du använder Node-Inspector för att felsöka dina Node.js-appar, kan du använda det för din aktiva Apptjänst-app. Node-Inspector förinstalleras i iisnode-installationen för Apptjänst. Om du distribuerar via Git innehåller den automatiskt skapade Web.config från Kudu redan all konfiguration du behöver för att aktivera Node-Inspector.

Följ de här stegen om du vill aktivera Node-Inspector:

1. Öppna iisnode.yml i lagringsplatsens rot och ange följande parametrar: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Spara ändringarna och push-överför sedan ändringarna till Azure med följande Git-kommandon:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Nu behöver du bara gå till appens startfil som anges i startskriptet i package.json och lägga till /debug i URL:en. Exempel:

        http://{appname}.azurewebsites.net/server.js/debug
    
    Eller:
    
        http://{appname}.azurewebsites.net/app.js/debug

## Fler resurser

- [Ange en Node.js-version i ett Azure-program](../nodejs-specify-node-version-azure-apps.md)
- [Bästa praxis och felsökningsguide för Node.js-program om Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Felsöka en Node.js-webbapp i Azure Apptjänst](web-sites-nodejs-debug.md)
- [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
- [Webbappar i Azure Apptjänst: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js Developer Center](/develop/nodejs/)
- [Kom igång med webbappar i Azure Apptjänst](app-service-web-get-started.md)
- [Utforska superhemliga Kudu-felsökningskonsolen]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure Apptjänst]: ../app-service/app-service-value-prop-what-is.md
[aktivera Visual Studio-prenumerantförmåner]: http://go.microsoft.com/fwlink/?LinkId=623901
[BOWER]: http://bower.io/
[Skapa ett Node.js-chattprogram med Socket.IO i Azure Apptjänst]: ./web-sites-nodejs-chat-app-socketio.md
[Distribuera en Sails.js-webbapp till Azure Apptjänst]: ./app-service-web-nodejs-sails.md
[Utforska superhemliga Kudu-felsökningskonsolen]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express generator for Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[Använda io.js med Webbappar i Azure Apptjänst]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[NODEJS]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[registrera dig för en kostnadsfri utvärderingsversion]: http://go.microsoft.com/fwlink/?LinkId=623901
[webbapp]: ./app-service-web-overview.md
[YEOMAN]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Sep16_HO3-->



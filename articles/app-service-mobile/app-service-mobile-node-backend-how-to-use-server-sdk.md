---
title: "Hur du arbetar med serverdelen Node.js SDK för Mobile Apps | Microsoft Docs"
description: "Lär dig hur du arbetar med serverdelen Node.js SDK för Azure Apptjänst Mobilappar."
services: app-service\mobile
documentationcenter: 
author: elamalani
manager: elamalani
editor: 
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 336da28bea7de313bced97e447fc6b7b1fb1390d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Hur du använder Azure Mobile Apps Node.js SDK
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Den här artikeln innehåller detaljerad information och exempel som visar hur du arbetar med en Node.js-serverdel i Azure Apptjänst Mobilappar.

## <a name="Introduction"></a>Introduktion
Azure Apptjänst Mobilappar ger möjlighet att lägga till en-optimerad dataåtkomst Web API till ett webbprogram.  Azure App Service Mobile Apps-SDK har angetts för ASP.NET och Node.js webbprogram.  SDK innehåller följande åtgärder:

* Tabellåtgärder (Läs-, Insert-, Update-, Delete) för dataåtkomst
* Anpassade API-åtgärder

Både operations erbjuda autentisering över alla identitetsleverantörer som tillåts av Azure App Service, inklusive sociala identitetsleverantörer, till exempel Facebook, Twitter, Google och Microsoft samt Azure Active Directory för enterprise-identitet.

Du kan hitta exempel för varje användningsfall som i den [katalogen samples på GitHub].

## <a name="supported-platforms"></a>Plattformar som stöds
Azure Mobile Apps nod SDK har stöd för den aktuella LTS-versionen av noden och senare.  Från och med skrivning är den senaste versionen av LTS nod v4.5.0.  Andra versioner av noden fungerar men stöds inte.

Azure Mobile Apps nod SDK stöder två databasdrivrutiner för - nod mssql-drivrutinen har stöd för SQL Azure och lokala SQL Server-instanser.  Sqlite3-drivrutinen stöder SQLite databaser på en enda instans.

### <a name="howto-cmdline-basicapp"></a>Så här: skapa ett grundläggande Node.js-serverdel med hjälp av kommandoraden
Varje Azure App Service Mobile App Node.js-serverdel startar som ett ExpressJS program.  ExpressJS är det mest populära web service ramverket för Node.js.  Du kan skapa en grundläggande [Express] program på följande sätt:

1. Skapa en katalog för ditt projekt i ett kommando eller PowerShell-fönstret.

        mkdir basicapp
2. Kör npm init för att initiera paketet strukturen.

        cd basicapp
        npm init

    Kommandot npm init frågar en uppsättning frågor att starta projektet.  Se de exempel på utdatan:

    ![Npm init-utdata][0]
3. Installera express och azure mobilappar bibliotek från npm-databasen.

        npm install --save express azure-mobile-apps
4. Skapa en app.js-fil om du vill implementera grundläggande mobila server.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Det här programmet skapar en optimerad för-WebAPI med en enda slutpunkt (`/tables/TodoItem`) som ger obehörig åtkomst till en underliggande SQL-datalagret med en dynamisk schema.  Det är lämpligt för följande snabbstarter för klient-biblioteket:

* [Snabbstart för Android-klienten]
* [Snabbstart för Apache Cordova-klient]
* [iOS klienten Snabbstart]
* [Snabbstart för Windows Store-klienten]
* [Snabbstart för Xamarin.iOS-klient]
* [Snabbstart för Xamarin.Android-klient]
* [Snabbstart för Xamarin.Forms-klient]

Du hittar koden för den här grundläggande program i den [basicapp exempel på GitHub].

### <a name="howto-vs2015-basicapp"></a>Så här: skapa en nod serverdel med Visual Studio 2015
Visual Studio 2015 kräver ett tillägg till utvecklar Node.js-program i IDE.  Starta genom att installera den [1.1 för Node.js-verktyg för Visual Studio].  När Node.js-verktyg för Visual Studio har installerats kan du skapa ett Express 4.x program:

1. Öppna den **nytt projekt** dialogrutan (från **filen** > **ny** > **projekt...** ).
2. Expandera **mallar** > **JavaScript** > **Node.js**.
3. Välj den **grundläggande Azure Node.js Express 4 programmet**.
4. Fyll i projektets namn.  Klicka på *OK*

    ![Nytt projekt i Visual Studio 2015][1]
5. Högerklicka på den **npm** och välj **installera nya npm-paket...** .
6. Du kan behöva uppdatera npm-katalogen för att skapa din första Node.js-program.  Klicka på **uppdatera** om det behövs.
7. Ange *azure mobilappar* i sökrutan.  Klicka på den **azure mobile apps 2.0.0** paketet och klicka sedan på **installera paket**.

    ![Installera nya npm-paket][2]
8. Klicka på **Stäng**.
9. Öppna den *app.js* filen att ge stöd för Azure Mobile Apps-SDK.  Kräver uttryck på rad 6 at längst ned i biblioteket, Lägg till följande kod:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    På cirka rad 27 efter andra app.use-instruktioner, lägger du till följande kod:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Spara filen.
10. Kör programmet lokalt (API: N hanteras på http://localhost: 3000) eller publicera till Azure.

### <a name="create-node-backend-portal"></a>Så här: skapa en Node.js-serverdel i Azure Portal
Du kan skapa en Mobilapp serverdel direkt i den [Azure-portalen]. Du kan följa anvisningarna nedan eller skapa en klient och server tillsammans genom att följa den [skapar en mobil app](app-service-mobile-ios-get-started.md) kursen. Vägledningen innehåller en förenklad version av dessa anvisningar och är bäst för bevis på koncept projekt.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

I den *Kom igång* bladet under **skapa en tabell API**, Välj **Node.js** som din **språk för serverdel**.
Markera kryssrutan för ”**bekräftar jag att skrivs alla plats innehållet.**”, klicka på **skapa TodoItem-tabell**.

### <a name="download-quickstart"></a>Så här: ladda ned Node.js quickstart kod serverdelsprojektet med Git
När du skapar en Node.js-mobilappsserverdel med hjälp av portalen **Snabbstart** bladet ett Node.js-projekt skapas för dig och distribueras till din webbplats. Du kan lägga till tabeller och API: er och redigera kodfiler för Node.js-serverdel i portalen. Du kan också använda olika distributionsverktyg för att hämta serverdelsprojektet så att du kan lägga till eller ändra tabeller och API: er och sedan publicera projektet. Mer information finns i [Azure App Service Deployment Guide]. följande procedur används en Git-lagringsplats för att hämta Projektkod Snabbstart.

1. Installera Git om du inte redan gjort det. Anvisningar för att installera Git variera mellan olika operativsystem. Se [installerar Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) för operativsystemspecifika distributioner och installationer.
2. Följ stegen i [aktivera databasen för Apptjänst-app](../app-service/app-service-deploy-local-git.md#Step3) att aktivera Git-lagringsplats för backend-platsen, och ett meddelande om distributionen användarnamn och lösenord.
3. I bladet för din mobilappsserverdel anteckna den **URL för Git-klon** inställningen.
4. Köra den `git clone` kommandot med Git klona URL, ange ditt lösenord vid behov, som i följande exempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Bläddra till lokal katalog, som i föregående exempel är /todolist, och Lägg märke till att projektfiler har laddats ned. Leta upp den `todoitem.json` filen i den `/tables` directory.  Den här filen definierar behörigheter i tabellen.  Också hitta den `todoitem.js` fil i samma katalog som definierar CRUD åtgärden skript för tabellen.
6. När du har gjort ändringar i projektfiler, kör du följande kommandon för att lägga till och genomför sedan överföra ändringarna till webbplatsen:

        $ git commit -m "updated the table script"
        $ git push origin master

    När du lägger till nya filer i projektet, måste du först köra den `git add .` kommando.

Varje gång en ny uppsättning incheckningar skickas till platsen publiceras på webbplatsen.

### <a name="howto-publish-to-azure"></a>Så här: publicera din Node.js-serverdel i Azure
Microsoft Azure tillhandahåller många metoder för att publicera dina Azure App Service Mobile Apps Node.js-serverdel till Azure-tjänsten.  Dessa inkluderar använder distributionsverktyg integrerade i Visual Studio, kommandoradsverktyg och kontinuerlig distributionsalternativ baserat på källkontroll.  Mer information om det här avsnittet finns det [Azure App Service Deployment Guide].

Azure Apptjänst har råd för Node.js-program som du bör läsa innan du distribuerar:

* Så här [ange nod-Version]
* Så här [använda noden moduler]

### <a name="howto-enable-homepage"></a>Så här: aktivera en hemsida för ditt program
Många program är en kombination av webb- och mobilappar och ExpressJS framework kan du kombinera de två aspekter.  Ibland kan dock kan du bara implementera ett gränssnitt för mobila.  Det är praktiskt att ge en landningssida för att undvika app-tjänsten är igång.  Du kan ange en egen hemsida eller aktivera en tillfällig startsida.  För att aktivera en tillfällig startsida, använda du följande för att skapa en instans av Azure Mobile Apps:

    var mobile = azureMobileApps({ homePage: true });

Om du vill bara det här alternativet är tillgängligt när du utvecklar lokalt kan du lägga till den här inställningen för att din `azureMobile.js` fil.

## <a name="TableOperations"></a>Åtgärder för tabeller
Node.js Server-SDK för azure mobilappar ger metoder för att visa datatabeller som lagras i Azure SQL Database som en WebAPI.  Fem tillhandahålls.

| Åtgärd | Beskrivning |
| --- | --- |
| GET-/tables/*tablename* |Hämta alla poster i tabellen |
| GET-/tables/*tablename*/:id |Hämta en viss post i tabellen |
| POST /tables/*tablename* |Skapa en post i tabellen |
| KORRIGERING /tables/*tablename*/:id |Uppdatera en post i tabellen |
| Ta bort /tables/*tablename*/:id |Ta bort en post i tabellen |

Har stöd för den här WebAPI [OData] och utökar tabellschemat att stödja [datasynkronisering offline].

### <a name="howto-dynamicschema"></a>Så här: definiera tabeller med en dynamisk schema
Innan en tabell kan användas, måste ha definierats.  Tabeller kan definieras med en statisk schema (där definierar utvecklaren kolumner i schemat) eller dynamiskt (där SDK styr schema baserat på inkommande begäranden). Utvecklaren kan dessutom styra vissa aspekter av WebAPI genom att lägga till Javascript-kod definitionen.

Som bästa praxis bör du definiera varje tabell i en Javascript-fil i katalogen tabeller och sedan använda tables.import()-metoden för att importera tabellerna.  Utöka appen basic skulle filen app.js ställas in:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definiera tabellen i-. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Dynamisk schemat använder som standard.  Ange om du vill inaktivera dynamiska schemat globalt Appinställningen **MS_DynamicSchema** till FALSKT i Azure-portalen.

Du hittar ett komplett exempel i den [todo prov på GitHub].

### <a name="howto-staticschema"></a>Så här: definiera tabeller med en statisk schema
Du kan uttryckligen definiera kolumner att visa via WebAPI.  Azure mobilappar Node.js SDK lägger automatiskt till alla andra kolumner som krävs för datasynkronisering offline i listan som du anger.  Exempelvis klientprogram Snabbstart kräver en tabell med två kolumner: text (en sträng) och slutföra (ett booleskt värde).  
Tabellen kan definieras i tabellen definition JavaScript-filen (som finns i katalogen tabeller) enligt följande:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Om du definierar tabeller statiskt, måste du också anropa metoden tables.initialize() för att skapa databasschemat vid start.  Tables.initialize()-metoden returnerar en [Promise] så att webbtjänsten inte hantera begäranden innan databasen initieras.

### <a name="howto-sqlexpress-setup"></a>Så här: använda SQL Express som en utveckling datalager på den lokala datorn
Azure Mobile Apps i AzureMobile appar nod SDK innehåller tre alternativ för betjänar data ur lådan: SDK innehåller tre alternativ för betjänar data ur lådan:

* Använd den **minne** drivrutinen att tillhandahålla en icke-beständig exempel store
* Använd den **mssql** drivrutinen att tillhandahålla ett dataarkiv som SQL Express för utveckling
* Använd den **mssql** drivrutinen att tillhandahålla en Azure SQL Database-datalagret för produktion

Azure Mobile Apps Node.js SDK använder den [mssql Node.js paketet] upprätta och använda en anslutning till SQL Express och SQL-databas.  Det här paketet kräver att du aktiverar TCP-anslutningar på din SQL Express-instans.

> [!TIP]
> Minne-drivrutinen ger inte en fullständig uppsättning funktioner för testning.  Om du vill testa serverdelen lokalt rekommenderar vi användningen av ett SQL Express datalager och mssql-drivrutinen.
>
>

1. Hämta och installera [Microsoft SQL Server 2014 Express].  Se till att du installerar SQL Server 2014 Express med verktyg edition.  Om du inte uttryckligen behöver stöd för 64-bitars, förbrukar 32-bitarsversionen mindre minne när du kör.
2. Kör Konfigurationshanteraren för SQL Server 2014.

   1. Expandera den **SQL Server-nätverkskonfigurationen** nod i den vänstra träd-menyn.
   2. Klicka på **protokoll för SQLEXPRESS**.
   3. Högerklicka på **TCP/IP** och välj **aktivera**.  Klicka på **OK** i popup-fönstret.
   4. Högerklicka på **TCP/IP** och välj **egenskaper**.
   5. Klicka på den **IP-adresser** fliken.
   6. Hitta de **IPAll** nod.  I den **TCP-Port** anger **1433**.

          ![Configure SQL Express for TCP/IP][3]
   7. Klicka på **OK**.  Klicka på **OK** i popup-fönstret.
   8. Klicka på **SQL Server Services** i den vänstra träd-menyn.
   9. Högerklicka på **SQL Server (SQLEXPRESS)** och välj **starta om**
   10. Stäng Konfigurationshanteraren för SQL Server 2014.
3. Kör SQL Server 2014 Management Studio och ansluta till din lokala SQL Express-instans

   1. Högerklicka på din instans i Object Explorer och välj **egenskaper**
   2. Välj den **säkerhet** sidan.
   3. Se till att den **SQL Server och Windows-autentisering läge** har valts
   4. Klicka på **OK**

          ![Configure SQL Express Authentication][4]
   5. Expandera **säkerhet** > **inloggningar** i Object Explorer
   6. Högerklicka på **inloggningar** och välj **ny inloggning...**
   7. Ange ett inloggningsnamn.  Välj **SQL Server-autentisering**.  Ange ett lösenord och sedan ange samma lösenord i **Bekräfta lösenord**.  Lösenordet måste uppfylla krav på komplexitet Windows.
   8. Klicka på **OK**

          ![Add a new user to SQL Express][5]
   9. Högerklicka på en ny inloggning och välj **egenskaper**
   10. Välj den **serverroller** sidan
   11. Markera kryssrutan bredvid den **dbcreator** serverroll
   12. Klicka på **OK**
   13. Stäng SQL Server 2015 Management Studio

Se till att du anteckna användarnamnet och lösenordet som du har valt.  Du kan behöva tilldela ytterligare serverroller eller behörigheter beroende på dina specifika krav.

Node.js-program läser den **SQLCONNSTR_MS_TableConnectionString** miljövariabeln för anslutningssträngen för den här databasen.  Du kan ange den här variabeln i din miljö.  Du kan till exempel använda PowerShell för att ange den här miljövariabeln:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Åtkomst till databasen via en TCP/IP-anslutning och ange ett användarnamn och lösenord för anslutningen.

### <a name="howto-config-localdev"></a>Så här: konfigurera ditt projekt för lokal utveckling
Azure Mobile Apps läser en JavaScript-fil som heter *azureMobile.js* från det lokala filsystemet.  Använd inte den här filen för att konfigurera Azure Mobile Apps-SDK i produktion - App-inställningar i den [Azure-portalen] i stället.  Den *azureMobile.js* filen ska exportera ett konfigurationsobjekt.  De vanligaste inställningarna är:

* Databasinställningar
* Inställningar för diagnostikloggning
* Alternativa CORS-inställningarna

Ett exempel *azureMobile.js* filen implementera föregående databasinställningarna följer:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Vi rekommenderar att du lägger till *azureMobile.js* till din *.gitignore* fil (eller andra källkodskontroll ignorera filen) till att förhindra att lösenord lagras i molnet.  Konfigurera inställningar för produktion alltid i App-inställningar i den [Azure-portalen].

### <a name="howto-appsettings"></a>Så här: Konfigurera Appinställningar för din mobila App
De flesta inställningar i den *azureMobile.js* filen har en motsvarande Appinställning den [Azure-portalen].  Använd listan nedan för att konfigurera din app i App-inställningar:

| Appinställningen | *azureMobile.js* inställning | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |namn |Namnet på appen |sträng |
| **MS_MobileLoggingLevel** |Logging.level |Lägsta loggningsnivån för meddelanden att logga |fel, varning, information, verbose,-debug, Löjliga |
| **MS_DebugMode** |felsök |Aktivera eller inaktivera felsökningsläge |SANT, FALSKT |
| **MS_TableSchema** |data.schema |Schemat för standardnamnet för SQL-tabeller |String (standard: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktivera eller inaktivera felsökningsläge |SANT, FALSKT |
| **MS_DisableVersionHeader** |(Ange till odefinierad) version |Inaktiverar huvudet X-ZUMO-Server-Version |SANT, FALSKT |
| **MS_SkipVersionCheck** |skipversioncheck |Inaktiverar version klientkontrollen API |SANT, FALSKT |

Ange en inställning för appen:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din Mobilapp.
3. Inställningsbladet öppnas som standard. Om den inte, klickar du på **inställningar**.
4. Klicka på **programinställningar** i menyn Allmänt.
5. Rulla ned till avsnittet App-inställningar.
6. Om din app inställningen redan finns, klickar du på värdet för appinställningen redigera värdet.
7. Om din appinställning inte finns, anger du Appinställningen i rutan nyckel och värde i rutan värde.
8. När du är klar klickar du på **spara**.

Ändrar appinställningar för de flesta måste tjänsten startas om.

### <a name="howto-use-sqlazure"></a>Så här: använda SQL-databas som datalager produktion
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Med Azure SQL Database som ett datalager är identisk för alla typer i Azure App Service-programmet. Om du redan inte har gjort det, Följ dessa steg om du vill skapa en mobilappsserverdel.

1. Logga in på [Azure-portalen].
2. Längst upp till vänster i fönstret, klickar du på den **+ ny** knappen > **webb + mobilt** > **Mobilapp**, ange ett namn för din mobilappsserverdel.
3. I den **resursgruppen** ange samma namn som din app.
4. Standard programtjänstplanen har valts.  Om du vill ändra din App Service-plan kan du göra det genom att klicka på Apptjänstplan > **+ skapa nya**.  Ange ett namn för den nya programtjänstplanen och välj en lämplig plats.  Klicka på prisnivå och välj en lämplig prisnivån för tjänsten. Välj **visa alla** att visa priser fler alternativ, exempelvis **lediga** och **delade**.  När du har valt prisnivå, klickar du på den **Välj** knappen.  I den **programtjänstplanen** bladet, klickar du på **OK**.
5. Klicka på **Skapa**. Etablering av en mobilappsserverdel kan ta några minuter.  När serverdelen för Mobilappen etableras portalen öppnar den **inställningar** bladet för serverdelen för Mobilappen.

När du har skapat serverdelen för Mobilappen kan du ansluta en befintlig SQL-databas till din mobilappsserverdel eller skapa en ny SQL-databas.  I det här avsnittet skapar vi en SQL-databas.

> [!NOTE]
> Om du redan har en databas på samma plats som serverdelen för mobilappen kan du i stället väljer **Använd en befintlig databas** och välj sedan den databasen. Det rekommenderas inte användning av en databas på en annan plats på grund av ökad latens.
>
>

1. Klicka på ny mobilappsserverdel och **inställningar** > **Mobilapp** > **Data** > **+ Lägg till**.
2. I den **Lägg till dataanslutning** bladet, klickar du på **SQL Database - konfigurera nödvändiga inställningar** > **skapa en ny databas**.  Ange namnet på den nya databasen i den **namn** fältet.
3. Klicka på **Server**.  I den **ny server** bladet, ange ett unikt servernamn i den **servernamn** fältet och ange ett lämpligt **inloggning för serveradministratör** och **lösenord**.  Se till att **ge azure-tjänster åtkomst till servern** är markerad.  Klicka på **OK**.

    ![Skapa en Azure SQL Database][6]
4. På den **ny databas** bladet, klickar du på **OK**.
5. Tillbaka på den **Lägg till dataanslutning** bladet väljer **anslutningssträngen**, anger inloggningsnamn och lösenord som du angav när du skapar databasen.  Ange inloggningsuppgifterna för databasen om du använder en befintlig databas.  När du har angett, klickar du på **OK**.
6. Tillbaka på den **Lägg till dataanslutning** bladet Klicka på **OK** att skapa databasen.

<!--- END OF ALTERNATE INCLUDE -->

Skapa databasen kan ta några minuter.  Använd den **meddelanden** område för att övervaka förloppet för distributionen.  Passera inte förrän databasen har distribuerats korrekt.  När har distribuerats, skapas en anslutningssträng för SQL Database-instans i din mobila serverdel App-inställningar.  Du kan se den här appen inställningen på den **inställningar** > **programinställningar** > **anslutningssträngar**.

### <a name="howto-tables-auth"></a>Så här: kräver autentisering för åtkomst till tabeller
Om du vill använda autentisering för App Service med tabeller slutpunkten måste du konfigurera App Service-autentisering i den [Azure-portalen] första.  Mer information om hur du konfigurerar autentisering i en Azure App Service granska i konfigurationsguiden för identitetsleverantör som du tänker använda:

* [Så här konfigurerar du Azure Active Directory-autentisering]
* [Hur du konfigurerar Facebook-autentisering]
* [Så här konfigurerar du autentisering med Google]
* [Så här konfigurerar du Microsoft Authentication]
* [Hur du konfigurerar Twitter-autentisering]

Varje tabell har en åtkomst-egenskap som kan användas för att styra åtkomsten till tabellen.  I följande exempel visas en statiskt definierade tabell med autentisering krävs.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Åtkomst-egenskapen kan ha ett av tre värden

* *anonym* indikerar att klientprogrammet har tillåtelse att läsa data utan autentisering
* *autentiserad* indikerar att klientprogrammet måste skicka en giltig autentiserings-token med begäran
* *inaktiverad* anger att den här tabellen är för närvarande inaktiverat

Om åtkomst-egenskapen är odefinierad tillåts obehörig åtkomst.

### <a name="howto-tables-getidentity"></a>Så här: använda anspråk för autentisering med tabeller
Du kan ställa in olika anspråk som begärs när autentisering har ställts in.  Dessa anspråk inte är normalt tillgängliga via den `context.user` objekt.  Men de kan hämtas med hjälp av den `context.user.getIdentity()` metoden.  Den `getIdentity()` metoden returnerar en Promise som matchar ett objekt.  Objektet aktiveras av autentiseringsmetod (facebook, google, twitter, microsoftaccount eller aad).

Om du konfigurerar Account autentisering och begär anspråk på e-postadresser du lägga till e-postadressen till posten med följande tabell domänkontrollant:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Om du vill se vilka anspråk som är tillgängliga, kan du använda en webbläsare för att visa den `/.auth/me` slutpunkten för din webbplats.

### <a name="howto-tables-disabled"></a>Så här: inaktivera åtkomst till specifika tabellåtgärder
Förutom som visas i tabellen, kan egenskapen åtkomst användas för att kontrollera enskilda operationer.  Det finns fyra åtgärder:

* *läsa* är RESTful GET-åtgärd på tabellen
* *Infoga* är RESTful POST-åtgärden för tabellen
* *Uppdatera* korrigering av RESTful-åtgärd på tabellen
* *ta bort* är RESTful ta bort-åtgärden för tabellen

Exempelvis kanske du vill ange en skrivskyddad oautentiserade tabell:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Så här: justera frågan som används med åtgärder för tabeller
Ett vanligt krav för tabellåtgärder är att tillhandahålla en begränsad vy av data.  Du kan till exempel ange en tabell som är märkta med autentiserat användar-ID så att du kan bara läsa eller uppdatera dina egna poster.  Följande tabelldefinitionen innehåller den här funktionen:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Åtgärder som normalt köra en fråga har en fråga-egenskap som du kan justera med where satsen. Frågeegenskapen är en [QueryJS] objekt som används för att konvertera en OData-frågan till något som kan bearbeta data serverdelen.  För enkel likheten fall (se ovan), kan du använda en karta. Du kan också lägga till specifika SQL-satser:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Så här: Konfigurera mjuk borttagning för en tabell
Mjuk borttagning faktiskt bort inte poster.  I stället märks de som borttagna i databasen genom att den borttagna kolumnen till true.  Azure Mobile Apps-SDK tar automatiskt bort ej permanent borttagna poster från resultat om Mobile klient-SDK använder IncludeDeleted().  För att konfigurera en tabell för mjuk borttagning, ange den `softDelete` egenskapen i definitionsfilen för tabell:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Du bör fastställa en mekanism för att rensa poster – antingen från ett klientprogram, via ett Webbjobb Azure-funktion eller via en anpassad API.

### <a name="howto-tables-seeding"></a>Så här: startvärde för databasen med data
När du skapar ett nytt program kan du initiera en tabell med data.  Detta kan göras i tabellen definition JavaScript-fil på följande sätt:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Dirigering av data utförs endast när tabellen har skapats med Azure Mobile Apps-SDK.  Om tabellen redan finns i databasen injekteras inga data i tabellen.  Om dynamiska schemat är aktiverat härleda schemat från matade data.

Vi rekommenderar att du explicit anropa den `tables.initialize()` metoden för att skapa tabellen när tjänsten startar.

### <a name="Swagger"></a>Så här: aktivera stöd för Swagger
Azure Apptjänst Mobilappar medföljer inbyggda [Swagger] stöd.  Om du vill aktivera stöd för Swagger, installera swagger-användargränssnitt som ett beroende:

    npm install --save swagger-ui

När du har installerat kan du aktivera stöd för Swagger i Azure Mobile Apps-konstruktorn:

    var mobile = azureMobileApps({ swagger: true });

Du förmodligen bara vill aktivera stöd för Swagger i development utgåvor.  Du kan göra detta genom att använda den `NODE_ENV` appinställningen:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Swagger-slutpunkt finns på http://*yoursite*.azurewebsites.net/swagger.  Du kan komma åt Swagger-användargränssnitt via den `/swagger/ui` slutpunkt.  Om du väljer att kräva autentisering över hela programmet genererar Swagger ett fel.  För bästa resultat bör du välja att tillåta oautentiserad förfrågningar via i Azure App Service-autentisering / auktoriseringsinställningar därefter kontrollera autentisering med hjälp av den `table.access` egenskapen.

Du kan också lägga till alternativet Swagger till din `azureMobile.js` filen om du bara vill Swagger-stöd när du utvecklar lokalt.

## <a name="a-namepushpush-notifications"></a><a name="push">Push-meddelanden
Mobile Apps integreras med Azure Notification Hubs så att du kan skicka riktade push-meddelanden till miljontals enheter alla större plattformar. Genom att använda Notification Hubs kan du skicka push-meddelanden till iOS, Android och Windows-enheter. Mer information om alla som du kan göra med Notification Hubs finns [översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Så här: skicka push-meddelanden
Följande kod visar hur du använder push-objektet för att skicka skicka push-meddelanden till registrerade iOS-enheter:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Du kan i stället skicka en mall för push-meddelande till enheter på alla plattformar som stöds genom att skapa en mall för push-registrering från klienten. Följande kod visar hur du skickar ett meddelande om mallen:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Så här: skicka push-meddelanden till en autentiserad användare med hjälp av taggar
När en autentiserad användare registrerar för push-meddelanden, läggs en tagg för användar-ID automatiskt till registreringen. Du kan skicka push-meddelanden till alla enheter som registreras av en viss användare med hjälp av den här taggen. Följande kod hämtar SID för användaren som skickar förfrågan och skickar en mall för push-meddelanden till alla enhetsregistrering för användaren:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

När du registrerar dig för push-meddelanden från en autentiserad klient, se till att autentiseringen har slutförts innan du försöker utföra registreringen.

## <a name="CustomAPI"></a>Anpassade API: er
### <a name="howto-customapi-basic"></a>Så här: definiera en anpassad API
Azure Mobile Apps kan tillhandahålla anpassade API täckning utöver dataåtkomst API via /tables slutpunkten.  Anpassade API: er har definierats i ett liknande sätt att tabelldefinitionerna och har åtkomst till samma anläggningar, inklusive autentisering.

Om du vill använda App Service-autentisering med en anpassad API måste du konfigurera App Service-autentisering i den [Azure-portalen] första.  Mer information om hur du konfigurerar autentisering i en Azure App Service granska i konfigurationsguiden för identitetsleverantör som du tänker använda:

* [Så här konfigurerar du Azure Active Directory-autentisering]
* [Hur du konfigurerar Facebook-autentisering]
* [Så här konfigurerar du autentisering med Google]
* [Så här konfigurerar du Microsoft Authentication]
* [Hur du konfigurerar Twitter-autentisering]

Anpassade API: er har definierats i ungefär samma sätt som tabeller API.

1. Skapa en **api** directory
2. Skapa en API-definitionen JavaScript-fil i den **api** directory.
3. Använda importmetoden för att importera den **api** directory.

Här är prototyp api-definition baserat på basic-app-exemplet som vi använde tidigare.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Låt oss ta ett exempel API som returnerar datum server använder den *Date.now()* metod.  Här är filen api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Varje parameter är en standard RESTful-verb - GET, POST, uppdatera eller ta bort.  Metoden är en standard [ExpressJS Middleware] funktion som skickar utdata krävs.

### <a name="howto-customapi-auth"></a>Så här: kräver autentisering för åtkomst till en anpassad API
Azure Mobile Apps-SDK implementerar autentisering på samma sätt för både tabeller slutpunkt och anpassade API: er.  Om du vill lägga till autentisering i API: et som utvecklats i föregående avsnitt, lägga till en **åtkomst** egenskapen:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Du kan också ange autentisering på specifika åtgärder:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Samma token som används för slutpunkten tabeller måste användas för anpassade API: er som kräver autentisering.

### <a name="howto-customapi-auth"></a>Så här: hantera stora filöverföringar
Azure Mobile Apps-SDK använder den [body-parsern mellanprogram](https://github.com/expressjs/body-parser) att acceptera och avkoda brödtext innehållet i din ansökan.  Du kan förkonfigurera body-parsern för att godkänna större filöverföringar:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Filen är Base64-kodad före överföringen.  Detta ökar storleken på den faktiska överföringen (och därför storleken du måste ta hänsyn till).

### <a name="howto-customapi-sql"></a>Så här: köra anpassade SQL-instruktioner
Azure Mobile Apps-SDK tillåter åtkomst till hela kontexten via Begäranobjektet, så att du kan köra SQL-instruktioner till definierade DataProvider enkelt:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Felsökning, enkelt tabeller och enkelt API: er
### <a name="howto-diagnostic-logs"></a>Så här: Felsök, diagnostisera och Felsök Azure Mobile apps
Azure App Service tillhandahåller flera felsökning och felsökningstekniker för Node.js-program.
Se följande artiklar för att komma igång vid felsökning av din Node.js Mobile-serverdel:

* [Övervaka en Azure Apptjänst]
* [Aktivera diagnostikloggning i Azure App Service]
* [Felsöka en Azure Apptjänst i Visual Studio]

Node.js-program har åtkomst till en mängd olika verktyg diagnostiska loggen.  Azure Mobile Apps Node.js SDK använder internt, [Winston] för diagnostikloggning.  Loggning aktiveras automatiskt genom att aktivera felsökningsläge eller genom att ange den **MS_DebugMode** appinställningen till true i den [Azure-portalen]. Genererade loggar visas i diagnostiska loggar på den [Azure-portalen].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Så här: arbeta med enkelt tabeller i Azure-portalen
Enkelt tabeller i portalen kan du skapa och arbeta med tabeller direkt i portalen. Du kan ladda upp datauppsättningen enkelt tabeller i CSV-format. Observera att du inte kan använda egenskaper namn (i din CSV-dataset) som står i konflikt med egenskaper systemnamn på Azure Mobile Apps-serverdel. De egenskaper namn är:
* CreatedAt
* updatedAt
* ta bort
* version

Du kan även redigera åtgärder för tabeller med App Service-redigeraren. När du klickar på **enkelt tabeller** i dina backend-inställningar, du kan lägga till, ändra eller ta bort en tabell. Du kan också se data i tabellen.

![Arbeta med enkelt tabeller](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Följande kommandon är tillgängliga i kommandofältet för en tabell:

* **Ändra behörigheter** - ändringsbehörighet för läsning, infoga, uppdatera och ta bort tabellen.
  Alternativen är att tillåta anonym åtkomst, att kräva autentisering eller inaktivera all åtkomst till åtgärden.
* **Redigera skriptet** -skriptfil för tabellen öppnas i App Service Editor.
* **Hantera schemat** - Lägg till eller ta bort kolumner eller ändra Tabellindex.
* **Rensa tabellen** -trunkerar en befintlig tabell är om du tar bort alla datarader men lämnar schemat oförändrat.
* **Ta bort rader** -ta bort enskilda rader med data.
* **Visa direktuppspelningsloggar** -ansluter du till strömmande Loggtjänsten för webbplatsen.

### <a name="work-easy-apis"></a>Så här: arbeta med enkla API: er i Azure-portalen
Enkelt API: er i portalen kan du skapa och arbeta med anpassade API: er direkt i portalen. Du kan redigera API-skript med hjälp av Redigeraren för App Service.

När du klickar på **enkelt API: er** i dina backend-inställningar, du kan lägga till, ändra eller ta bort en anpassad API-slutpunkt.

![Arbeta med enkla API: er](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

I portalen du ändra åtkomstbehörighet för en viss HTTP-åtgärd, redigera skriptfilen API i App Service Editor eller visa strömmande loggarna.

### <a name="online-editor"></a>Så här: redigera kod i App Service Editor
Azure-portalen kan du redigera dina Node.js-serverdel skriptfiler i App Service Editor utan att behöva hämta projektet till den lokala datorn. Redigera filer i redigeraren online:

1. I din Mobilapp backend-bladet klickar du på **alla inställningar** > antingen **enkelt tabeller** eller **enkelt API: er**, klicka på en tabell eller API: et och sedan på **redigera skriptet**. Skriptet öppnas i App Service Editor.

    ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. Gör ändringarna i filen koden i redigeraren online. Ändringarna sparas automatiskt när du skriver.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Snabbstart för Android-klienten]: app-service-mobile-android-get-started.md
[Snabbstart för Apache Cordova-klient]: app-service-mobile-cordova-get-started.md
[iOS klienten Snabbstart]: app-service-mobile-ios-get-started.md
[Snabbstart för Xamarin.iOS-klient]: app-service-mobile-xamarin-ios-get-started.md
[Snabbstart för Xamarin.Android-klient]: app-service-mobile-xamarin-android-get-started.md
[Snabbstart för Xamarin.Forms-klient]: app-service-mobile-xamarin-forms-get-started.md
[Snabbstart för Windows Store-klienten]: app-service-mobile-windows-store-dotnet-get-started.md
[datasynkronisering offline]: app-service-mobile-offline-data-sync.md
[Så här konfigurerar du Azure Active Directory-autentisering]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Hur du konfigurerar Facebook-autentisering]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Så här konfigurerar du autentisering med Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Så här konfigurerar du Microsoft Authentication]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Hur du konfigurerar Twitter-autentisering]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service Deployment Guide]: ../app-service/app-service-deploy-local-git.md
[Övervaka en Azure Apptjänst]: ../app-service/web-sites-monitor.md
[Aktivera diagnostikloggning i Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Felsöka en Azure Apptjänst i Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[ange nod-Version]: ../nodejs-specify-node-version-azure-apps.md
[använda noden moduler]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure-portalen]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp exempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo prov på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[katalogen samples på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[1.1 för Node.js-verktyg för Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js paketet]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

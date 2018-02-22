---
title: "Hur du arbetar med SDK för Node.js backend-Server för Mobile Apps | Microsoft Docs"
description: "Lär dig hur du arbetar med SDK för Node.js backend-Server för Azure Apptjänst Mobilappar."
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
ms.openlocfilehash: bd423d6fb62b2ace16832f665c8834b4aea7e26f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Hur du använder Mobile Apps-SDK för Node.js
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Den här artikeln innehåller detaljerad information och exempel som visar hur du arbetar med en Node.js-serverdel i funktionen Mobilappar i Azure App Service.

## <a name="Introduction"></a>Introduktion
Mobilappar ger möjlighet att lägga till en-optimerad dataåtkomst Web API till ett webbprogram. Mobile Apps-SDK har angetts för ASP.NET och Node.js webbprogram. SDK innehåller följande åtgärder:

* Tabell åtgärder (läsa, infoga, uppdatera, ta bort) för dataåtkomst
* Anpassade API-åtgärder

Både operations erbjuda autentisering över alla identitetsleverantörer som gör att Azure App Service. Dessa providers omfattar sociala identitetsleverantörer, till exempel Facebook, Twitter, Google, Microsoft, samt och Azure Active Directory för enterprise-identitet.

Du kan hitta exempel för varje användningsfall som i den [katalogen samples på GitHub].

## <a name="supported-platforms"></a>Plattformar som stöds
Mobile Apps Node.js SDK har stöd för den aktuella LTS-versionen av noden och senare. Den senaste versionen av LTS är för närvarande nod v4.5.0. Andra versioner av noden fungerar men stöds inte.

Mobile Apps Node.js SDK stöder två databasdrivrutiner för: 

* Noden mssql-drivrutinen har stöd för Azure SQL Database och lokala SQL Server-instanser.  
* Sqlite3-drivrutinen stöder SQLite databaser på en enda instans.

### <a name="howto-cmdline-basicapp"></a>Skapa en enkel Node.js-serverdel med hjälp av kommandoraden
Varje Mobile Apps Node.js-serverdel startar som ett ExpressJS program. ExpressJS är det mest populära web service ramverket för Node.js. Du kan skapa en grundläggande [Express] program på följande sätt:

1. Skapa en katalog för ditt projekt i ett kommando eller PowerShell-fönstret:

        mkdir basicapp
2. Kör `npm init` initieras strukturen för paketet:

        cd basicapp
        npm init

   Den `npm init` kommandot frågar en uppsättning frågor att starta projektet. Se de exempel på utdatan:

   ![Npm init-utdata][0]
3. Installera den `express` och `azure-mobile-apps` bibliotek från npm-databasen:

        npm install --save express azure-mobile-apps
4. Skapa en app.js-fil om du vill implementera grundläggande mobila server:

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table.
        mobile.tables.add('TodoItem');

        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);

Det här programmet skapar ett Web API optimerad med en enda slutpunkt (`/tables/TodoItem`) som ger obehörig åtkomst till en underliggande SQL-databasen med hjälp av en dynamisk schemat. Det är lämpligt för följande Snabbstart för klient-biblioteket:

* [Snabbstart för Android-klienten]
* [Snabbstart för Apache Cordova-klient]
* [iOS klienten Snabbstart]
* [Snabbstart för Windows Store-klienten]
* [Snabbstart för Xamarin.iOS-klient]
* [Snabbstart för Xamarin.Android-klient]
* [Snabbstart för Xamarin.Forms-klient]

Du hittar koden för den här grundläggande program i den [basicapp exempel på GitHub].

### <a name="howto-vs2015-basicapp"></a>Skapa en Node.js-serverdel med hjälp av Visual Studio 2015
Visual Studio 2015 kräver ett tillägg till utvecklar Node.js-program i IDE. Starta genom att installera den [1.1 för Node.js-verktyg för Visual Studio]. När installationen är klar skapar du en snabb 4.x-program:

1. Öppna den **nytt projekt** dialogrutan (från **filen** > **ny** > **projekt**).
2. Expandera **mallar** > **JavaScript** > **Node.js**.
3. Välj **grundläggande Azure Node.js Express 4 programmet**.
4. Fyll i projektets namn. Välj **OK**.

   ![Visual Studio 2015 nytt projekt][1]
5. Högerklicka på den **npm** och välj **installera nya npm paket**.
6. Du kan behöva uppdatera npm-katalogen när du har skapat din första Node.js-program. Välj **uppdatera** om det behövs.
7. Ange **azure mobilappar** i sökrutan. Välj den **azure mobile apps 2.0.0** paketet och välj sedan **installera paket**.

   ![Installera nya npm-paket][2]
8. Välj **Stäng**.
9. Öppna filen app.js om du vill lägga till stöd för Mobile Apps-SDK. AT rad 6 at längst ned i biblioteket `require` instruktioner, Lägg till följande kod:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

   AT rad ungefär 27 efter en `app.use` instruktioner, Lägg till följande kod:

        app.use('/users', users);

        // Mobile Apps initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

   Spara filen.
10. Kör programmet lokalt (API: N hanteras på http://localhost: 3000) eller publicera till Azure.

### <a name="create-node-backend-portal"></a>Skapa en Node.js-serverdel med hjälp av Azure portal
Du kan skapa en Mobile Apps serverdel direkt i den [Azure-portalen]. Du kan utföra följande steg eller skapa en klient och server tillsammans genom att följa den [skapar en mobil app](app-service-mobile-ios-get-started.md) kursen. Vägledningen innehåller en förenklad version av dessa anvisningar och är bäst för proof of concept projekt.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Tillbaka i den **Kom igång** fönstret under **skapa en tabell API**, Välj **Node.js** som backend-språk.
Markera kryssrutan för **bekräftar jag att allt webbinnehåll skrivs**, och välj sedan **skapa TodoItem-tabell**.

### <a name="download-quickstart"></a>Ladda ned Node.js-backend-Snabbstart Kodprojekt via Git
När du skapar en Node.js Mobile Apps serverdel med hjälp av portalen **Snabbstart** rutan ett Node.js-projekt skapas för dig och distribueras till din webbplats. Du kan lägga till tabeller och API: er och redigera kodfiler för Node.js-serverdel i portalen. Du kan också använda olika distributionsverktyg för att hämta backend-projektet så att du kan lägga till eller ändra tabeller och API: er och sedan publicera projektet. Mer information finns i [Distributionsguide för Azure App Service]. 

Följande procedur använder en Git-lagringsplats för att hämta Projektkod Snabbstart:

1. Installera Git om du inte redan gjort det. Anvisningar för att installera Git variera mellan olika operativsystem. Operativsystemspecifika distributioner och installationer finns på [installerar Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Följ stegen i [aktivera databasen för Apptjänst-app](../app-service/app-service-deploy-local-git.md#Step3) att aktivera Git-lagringsplats för backend-platsen. Anteckna distribution användarnamn och lösenord.
3. I fönstret för Mobile Apps serverdel, notera den **URL för Git-klon** inställningen.
4. Köra den `git clone` kommandot med hjälp av URL för Git-klonen. Ange ditt lösenord vid behov, som i följande exempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Bläddra till den lokala katalogen (`/todolist` i föregående exempel), och Lägg märke till att projektfiler har laddats ned. Leta upp filen todoitem.json i den `/tables` directory. Den här filen definierar behörigheter i tabellen. Filen todoitem.js finns i samma katalog. Den definierar CRUD-skript för åtgärden för tabellen.
6. När du gör ändringar i projektfiler, kör följande kommandon för att lägga till, genomför och sedan överföra ändringarna till webbplatsen:

        $ git commit -m "updated the table script"
        $ git push origin master

   När du lägger till nya filer i projektet, måste du först köra den `git add .` kommando.

Varje gång en ny uppsättning incheckningar skickas till platsen publiceras på webbplatsen.

### <a name="howto-publish-to-azure"></a>Publicera din Node.js-serverdel i Azure
Microsoft Azure tillhandahåller många metoder för att publicera din Node.js för Mobile Apps serverdel till Azure-tjänsten. Metoderna inkluderar distributionsverktyg integrerade i Visual Studio, kommandoradsverktyg och kontinuerlig distributionsalternativ baserat på källkontroll. Mer information finns i [Distributionsguide för Azure App Service].

Azure Apptjänst har råd för Node.js-program som du bör läsa innan du publicerar serverdelen:

* Så här [ange nod-version]
* Så här [använda noden moduler]

### <a name="howto-enable-homepage"></a>Aktivera en hemsida för ditt program
Många program är en kombination av webb- och mobilappar. Du kan använda ExpressJS framework för att kombinera två aspekter. Ibland, men kanske du vill bara implementerar ett gränssnitt för mobila. Det är praktiskt att ange en hemsida för att säkerställa att app-tjänsten är igång och körs. Du kan ange en egen hemsida eller aktivera en tillfällig startsida. Om du vill aktivera en tillfällig startsida, använder du följande kod för att instansiera Mobile Apps:

    var mobile = azureMobileApps({ homePage: true });

Om du vill bara det här alternativet är tillgängligt när du utvecklar lokalt, kan du lägga till den här inställningen azureMobile.js-filen.

## <a name="TableOperations"></a>Åtgärder för tabeller
Node.js Server-SDK för azure mobilappar ger metoder för att visa datatabeller som lagras i Azure SQL Database som en webb-API. Det finns fem:

| Åtgärd | Beskrivning |
| --- | --- |
| GET-/tables/*tablename* |Hämta alla poster i tabellen. |
| GET-/tables/*tablename*/:id |Hämta en viss post i tabellen. |
| POST /tables/*tablename* |Skapa en post i tabellen. |
| KORRIGERING /tables/*tablename*/:id |Uppdatera en post i tabellen. |
| Ta bort /tables/*tablename*/:id |Ta bort en post i tabellen. |

Den här webb-API: et stöder [OData] och utökar tabellschemat att stödja [datasynkronisering offline].

### <a name="howto-dynamicschema"></a>Definiera tabeller med hjälp av en dynamisk schema
Du måste definiera innan du kan använda en tabell. Du kan definiera tabeller med hjälp av en statisk schemat (där du definiera vilka kolumner i schemat) eller dynamiskt (om SDK styr schema baserat på inkommande begäranden). Du kan dessutom styra vissa aspekter av webb-API genom att lägga till JavaScript-kod definitionen.

Som bästa praxis bör du definiera varje tabell i en JavaScript-fil i den `tables` directory, och sedan använda den `tables.import()` metod för att importera tabellerna. Utöka basic-app exemplet skulle du justera filen app.js:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed.
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined.
    mobile.tables.initialize().then(function () {
        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);
    });

Definiera tabellen i-. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here.

    module.exports = table;

En dynamisk schemat använder som standard. Om du vill inaktivera dynamiska schemat globalt, ange den `MS_DynamicSchema` appinställningen false i Azure-portalen.

Du hittar ett komplett exempel i den [todo prov på GitHub].

### <a name="howto-staticschema"></a>Definiera tabeller med en statisk schema
Du kan uttryckligen definiera kolumner att visa via webb-API. Azure mobilappar Node.js SDK lägger automatiskt till några extra kolumner som krävs för datasynkronisering offline i listan som du anger. Exempelvis klientprogram Snabbstart kräver en tabell med två kolumner: `text` (en sträng) och `complete` (ett booleskt värde).  
Tabellen kan definieras i tabellen definition JavaScript-fil (finns i den `tables` directory) enligt följande:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    module.exports = table;

Om du definierar tabeller statiskt, måste du också anropa den `tables.initialize()` metoden för att skapa databasschemat vid start. Den `tables.initialize()` metoden returnerar en [promise] så att webbtjänsten inte betjäna begäranden innan databasen har initierats.

### <a name="howto-sqlexpress-setup"></a>Använda SQL Server Express som en utveckling datalager på den lokala datorn
Mobile Apps Node.js SDK innehåller betjänar data direkt på tre sätt:

* Använd den **minne** drivrutinen att tillhandahålla en icke-beständig exempel store.
* Använd den **mssql** drivrutinen att tillhandahålla ett dataarkiv som SQL Server Express för utveckling.
* Använd den **mssql** drivrutinen att tillhandahålla en Azure SQL Database-datalagret för produktion.

Mobile Apps Node.js SDK använder den [mssql Node.js paketet] upprätta och använda en anslutning till SQL Server Express och SQL-databas. Det här paketet kräver att du aktiverar TCP-anslutningar på din SQL Server Express-instans.

> [!TIP]
> Minne-drivrutinen ger inte en fullständig uppsättning funktioner för testning. Om du vill testa serverdelen lokalt rekommenderar vi använda ett SQL Server Express datalager och mssql-drivrutinen.
>
>

1. Hämta och installera [Microsoft SQL Server 2014 Express]. Se till att du installerar SQL Server 2014 Express med verktyg edition. Om du inte uttryckligen behöver stöd för 64-bitars, förbrukar 32-bitarsversionen mindre minne när du kör.
2. Kör Konfigurationshanteraren för SQL Server 2014:

   a. Expandera den **SQL Server-nätverkskonfigurationen** nod i träd-menyn.

   b. Välj **protokoll för SQLEXPRESS**.

   c. Högerklicka på **TCP/IP** och välj **aktivera**. Välj **OK** i popup-dialogrutan.

   d. Högerklicka på **TCP/IP** och välj **egenskaper**.

   e. Välj den **IP-adresser** fliken.

   f. Hitta de **IPAll** nod. I den **TCP-Port** anger **1433**.

      ![Konfigurera SQL Server Express för TCP/IP][3]

   g. Välj **OK**. Välj **OK** i popup-dialogrutan.

   h. Välj **SQL Server Services** i träd-menyn.

   i. Högerklicka på **SQL Server (SQLEXPRESS)** och välj **starta om**.

   j. Stäng Konfigurationshanteraren för SQL Server 2014.
3. Kör SQL Server 2014 Management Studio och ansluta till din lokala SQL Server Express-instans:

   1. Högerklicka på din instans i Object Explorer och välj **egenskaper**.
   2. Välj den **säkerhet** sidan.
   3. Se till att **SQL Server och Windows-autentisering läge** är markerad.
   4. Välj **OK**.

      ![Konfigurera SQL Server Express-autentisering][4]
   5. Expandera **säkerhet** > **inloggningar** i Object Explorer.
   6. Högerklicka på **inloggningar** och välj **ny inloggning**.
   7. Ange ett inloggningsnamn. Välj **SQL Server-autentisering**. Ange ett lösenord och sedan ange samma lösenord i **Bekräfta lösenord**. Lösenordet måste uppfylla krav på komplexitet Windows.
   8. Välj **OK**.

      ![Lägg till en ny användare i SQL Server Express][5]
   9. Högerklicka på en ny inloggning och välj **egenskaper**.
   10. Välj den **serverroller** sidan.
   11. Markera kryssrutan för den **dbcreator** -serverrollen.
   12. Välj **OK**.
   13. Stäng SQL Server 2015 Management Studio.

Kom ihåg att registrera det användarnamn och lösenord som du har valt. Du kan behöva tilldela ytterligare serverroller eller behörigheter, beroende på din databas.

Node.js-program läser den `SQLCONNSTR_MS_TableConnectionString` miljövariabeln för anslutningssträngen för den här databasen. Du kan ange den här variabeln i din miljö. Du kan till exempel använda PowerShell för att ange den här miljövariabeln:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Åtkomst till databasen via en TCP/IP-anslutning. Ange ett användarnamn och lösenord för anslutningen.

### <a name="howto-config-localdev"></a>Konfigurera ditt projekt för lokal utveckling
Mobile Apps läser en JavaScript-fil som heter *azureMobile.js* från det lokala filsystemet. Använd inte den här filen för att konfigurera Mobile Apps-SDK i produktion. Använd i stället **appinställningar** i den [Azure-portalen]. 

Filen azureMobile.js ska exportera ett konfigurationsobjekt. De vanligaste inställningarna är:

* Databasinställningar
* Inställningar för diagnostikloggning
* Alternativa CORS-inställningarna

Den här azureMobile.js exempelfil implementerar föregående databasinställningarna:

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

Vi rekommenderar att du lägger till azureMobile.js .gitignore-fil (eller andra källkodskontroll ignorera filen) till att förhindra att lösenord lagras i molnet. Konfigurera inställningar för produktion i alltid **appinställningar** inom den [Azure-portalen].

### <a name="howto-appsettings"></a>Konfigurera appinställningar för din mobila app
De flesta inställningar i filen azureMobile.js har en motsvarande appinställning i den [Azure-portalen]. Använd listan nedan för att konfigurera din app i **appinställningar**:

| Appinställningen | azureMobile.js setting | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |namn |Namnet på appen |sträng |
| **MS_MobileLoggingLevel** |logging.level |Lägsta loggningsnivån för meddelanden att logga |fel, varning, information, verbose,-debug, Löjliga |
| **MS_DebugMode** |felsök |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_TableSchema** |data.schema |Schemat för standardnamnet för SQL-tabeller |String (standard: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_DisableVersionHeader** |(Ange till odefinierad) version |Inaktiverar huvudet X-ZUMO-Server-Version |SANT, FALSKT |
| **MS_SkipVersionCheck** |skipversioncheck |Inaktiverar version klientkontrollen API |SANT, FALSKT |

Ange en inställning för appen:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster**, och välj sedan namnet på din mobila app.
3. Den **inställningar** rutan öppnas som standard. Om det inte, väljer **inställningar**.
4. På den **allmänna** väljer du **programinställningar**.
5. Bläddra till den **appinställningar** avsnitt.
6. Om din app inställningen redan finns markerar du värdet för appinställningen redigera värdet.
   Om din appinställning inte finns, ange appinställningen i den **nyckeln** rutan och värdet i den **värdet** rutan.
8. Välj **Spara**.

Ändrar appinställningar för de flesta måste tjänsten startas om.

### <a name="howto-use-sqlazure"></a>Använda SQL-databas som produktionsdata lagra
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Med Azure SQL Database som ett datalager är identisk för alla typer i Azure App Service-programmet. Om du redan inte har gjort det, Följ dessa steg om du vill skapa en Mobile Apps-serverdel:

1. Logga in på [Azure-portalen].
2. I det övre vänstra fönstret, Välj den **+ ny** knappen > **webb + mobilt** > **Mobilapp**, och ange sedan ett namn för Mobile Apps serverdel.
3. I den **resursgruppen** ange samma namn som din app.
4. Standard programtjänstplanen har valts. Om du vill ändra din programtjänstplan:

   a. Välj **Programtjänstplanen** > **+ skapa nya**. 
   
   b. Ange ett namn för den nya programtjänstplanen och välj en lämplig plats. 
   
   c. Välj en lämplig prisnivån för tjänsten. Välj **visa alla** att visa priser fler alternativ, exempelvis **lediga** och **delade**. 
   
   d. Klicka på den **Välj** knappen. 
   
   e. I den **programtjänstplanen** väljer **OK**.
5. Välj **Skapa**. 

Etablera en Mobile Apps kan serverdel ta några minuter. När Mobile Apps tillbaka slutet etableras, öppnar portalen den **inställningar** fönstret för Mobile Apps-serverdel.

Du kan välja att ansluta en befintlig SQL-databas till din Mobile Apps-serverdel eller skapa en ny SQL-databas. I det här avsnittet skapar vi en SQL-databas.

> [!NOTE]
> Om du redan har en databas på samma plats som Mobile Apps serverdel kan du istället välja **Använd en befintlig databas** och välj sedan den databasen. Vi rekommenderar inte att använda en databas på en annan plats på grund av ökad latens.
>
>

1. I den nya Mobile Apps serverdelen, väljer **inställningar** > **Mobilapp** > **Data** > **+ Lägg till**.
2. I den **Lägg till dataanslutning** väljer **SQL Database - konfigurera nödvändiga inställningar** > **skapa en ny databas**. Ange namnet på den nya databasen i den **namn** rutan.
3. Välj **Server**. I den **ny server** rutan Ange ett unikt servernamn i den **servernamn** rutan och ange en lämplig server admin inloggningsnamn och lösenord. Se till att **ge azure-tjänster åtkomst till servern** är markerad. Välj **OK**.

   ![Skapa en Azure SQL Database][6]
4. I den **ny databas** väljer **OK**.
5. I den **Lägg till dataanslutning** väljer **anslutningssträngen**, och ange inloggningsnamn och lösenord som du angav när du skapade databasen. Ange inloggningsuppgifterna för databasen om du använder en befintlig databas. Välj **OK**.
6. I den **Lägg till dataanslutning** fönstret igen och välj **OK** att skapa databasen.

<!--- END OF ALTERNATE INCLUDE -->

Skapa databasen kan ta några minuter. Använd den **meddelanden** område för att övervaka förloppet för distributionen. Passera inte förrän databasen har distribuerats. När databasen har distribuerats skapas en anslutningssträng för SQL-databasinstans i inställningarna för Mobile Apps backend-app. Du kan se den här appen inställningen i **inställningar** > **programinställningar** > **anslutningssträngar**.

### <a name="howto-tables-auth"></a>Kräva autentisering för åtkomst till tabeller
Om du vill använda autentisering för App Service med den `tables` slutpunkt, måste du konfigurera App Service-autentisering i den [Azure-portalen] första. Mer information finns i konfigurationsguiden för identitetsleverantör som du tänker använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Varje tabell har en åtkomst-egenskap som du kan använda för att styra åtkomsten till tabellen. I följande exempel visas en statiskt definierade tabell med autentisering krävs.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Åtkomst-egenskapen kan ha ett av tre värden:

* *anonym* indikerar att klientprogrammet har tillåtelse att läsa data utan autentisering.
* *autentiserad* indikerar att klientprogrammet måste skicka en giltig autentiserings-token med begäran.
* *inaktiverad* anger att den här tabellen är för närvarande inaktiverat.

Om åtkomst-egenskapen är odefinierad tillåts obehörig åtkomst.

### <a name="howto-tables-getidentity"></a>Använda anspråk för autentisering med tabeller
Du kan ställa in olika anspråk som begärs när autentisering har ställts in. Dessa anspråk inte är normalt tillgängliga via den `context.user` objekt. Men du kan hämta dem med hjälp av den `context.user.getIdentity()` metoden. Den `getIdentity()` metoden returnerar en promise som matchar ett objekt. Objektet aktiveras av autentiseringsmetod (`facebook`, `google`, `twitter`, `microsoftaccount`, eller `aad`).

Om du konfigurerar autentisering för Microsoft-konto och begär anspråk på e-postadresser du lägga till e-postadressen till posten med följande tabell domänkontrollant:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition.
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

    // Configure specific code when the client does a request.
    // READ: only return records that belong to the authenticated user.
    table.read(queryContextForEmail);

    // CREATE: add or overwrite the userId based on the authenticated user.
    table.insert(addEmailToContext);

    // UPDATE: only allow updating of records that belong to the authenticated user.
    table.update(queryContextForEmail);

    // DELETE: only allow deletion of records that belong to the authenticated user.
    table.delete(queryContextForEmail);

    module.exports = table;

Om du vill se vilka anspråk som är tillgängliga, kan du använda en webbläsare för att visa den `/.auth/me` slutpunkten för din webbplats.

### <a name="howto-tables-disabled"></a>Inaktivera åtkomst till specifika tabellåtgärder
Förutom som visas i tabellen, kan egenskapen åtkomst användas för att kontrollera enskilda operationer. Det finns fyra åtgärder:

* `read` är RESTful GET-åtgärd på tabellen.
* `insert` är åtgärden RESTful POST i tabellen.
* `update` är korrigering av RESTful-åtgärden för tabellen.
* `delete` Det är den RESTful ta bort i tabellen.

Du kanske vill ange en skrivskyddad oautentiserade tabell:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-only table. Only allow READ operations.
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Justera frågan som används med åtgärder för tabeller
Ett vanligt krav för tabellåtgärder är att tillhandahålla en begränsad vy av data. Du kan till exempel ange en tabell som är märkta med autentiserat användar-ID så att du kan bara läsa eller uppdatera dina egna poster. Följande tabelldefinitionen innehåller den här funktionen:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table.
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table.
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved.
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user.
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Åtgärder som normalt köra en fråga har en fråga-egenskap som du kan justera med hjälp av en `where` satsen. Frågeegenskapen är en [QueryJS] objekt som används för att konvertera en OData-fråga till något som serverdel data kan bearbeta. Du kan använda en karta för enkel likheten fall (se ovan). Du kan också lägga till specifika SQL-satser:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Konfigurera en mjuk borttagning för en tabell
En mjuk borttagning faktiskt bort inte poster. I stället märks de som borttagna i databasen genom att den borttagna kolumnen till true. Mobile Apps-SDK tar automatiskt bort ej permanent borttagna poster från resultat om Mobile klient-SDK använder `IncludeDeleted()`. För att konfigurera en tabell för mjuk borttagning, ange den `softDelete` egenskapen i definitionsfilen för tabell:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Turn on soft delete.
    table.softDelete = true;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Du bör fastställa en mekanism för borttagning av poster: ett klientprogram, ett Webbjobb, en Azure-funktion eller en anpassad API.

### <a name="howto-tables-seeding"></a>Startvärde för databasen med data
När du skapar ett nytt program, kanske du vill initiera en tabell med data. Du kan göra i tabellen definition JavaScript-fil på följande sätt:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Dirigering av data sker bara när du har använt Mobile Apps-SDK för att skapa tabellen. Om tabellen redan finns i databasen injekteras inga data i tabellen. Om dynamiska schemat är aktiverat härleda schemat från matade data.

Vi rekommenderar att du explicit anropa den `tables.initialize()` metoden för att skapa tabellen när tjänsten startar.

### <a name="Swagger"></a>Aktivera stöd för Swagger
Mobile Apps medföljer inbyggda [Swagger] stöd. Om du vill aktivera stöd för Swagger, installera swagger-användargränssnitt som ett beroende:

    npm install --save swagger-ui

Sedan kan du aktivera stöd för Swagger i Mobile Apps-konstruktorn:

    var mobile = azureMobileApps({ swagger: true });

Du förmodligen bara vill aktivera stöd för Swagger i development utgåvor. Du kan göra detta med hjälp av den `NODE_ENV` appinställningen:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Den `swagger` slutpunkt finns på http://*yoursite*.azurewebsites.net/swagger. Du kan komma åt Swagger-användargränssnitt via den `/swagger/ui` slutpunkt. Om du väljer att kräva autentisering över hela programmet genererar Swagger ett fel. För bästa resultat bör du välja att tillåta oautentiserade begäranden i Azure autentisering/auktorisering i Apptjänst-inställningar och kontrollera sedan autentisering med hjälp av den `table.access` egenskapen.

Du kan också lägga till alternativet Swagger azureMobile.js-filen om du bara vill Swagger-stöd för utveckling av lokalt.

## <a name="a-namepushpush-notifications"></a><a name="push">Push-meddelanden
Mobile Apps integreras med Azure Notification Hubs så att du kan skicka riktade push-meddelanden till miljontals enheter för alla större plattformar. Genom att använda Notification Hubs kan du skicka push-meddelanden till iOS, Android och Windows enheter. Mer information om alla som du kan göra med Notification Hubs finns i [översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Skicka push-meddelanden
Följande kod visar hur du använder den `push` objekt som ska skicka push-meddelanden skickas till registrerade iOS-enheter:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured.
    if (context.push) {
        // Send a push notification by using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Du kan i stället skicka en mall för push-meddelande till enheter på alla plattformar som stöds genom att skapa en mall för push-registrering från klienten. Följande kod visar hur du skickar ett meddelande om mallen:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured.
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Skicka push-meddelanden till en autentiserad användare med hjälp av taggar
När en autentiserad användare registrerar för push-meddelanden, läggs en tagg för användar-ID automatiskt till registreringen. Du kan skicka push-meddelanden till alla enheter som registreras av en viss användare med hjälp av den här taggen. Följande kod hämtar SID för användaren som begäran kommer ifrån och skickar en mall för push-meddelanden till alla enhetsregistrering för användaren:

    // Only do the push if configured.
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

När du registrerat för push-meddelanden från en autentiserad klient kan du kontrollera att autentiseringen har slutförts innan du försöker registrera.

## <a name="CustomAPI"></a> Anpassade API: er
### <a name="howto-customapi-basic"></a>Definiera en anpassad API
Förutom Data Access-API via den `/tables` slutpunkt, Mobile Apps kan tillhandahålla anpassade API täckning. Anpassade API: er har definierats i ett liknande sätt att tabelldefinitionerna och har åtkomst till samma anläggningar, inklusive autentisering.

Om du vill använda App Service-autentisering med en anpassad API, måste du konfigurera App Service-autentisering i den [Azure-portalen] första. Mer information finns i konfigurationsguiden för identitetsleverantör som du tänker använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Anpassade API: er definieras på ungefär samma sätt som tabeller API:

1. Skapa en `api` directory.
2. Skapa en API-definitionen JavaScript-fil i den `api` directory.
3. Använda importmetoden för att importera den `api` directory.

Här är prototyp API-definition baserat på exemplet basic-app som vi använde tidigare:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Låt oss ta ett exempel API som returnerar datum server med hjälp av den `Date.now()` metoden. Här är filen api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Varje parameter är en standard RESTful-verb: hämta, publicera, KORRIGERA eller ta bort. Metoden är en standard [ExpressJS mellanprogram] funktion som skickar utdata krävs.

### <a name="howto-customapi-auth"></a>Kräva autentisering för åtkomst till en anpassad API
Mobile Apps-SDK implementerar autentisering på samma sätt för både den `tables` slutpunkt och anpassade API: er. Om du vill lägga till autentisering i API: et som utvecklats i föregående avsnitt, lägga till en `access` egenskapen:

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

Samma token som används för den `tables` slutpunkt måste användas för anpassade API: er som kräver autentisering.

### <a name="howto-customapi-auth"></a>Hantera stora filöverföringar
Mobile Apps-SDK använder den [body-parsern mellanprogram](https://github.com/expressjs/body-parser) att acceptera och avkoda brödtext innehållet i din ansökan. Du kan förkonfigurera body-parsern för att godkänna större filöverföringar:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling.
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);

Filen är Base64-kodad före överföringen. Den här kodningen ökar storleken på den faktiska överföringen (och den storlek som du måste ta hänsyn till).

### <a name="howto-customapi-sql"></a>Köra anpassade SQL-instruktioner
Mobile Apps-SDK tillåter åtkomst till hela kontexten via request-objektet. Du kan enkelt köra SQL-instruktioner till definierade DataProvider:

    var api = {
        get: function (request, response, next) {
            // Check for parameters. If not there, pass on to a later API call.
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query. Anything that the mssql
            // driver can handle is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query. The context for Mobile Apps is available through
            // request.azureMobile. The data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Felsökning, enkelt tabeller och enkelt API: er
### <a name="howto-diagnostic-logs"></a>Felsök, diagnostisera och felsöka Mobile Apps
Azure App Service tillhandahåller flera felsökning och felsökningstekniker för Node.js-program.
Börja felsöka dina Node.js Mobile Apps-serverdel finns i följande artiklar:

* [Övervaka Azure App Service]
* [Aktivera diagnostik loggning i Azure App Service]
* [Felsöka Azure Apptjänst i Visual Studio]

Node.js-program har åtkomst till en mängd olika verktyg diagnostiska loggen. Mobile Apps Node.js SDK använder internt, [Winston] för diagnostikloggning. Loggning aktiveras automatiskt när du aktiverar debug läge eller anger den `MS_DebugMode` appinställningen till true i den [Azure-portalen]. Genererade loggar visas i diagnostikloggar i den [Azure-portalen].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Arbeta med enkelt tabeller i Azure-portalen
Du kan använda enkel tabeller för att skapa och arbeta med tabeller direkt i portalen. Du kan ladda upp datauppsättningen enkelt tabeller i CSV-format. Observera att du inte använda egenskapsnamn (i din CSV-dataset) som står i konflikt med systemet egenskapsnamnen för Mobile Apps-serverdel. System-egenskapsnamnen är:
* createdAt
* updatedAt
* borttaget
* version

Du kan även redigera åtgärder för tabeller med hjälp av App Service-redigerare. När du väljer **enkelt tabeller** i inställningarna för backend-webbplatsen du lägga till, ändra eller ta bort en tabell. Du kan också se data i tabellen.

![Arbeta med enkelt tabeller](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Följande kommandon är tillgängliga i kommandofältet för en tabell:

* **Ändra behörigheter**: ändra behörigheten för läsning, infoga, uppdatera och ta bort tabellen.
 Alternativen är att tillåta anonym åtkomst, att kräva autentisering eller inaktivera all åtkomst till åtgärden.
* **Redigera skriptet**: skriptfilen för tabellen öppnas i App Service Editor.
* **Hantera schemat**: lägga till eller ta bort kolumner eller ändra Tabellindex.
* **Rensa tabellen**: trunkera en befintlig tabell genom att ta bort alla rader med data, men lämna schemat oförändrade.
* **Ta bort rader**: ta bort enskilda rader med data.
* **Visa direktuppspelningsloggar**: ansluta till strömmande Loggtjänsten för webbplatsen.

### <a name="work-easy-apis"></a>Arbeta med enkla API: er i Azure-portalen
Du kan använda enkla API: er för att skapa och arbeta med anpassade API: er direkt i portalen. Du kan redigera API-skript med hjälp av App Service-redigerare.

När du väljer **enkelt API: er** i inställningarna för backend-webbplatsen du lägga till, ändra eller ta bort en anpassad API-slutpunkt.

![Arbeta med enkla API: er](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

I portalen du ändra åtkomstbehörighet för en HTTP-åtgärd, redigera skriptfilen API i App Service Editor eller visa strömmande loggarna.

### <a name="online-editor"></a>Redigera koden i App Service Editor
Du kan redigera din Node.js backend-filer i App Service Editor utan att behöva hämta projektet till den lokala datorn med hjälp av Azure portal. Redigera filer i redigeraren online:

1. I fönstret för Mobile Apps serverdel, väljer du **alla inställningar** > antingen **enkelt tabeller** eller **enkelt API: er**. Markera en tabell eller API och välj sedan **redigera skriptet**. Skriptfilen öppnas i App Service Editor.

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
[Konfigurera Azure Active Directory-autentisering]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurera Facebook-autentisering]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurera Google-autentisering]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Konfigurera Microsoft-autentisering]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurera Twitter-autentisering]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Distributionsguide för Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Övervaka Azure App Service]: ../app-service/web-sites-monitor.md
[Aktivera diagnostik loggning i Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Felsöka Azure Apptjänst i Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[ange nod-version]: ../nodejs-specify-node-version-azure-apps.md
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
[ExpressJS mellanprogram]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

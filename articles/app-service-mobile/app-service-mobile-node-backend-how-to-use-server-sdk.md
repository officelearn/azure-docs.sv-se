---
title: Så här arbetar du med Node. js backend server SDK för Mobile Apps | Microsoft Docs
description: Lär dig hur du arbetar med Node. js backend server SDK för Azure App Service Mobile Apps.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 74a522f8761c2eeaf329c90ae35aef0f44c40254
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027203"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Använda Mobile Apps Node. js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

Den här artikeln innehåller detaljerad information och exempel som visar hur du arbetar med en Node. js-Server del i Mobile Apps funktionen i Azure App Service.

## <a name="Introduction"></a>Introduktion

Mobile Apps ger möjlighet att lägga till en Mobile-optimerad webb-API för data åtkomst i ett webb program. Mobile Apps SDK tillhandahålls för ASP.NET-och Node. js-webbprogram. SDK: n tillhandahåller följande åtgärder:

* Tabell åtgärder (läsa, infoga, uppdatera, ta bort) för data åtkomst
* Anpassade API-åtgärder

Båda åtgärderna tillhandahåller autentisering över alla identitets leverantörer som Azure App Service tillåter. Dessa leverantörer inkluderar sociala identitets leverantörer som Facebook, Twitter, Google och Microsoft, samt Azure Active Directory för företags identitet.

Du kan hitta exempel för varje användnings fall i [exempel katalog på GitHub].

## <a name="supported-platforms"></a>Plattformar som stöds

Mobile Apps Node. js SDK stöder den aktuella LTS-versionen av Node och senare. För närvarande är den senaste LTS-versionen Node v-4.5.0. Andra versioner av noden kan fungera men stöds inte.

Mobile Apps Node. js SDK har stöd för två databas driv rutiner: 

* Node-MSSQL-drivrutinen stöder Azure SQL Database och lokala SQL Server instanser.  
* Sqlite3-drivrutinen stöder bara SQLite-databaser på en enda instans.

### <a name="howto-cmdline-basicapp"></a>Skapa en grundläggande Node. js-Server del med hjälp av kommando raden

Varje Mobile Apps Node. js-backend startar som ett ExpressJS-program. ExpressJS är det mest populära webb tjänst ramverket som är tillgängligt för Node. js. Du kan skapa ett grundläggande [Express] -program på följande sätt:

1. Skapa en katalog för ditt projekt i ett kommando-eller PowerShell-fönster:

        mkdir basicapp

1. Kör `npm init` för att initiera paket strukturen:

        cd basicapp
        npm init

   Kommandot `npm init` ställer en uppsättning frågor för att initiera projektet. Se exempel på utdata:

   ![NPM init-utdata][0]

1. Installera `express`-och `azure-mobile-apps`-biblioteken från NPM-lagringsplatsen:

        npm install --save express azure-mobile-apps

1. Skapa en app. js-fil för att implementera den grundläggande mobila servern:

    ```javascript
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
    ```

Det här programmet skapar ett mobilt optimerat webb-API med en enda slut punkt (`/tables/TodoItem`) som ger oautentiserad åtkomst till ett underliggande SQL-datalager med hjälp av ett dynamiskt schema. Det är lämpligt att följa snabb starter för klient bibliotek:

* [Snabb start för Android-klient]
* [Apache Cordova-klient snabb start]
* [snabb start för iOS-klient]
* [Snabb start för Windows Store-klient]
* [Snabb start för Xamarin. iOS-klient]
* [Snabb start för Xamarin. Android-klient]
* [Snabb start för Xamarin. Forms-klient]

Du hittar koden för det här grundläggande programmet i basicapp- [basicapp-exempel på GitHub].

### <a name="howto-vs2015-basicapp"></a>Skapa en Node. js-Server del med hjälp av Visual Studio 2015

Visual Studio 2015 kräver ett tillägg för att utveckla Node. js-program i IDE. Starta genom att installera [Node. js-verktyg 1,1 för Visual Studio]. När du är klar med installationen skapar du ett Express 4. x-program:

1. Öppna dialog rutan **nytt projekt** (från **fil** > **nytt** > **projekt**).
1. Expandera **mallar** > **JavaScript**- > **Node. js**.
1. Välj **grundläggande Azure Node. js Express 4-program**.
1. Fyll i projekt namnet. Välj **OK**.

   ![Nytt projekt i Visual Studio 2015][1]
1. Högerklicka på **NPM** -noden och välj **installera nya NPM-paket**.
1. Du kan behöva uppdatera NPM-katalogen när du har skapat ditt första Node. js-program. Välj **Uppdatera** vid behov.
1. Ange **Azure-Mobile-Apps** i sökrutan. Välj paketet **Azure-Mobile-Apps 2.0.0** och välj sedan **installera paket**.

   ![Installera nya NPM-paket][2]
1. Välj **Stäng**.
1. Öppna filen app. js för att lägga till stöd för Mobile Apps SDK. Lägg till följande kod på rad 6 längst ned i biblioteks-`require`-satserna:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Lägg till följande kod på ungefär rad 27 efter de andra `app.use`-satserna:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Spara filen.

1. Kör programmet lokalt (API: et behandlas på `http://localhost:3000`) eller publicera till Azure.

### <a name="create-node-backend-portal"></a>Skapa en Node. js-Server del med hjälp av Azure Portal

Du kan skapa en Mobile Apps Server del direkt i [Azure Portal]. Du kan antingen utföra följande steg eller skapa en klient och Server tillsammans genom att följa själv studie kursen [skapa en mobilapp](app-service-mobile-ios-get-started.md) . I självstudien finns en förenklad version av dessa instruktioner och det är bäst att använda POC-projekt.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Gå tillbaka till fönstret **Kom igång** , under **skapa ett tabell-API**väljer du **Node. js** som server dels språk.
Markera rutan för **Jag bekräftar att detta kommer att skriva över allt webbplats innehåll**och välj sedan **skapa TodoItem-tabell**.

### <a name="download-quickstart"></a>Hämta snabb starts kod projektet Node. js med hjälp av git

När du skapar en Node. js-Mobile Apps Server delen med hjälp av portalens **snabb starts** fönster, skapas ett Node. js-projekt för dig och distribueras till din webbplats. I portalen kan du lägga till tabeller och API: er och redigera kodfragment för Node. js-Server delen. Du kan också använda olika distributions verktyg för att ladda ned Server dels projektet så att du kan lägga till eller ändra tabeller och API: er och sedan publicera projektet igen. Mer information finns i [Azure App Service distributions guide].

I följande procedur används en git-lagringsplats för att ladda ned snabb starts koden:

1. Installera Git om du inte redan gjort det. De steg som krävs för att installera git varierar mellan olika operativ system. Information om distributioner och installations anvisningar för operativ system finns i [Installera git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Se [förbereda lagrings](../app-service/deploy-local-git.md#prepare-your-repository) platsen för att aktivera git-lagringsplatsen för backend-platsen. Anteckna distributionens användar namn och lösen ord.
3. I fönstret för Mobile Apps Server del noterar du URL-inställningen för git- **kloningen** .
4. Kör kommandot `git clone` med hjälp av Git-klon-URL: en. Ange ditt lösen ord vid behov, som i följande exempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Bläddra till den lokala katalogen (`/todolist` i föregående exempel) och Lägg märke till att projektfiler har hämtats. Leta upp filen todoitem. json i katalogen `/tables`. Den här filen definierar behörigheter för tabellen. Hitta också filen todoitem. js i samma katalog. Den definierar åtgärds skripten för CRUD för tabellen.
6. När du har gjort ändringar i projektfiler kör du följande kommandon för att lägga till, allokera och sedan överföra ändringarna till platsen:

        $ git commit -m "updated the table script"
        $ git push origin master

   När du lägger till nya filer i projektet måste du först köra kommandot `git add .`.

Platsen publiceras om varje gång en ny uppsättning incheckningar skickas till platsen.

### <a name="howto-publish-to-azure"></a>Publicera Node. js-Server delen till Azure

Microsoft Azure innehåller många mekanismer för att publicera Mobile Apps Node. js-Server delen till Azure-tjänsten. Dessa mekanismer innehåller distributions verktyg som är integrerade i Visual Studio, kommando rads verktyg och kontinuerliga distributions alternativ baserat på käll kontroll. Mer information finns i [Azure App Service distributions guide].

Azure App Service har särskilda råd för Node. js-program som du bör granska innan du publicerar Server delen:

* Så här [Ange Node-version]
* [Använda Node-moduler]

### <a name="howto-enable-homepage"></a>Aktivera en start sida för ditt program

Många program är en kombination av webb-och mobilappar. Du kan använda ExpressJS-ramverket för att kombinera de två fasetterna. Ibland kanske du bara vill implementera ett mobil gränssnitt. Det är praktiskt att tillhandahålla en start sida för att säkerställa att App Service är igång och körs. Du kan antingen ange en egen start sida eller aktivera en tillfällig start sida. Om du vill aktivera en tillfällig start sida använder du följande kod för att instansiera Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Om du bara vill att det här alternativet ska vara tillgängligt när du utvecklar lokalt kan du lägga till den här inställningen i filen azureMobile. js.

## <a name="TableOperations"></a>Tabell åtgärder

Azure-Mobile-Apps Node. js Server SDK innehåller mekanismer för att visa data tabeller som lagras i Azure SQL Database som ett webb-API. Den tillhandahåller fem åtgärder:

| Åtgärd | Beskrivning |
| --- | --- |
| Hämta/tables/*TableName* |Hämta alla poster i tabellen. |
| Hämta/tables/*TableName*/: ID |Hämta en speciell post i tabellen. |
| PUBLICERA/tables/*TableName* |Skapa en post i tabellen. |
| KORRIGERA/tables/*TableName*/: ID |Uppdatera en post i tabellen. |
| TA bort/tables/*TableName*/: ID |Ta bort en post i tabellen. |

Detta webb-API stöder [OData] och utökar Table-schemat så att det stöder [offline-datasynkronisering].

### <a name="howto-dynamicschema"></a>Definiera tabeller med hjälp av ett dynamiskt schema

Innan du kan använda en tabell måste du definiera den. Du kan definiera tabeller genom att använda ett statiskt schema (där du definierar kolumner i schemat) eller dynamiskt (där SDK styr schemat baserat på inkommande begär Anden). Dessutom kan du kontrol lera vissa aspekter av webb-API: et genom att lägga till JavaScript-kod i definitionen.

Vi rekommenderar att du definierar varje tabell i en JavaScript-fil i katalogen `tables` och använder sedan metoden `tables.import()` för att importera tabellerna. Genom att utöka exemplet på Basic-app kan du justera filen app. js:

```javascript
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
```

Definiera tabellen i./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabeller använder ett dynamiskt schema som standard. Om du vill stänga av det dynamiska schemat globalt ställer du in appen för `MS_DynamicSchema` på falskt i Azure Portal.

Du hittar ett fullständigt exempel i att göra- [att göra exempel på GitHub].

### <a name="howto-staticschema"></a>Definiera tabeller med hjälp av ett statiskt schema

Du kan uttryckligen definiera vilka kolumner som ska exponeras via webb-API: et. Azure-Mobile-Apps Node. js SDK lägger automatiskt till eventuella extra kolumner som krävs för datasynkronisering offline i den lista som du anger. Till exempel kräver snabb starts klient programmen en tabell med två kolumner: `text` (en sträng) och `complete` (ett booleskt värde).  
Tabellen kan definieras i tabell definitionens JavaScript-fil (finns i katalogen `tables`) enligt följande:

```javascript
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
```

Om du definierar tabeller statiskt måste du också anropa metoden `tables.initialize()` för att skapa databasschemat vid start. Metoden `tables.initialize()` returnerar ett [Löften] så att webb tjänsten inte hanterar begär Anden innan databasen initieras.

### <a name="howto-sqlexpress-setup"></a>Använd SQL Server Express som utvecklings data lager på din lokala dator

Mobile Apps Node. js SDK tillhandahåller tre alternativ för att betjäna data från rutan:

* Använd **minnes** driv rutinen för att tillhandahålla ett icke-beständigt exempel lager.
* Använd **MSSQL** -drivrutinen för att tillhandahålla en SQL Server Express data lager för utveckling.
* Använd **MSSQL** -drivrutinen för att tillhandahålla en Azure SQL Database data lager för produktion.

I Mobile Apps Node. js SDK används [MSSQL Node. js-paket] för att upprätta och använda en anslutning till både SQL Server Express och SQL Database. Det här paketet kräver att du aktiverar TCP-anslutningar på SQL Server Express-instansen.

> [!TIP]
> Minnes driv rutinen tillhandahåller inte en fullständig uppsättning anläggningar för testning. Om du vill testa Server delen lokalt rekommenderar vi att du använder ett SQL Server Express data lager och MSSQL-drivrutinen.

1. Hämta och installera [Microsoft SQL Server 2014 Express]. Se till att du installerar SQL Server 2014 Express med tools Edition. Om du inte uttryckligen behöver 64-bitars stöd förbrukar 32-bitars versionen mindre minne när den körs.
1. Kör SQL Server 2014 Configuration Manager:

   a. Expandera noden **SQL Server nätverks konfiguration** på träd-menyn.

   b. Välj **protokoll för SQLExpress**.

   c. Högerklicka på **TCP/IP** och välj **Aktivera**. Välj **OK** i dialog rutan popup-fönster.

   d. Högerklicka på **TCP/IP** och välj **Egenskaper**.

   e. Välj fliken **IP-adresser** .

   f. Hitta **IPAll** -noden. I fältet **TCP-port** anger du **1433**.

      ![Konfigurera SQL Server Express för TCP/IP][3]

   g. Välj **OK**. Välj **OK** i dialog rutan popup-fönster.

   h. Välj **SQL Server tjänster** på träd menyn.

   i. Högerklicka på **SQL Server (SQLExpress)** och välj **starta om**.

   j. Stäng SQL Server 2014 Configuration Manager.

1. Kör SQL Server 2014 Management Studio och Anslut till din lokala SQL Server Express-instans:

   1. Högerklicka på din instans i Object Explorer och välj **Egenskaper**.
   1. Välj sidan **säkerhet** .
   1. Kontrol lera att **SQL Server och Windows-autentiseringsläge** är markerat.
   1. Välj **OK**.

      ![Konfigurera SQL Server Express autentisering][4]
   1. Expandera **säkerhets** > **inloggningar** i Object Explorer.
   1. Högerklicka på **inloggningar** och välj **ny inloggning**.
   1. Ange ett inloggnings namn. Välj **SQL Server-autentisering**. Ange ett lösen ord och ange sedan samma lösen ord i **Bekräfta lösen ord**. Lösen ordet måste uppfylla kraven för Windows-komplexitet.
   1. Välj **OK**.

      ![Lägg till en ny användare i SQL Server Express][5]
   1. Högerklicka på den nya inloggningen och välj **Egenskaper**.
   1. Välj sidan **Server roller** .
   1. Markera kryss rutan för **dbcreator** -serverrollen.
   1. Välj **OK**.
   1. Stäng SQL Server 2015 Management Studio.

Se till att du registrerar det användar namn och lösen ord som du har valt. Du kan behöva tilldela ytterligare Server roller eller behörigheter, beroende på dina databas krav.

Node. js-programmet läser variabeln `SQLCONNSTR_MS_TableConnectionString` för anslutnings strängen för den här databasen. Du kan ställa in den här variabeln i din miljö. Du kan till exempel använda PowerShell för att ställa in miljövariabeln:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Få åtkomst till databasen via en TCP/IP-anslutning. Ange ett användar namn och lösen ord för anslutningen.

### <a name="howto-config-localdev"></a>Konfigurera ditt projekt för lokal utveckling

Mobile Apps läser en JavaScript-fil med namnet *azureMobile. js* från det lokala fil systemet. Använd inte den här filen för att konfigurera Mobile Apps SDK i produktion. Använd i stället **appinställningar** i [Azure Portal].

AzureMobile. js-filen bör exportera ett konfigurations objekt. De vanligaste inställningarna är:

* Databas inställningar
* Inställningar för diagnostisk loggning
* Alternativa CORS-inställningar

I det här exemplet **azureMobile. js** -filen implementeras föregående databas inställningar:

```javascript
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
```

Vi rekommenderar att du lägger till **azureMobile. js** i **. gitignore** -filen (eller annan käll kods kontroll ignorera fil) för att förhindra att lösen ord lagras i molnet. Konfigurera alltid produktions inställningar i **appinställningar** inom [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurera appinställningar för mobilappen

De flesta inställningar i filen azureMobile. js har en motsvarande app-inställning i [Azure Portal]. Använd följande lista för att konfigurera din app i **appinställningar**:

| App-inställning | azureMobile. js-inställning | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Appens namn |sträng |
| **MS_MobileLoggingLevel** |loggning. nivå |Lägsta logg nivå för meddelanden som ska loggas |fel, varning, info, verbose, debug, Silly |
| **MS_DebugMode** |förbigå |Aktiverar eller inaktiverar fel söknings läge |true, false |
| **MS_TableSchema** |data.schema |Standard schema namn för SQL-tabeller |sträng (standard: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktiverar eller inaktiverar fel söknings läge |true, false |
| **MS_DisableVersionHeader** |version (inställt på odefinierad) |Inaktiverar huvudet X-ZUMO-Server-version |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Inaktiverar kontroll av klientens API-version |true, false |

Ange en app-inställning:

1. Logga in på [Azure Portal].
1. Välj **alla resurser** eller **app Services**och välj sedan namnet på din mobilapp.
1. Fönstret **Inställningar** öppnas som standard. Om den inte är det väljer du **Inställningar**.
1. Välj **program inställningar**på menyn **Allmänt** .
1. Bläddra till avsnittet **app-inställningar** .
1. Om din app-inställning redan finns väljer du värdet för appens inställning för att redigera värdet.
   Om din app-inställning inte finns anger du appens inställning i rutan **nyckel** och värdet i rutan **värde** .
1. Välj **Spara**.

Om du ändrar de flesta appinställningar måste du starta om tjänsten.

### <a name="howto-use-sqlazure"></a>Använd SQL Database som produktions data lager

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Att använda Azure SQL Database som ett data lager är identiskt för alla Azure App Service program typer. Om du inte redan har gjort det följer du stegen nedan för att skapa en Mobile Apps Server del:

1. Logga in på [Azure Portal].
1. Klicka på knappen **+ ny** i det övre vänstra hörnet i fönstret för > **webb och mobilt** >- **mobilapp**och ange sedan ett namn för Mobile Apps Server delen.
1. I rutan **resurs grupp** anger du samma namn som din app.
1. Standard App Service plan väljs. Om du vill ändra App Service plan:

   a. Välj **App Service Plan** >  **+ Skapa nytt**.

   b. Ange ett namn på den nya App Service plan och välj en lämplig plats.

   c. Välj en lämplig pris nivå för tjänsten. Välj **Visa alla** för att visa fler pris alternativ, till exempel **kostnads fri** och **delad**.

   d. Klicka på knappen **Välj** .

   e. I fönstret **App Service plan** väljer du **OK**.
1. Välj **Skapa**.

Det kan ta några minuter att etablering av en Mobile Apps Server del. När Mobile Apps Server del har tillhandahållits, öppnar portalen fönstret **Inställningar** för Mobile Apps Server delen.

Du kan välja att antingen ansluta en befintlig SQL-databas till Mobile Apps Server del eller skapa en ny SQL-databas. I det här avsnittet skapar vi en SQL-databas.

> [!NOTE]
> Om du redan har en databas på samma plats som Mobile Apps Server del kan du istället välja **Använd en befintlig databas** och sedan välja den databasen. Vi rekommenderar inte att en databas används på en annan plats på grund av högre fördröjning.

1. I den nya Mobile Apps Server delen väljer du **inställningar** > **mobilapp** > **data** >  **+ Lägg till**.
1. I fönstret **Lägg till data anslutning** väljer du **SQL Database-konfigurera nödvändiga inställningar** > **skapa en ny databas**. Ange namnet på den nya databasen i rutan **namn** .
1. Välj **Server**. I fönstret **ny server** anger du ett unikt server namn i rutan **Server namn** och anger ett lämpligt inloggnings namn och lösen ord för Server administratören. Kontrol lera att **Tillåt Azure-tjänster för åtkomst till servern** är markerat. Välj **OK**.

   ![Skapa en Azure SQL-databas][6]
1. I fönstret **ny databas** väljer du **OK**.
1. Tillbaka i fönstret **Lägg till data anslutning** väljer du **anslutnings sträng**och anger inloggning och lösen ord som du angav när du skapade databasen. Om du använder en befintlig databas anger du inloggnings uppgifterna för den databasen. Välj **OK**.
1. Tillbaka i fönstret **Lägg till data anslutning** igen väljer du **OK** för att skapa databasen.

<!--- END OF ALTERNATE INCLUDE -->

Det kan ta några minuter att skapa databasen. Använd **aviserings** området för att övervaka förloppet för distributionen. Förloppet inte förrän databasen har distribuerats. När databasen har distribuerats skapas en anslutnings sträng för SQL Database-instansen i Mobile Apps Server dels inställningar för backend-appen. Du kan se den här appens inställning i **inställningar** > **program inställningar** > **anslutnings strängar**.

### <a name="howto-tables-auth"></a>Kräv autentisering för åtkomst till tabeller

Om du vill använda App Service autentisering med slut punkten `tables` måste du konfigurera App Service autentisering i [Azure Portal] först. Mer information finns i konfigurations guiden för den identitetsprovider som du tänker använda:

* [Konfigurera Azure Active Directory autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Varje tabell har en åtkomst egenskap som du kan använda för att kontrol lera åtkomsten till tabellen. I följande exempel visas en statiskt definierad tabell med autentisering krävs.

```javascript
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
```

Åtkomst egenskapen kan ha ett av tre värden:

* *Anonym* anger att klient programmet får läsa data utan autentisering.
* *autentiserad* anger att klient programmet måste skicka en giltig autentiseringstoken med begäran.
* *Disabled* anger att den här tabellen är inaktive rad för tillfället.

Om åtkomst egenskapen är odefinierad tillåts oautentiserad åtkomst.

### <a name="howto-tables-getidentity"></a>Använda autentiserings anspråk med dina tabeller
Du kan ställa in olika anspråk som begärs när autentisering har kon figurer ATS. Dessa anspråk är normalt inte tillgängliga via `context.user`-objektet. Du kan dock hämta dem med hjälp av metoden `context.user.getIdentity()`. Metoden `getIdentity()` returnerar ett löfte som matchar ett objekt. Objektet konfigureras av autentiseringsmetoden (`facebook`, `google`, `twitter`, `microsoftaccount` eller `aad`).

Om du till exempel konfigurerar Microsoft-konto autentisering och begär e-postadresserna anspråket kan du lägga till e-postadressen till posten med följande tabell kontroll enhet:

```javascript
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
```

Om du vill se vilka anspråk som är tillgängliga använder du en webbläsare för att Visa `/.auth/me`-slutpunkten för din webbplats.

### <a name="howto-tables-disabled"></a>Inaktivera åtkomst till vissa tabell åtgärder

Förutom att visas i tabellen kan åtkomst egenskapen användas för att styra enskilda åtgärder. Det finns fyra åtgärder:

* `read` är åtgärden för att hämta RESTful i tabellen.
* `insert` är RESTful POST-åtgärden i tabellen.
* `update` är RESTful KORRIGERINGs åtgärd för tabellen.
* `delete` är åtgärden för att ta bort RESTful i tabellen.

Du kanske till exempel vill tillhandahålla en skrivskyddad oautentiserad tabell:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Justera frågan som används med tabell åtgärder

Ett vanligt krav för tabell åtgärder är att tillhandahålla en begränsad vy av data. Du kan till exempel ange en tabell som är Taggad med det autentiserade användar-ID: t, så att du bara kan läsa eller uppdatera dina egna poster. Följande tabell definition innehåller den här funktionen:

```javascript
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
```

Åtgärder som vanligt vis kör en fråga har en egenskap för fråga som du kan justera med hjälp av en `where`-sats. Egenskapen Query är ett [QueryJS] -objekt som används för att konvertera en OData-fråga till något som kan utföras av data server delen. För enkla likhets fall (precis som föregående) kan du använda en karta. Du kan också lägga till vissa SQL-satser:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurera en mjuk borttagning för en tabell

En mjuk borttagning tar egentligen inte bort poster. I stället markeras de som borttagna i databasen genom att ange den borttagna kolumnen till sant. Mobile Apps SDK tar automatiskt bort rader som inte tagits bort från resultatet om inte SDK: n för mobil klient använder `IncludeDeleted()`. Om du vill konfigurera en tabell för en mjuk borttagning anger du egenskapen `softDelete` i tabell definitions filen:

```javascript
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
```

Du bör upprätta en mekanism för att ta bort poster: ett klient program, ett webb jobb, en Azure-funktion eller ett anpassat API.

### <a name="howto-tables-seeding"></a>Dirigera databasen med data

När du skapar ett nytt program kanske du vill dirigera en tabell med data. Du kan göra detta inom tabell definitionens JavaScript-fil på följande sätt:

```javascript
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
```

Dirigering av data sker bara när du har använt Mobile Apps SDK för att skapa tabellen. Om tabellen redan finns i databasen, matas inga data in i tabellen. Om det dynamiska schemat är påslaget härleds schemat från de data som har dirigerats.

Vi rekommenderar att du explicit anropar metoden `tables.initialize()` för att skapa tabellen när tjänsten börjar köras.

### <a name="Swagger"></a>Aktivera stöd för Swagger
Mobile Apps levereras med inbyggt stöd för [Swagger] . Om du vill aktivera Swagger-stöd måste du först installera Swagger-UI som ett beroende:

    npm install --save swagger-ui

Du kan sedan aktivera stöd för Swagger i Mobile Apps-konstruktorn:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Du vill förmodligen bara aktivera Swagger support i utvecklings versioner. Du kan göra detta med hjälp av app-inställningen `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

@No__t-0-slutpunkten finns på http://*yoursite*. azurewebsites.net/Swagger. Du kan komma åt Swagger-ANVÄNDARGRÄNSSNITTET via slut punkten för `/swagger/ui`. Om du väljer att kräva autentisering i hela programmet ger Swagger ett fel. För bästa resultat väljer du att tillåta oautentiserade begär anden i Azure App Service autentiserings-/auktoriseringsdata och kontrollerar sedan autentiseringen med hjälp av egenskapen `table.access`.

Du kan också lägga till alternativet Swagger i filen azureMobile. js om du bara vill att Swagger-supporten ska utvecklas lokalt.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Push-meddelanden

Mobile Apps integreras med Azure Notification Hubs så att du kan skicka riktade push-meddelanden till miljon tals enheter över alla större plattformar. Med hjälp av Notification Hubs kan du skicka push-meddelanden till iOS-, Android-och Windows-enheter. Mer information om allt som du kan göra med Notification Hubs finns i [Översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Skicka push-meddelanden

Följande kod visar hur du använder objektet `push` för att skicka ett sändnings-push-meddelande till registrerade iOS-enheter:

```javascript
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
```

Genom att skapa en mall för push-registrering från klienten kan du i stället skicka ett mall-push-meddelande till enheter på alla plattformar som stöds. Följande kod visar hur du skickar ett meddelande till en mall:

```javascript
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
```

### <a name="push-user"></a>Skicka push-meddelanden till en autentiserad användare med hjälp av Taggar
När en autentiserad användare registrerar sig för push-meddelanden läggs en tagg för användar-ID automatiskt till i registreringen. Genom att använda den här taggen kan du skicka push-meddelanden till alla enheter som registrerats av en speciell användare. Följande kod hämtar SID för användaren som gör begäran och skickar ett push-meddelande för en mall till varje enhets registrering för den användaren:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

När du registrerar dig för push-meddelanden från en autentiserad klient kontrollerar du att autentiseringen är klar innan du försöker registrera dig.

## <a name="CustomAPI"></a>Anpassade API: er

### <a name="howto-customapi-basic"></a>Definiera ett anpassat API

Förutom data åtkomst-API: et via `/tables`-slutpunkten kan Mobile Apps tillhandahålla anpassad API-täckning. Anpassade API: er definieras på liknande sätt som tabell definitioner och har åtkomst till alla funktioner, inklusive autentisering.

Om du vill använda App Service autentisering med ett anpassat API måste du konfigurera App Service autentisering i [Azure Portal] först. Mer information finns i konfigurations guiden för den identitetsprovider som du tänker använda:

* [Konfigurera Azure Active Directory autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Anpassade API: er definieras på ungefär samma sätt som tabellerna API:

1. Skapa en `api`-katalog.
1. Skapa en JavaScript-definition JavaScript-fil i katalogen `api`.
1. Använd import metoden för att importera `api`-katalogen.

Här är en prototyp-API-definition baserad på det Basic-app-exempel som vi använde tidigare:

```javascript
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
```

Vi tar ett exempel-API som returnerar Server datumet genom att använda metoden `Date.now()`. Här är API/datum. js-filen:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Varje parameter är en av standard RESTful-verben: Hämta, skicka, korrigera eller ta bort. Metoden är en standard [ExpressJS mellanprogram] funktioner som skickar nödvändiga utdata.

### <a name="howto-customapi-auth"></a>Kräv autentisering för åtkomst till ett anpassat API

Mobile Apps SDK implementerar autentisering på samma sätt för både `tables`-slutpunkten och anpassade API: er. Om du vill lägga till autentisering i det API som utvecklades i föregående avsnitt lägger du till en `access`-egenskap:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Du kan också ange autentisering för vissa åtgärder:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Samma token som används för `tables`-slutpunkten måste användas för anpassade API: er som kräver autentisering.

### <a name="howto-customapi-auth"></a>Hantera stora fil överföringar

I Mobile Apps SDK används [mellanliggande Body-parser](https://github.com/expressjs/body-parser) för att godkänna och avkoda innehåll i ditt bidrag. Du kan förkonfigurera Body-parser för att ta emot större fil överföringar:

```javascript
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
```

Filen är Base-64-kodad före överföring. Den här kodningen ökar storleken på den faktiska överföringen (och den storlek som du måste ha som konto).

### <a name="howto-customapi-sql"></a>Köra anpassade SQL-uttryck

Mobile Apps SDK tillåter åtkomst till hela kontexten via objektet Request. Du kan enkelt köra parametriserade SQL-uttryck till den definierade data leverantören:

```javascript
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
```

## <a name="Debugging"></a>Fel sökning

### <a name="howto-diagnostic-logs"></a>Felsöka, diagnostisera och Felsök Mobile Apps

Azure App Service tillhandahåller flera fel söknings-och fel söknings tekniker för Node. js-program.
Information om hur du kommer igång med fel sökning av Node. js Mobile Apps Server del finns i följande artiklar:

* [Övervaknings Azure App Service]
* [Aktivera diagnostisk loggning i Azure App Service]
* [Felsöka Azure App Service i Visual Studio]

Node. js-program har till gång till en mängd diagnostiska logg verktyg. Internt använder Mobile Apps Node. js SDK [Winston] för diagnostisk loggning. Loggning aktive ras automatiskt när du aktiverar fel söknings läge eller anger appens inställning för `MS_DebugMode` till true i [Azure Portal]. Skapade loggar visas i diagnostikloggar i [Azure Portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Snabb start för Android-klient]: app-service-mobile-android-get-started.md
[Apache Cordova-klient snabb start]: app-service-mobile-cordova-get-started.md
[snabb start för iOS-klient]: app-service-mobile-ios-get-started.md
[Snabb start för Xamarin. iOS-klient]: app-service-mobile-xamarin-ios-get-started.md
[Snabb start för Xamarin. Android-klient]: app-service-mobile-xamarin-android-get-started.md
[Snabb start för Xamarin. Forms-klient]: app-service-mobile-xamarin-forms-get-started.md
[Snabb start för Windows Store-klient]: app-service-mobile-windows-store-dotnet-get-started.md
[offline-datasynkronisering]: app-service-mobile-offline-data-sync.md
[Konfigurera Azure Active Directory autentisering]: ../app-service/configure-authentication-provider-aad.md
[Konfigurera Facebook-autentisering]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurera Google-autentisering]: ../app-service/configure-authentication-provider-google.md
[Konfigurera Microsoft-autentisering]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurera Twitter-autentisering]: ../app-service/configure-authentication-provider-twitter.md
[Azure App Service distributions guide]: ../app-service/deploy-local-git.md
[Övervaknings Azure App Service]: ../app-service/web-sites-monitor.md
[Aktivera diagnostisk loggning i Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Felsöka Azure App Service i Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Ange Node-version]: ../nodejs-specify-node-version-azure-apps.md
[använda Node-moduler]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Löften]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp-exempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[att göra exempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[exempel katalog på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node. js-verktyg 1,1 för Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node. js-paket]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS mellanprogram]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

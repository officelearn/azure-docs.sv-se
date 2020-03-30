---
title: Arbeta med Node.js backend Server SDK
description: Lär dig hur du arbetar med Node.js backend Server SDK för Azure App Service Mobile Apps.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249950"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Så här använder du Mobile Apps Node.js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Den här artikeln innehåller detaljerad information och exempel som visar hur du arbetar med en nod.js-backend i funktionen Mobilappar i Azure App Service.

## <a name="introduction"></a><a name="Introduction"></a>Introduktion

Mobilappar ger möjlighet att lägga till ett mobiloptimerat webb-API för dataåtkomst i ett webbprogram. Mobile Apps SDK tillhandahålls för ASP.NET och Node.js webbapplikationer. SDK tillhandahåller följande åtgärder:

* Tabellåtgärder (läsa, infoga, uppdatera, ta bort) för dataåtkomst
* Anpassade API-åtgärder

Båda åtgärderna tillhandahåller autentisering för alla identitetsleverantörer som Azure App Service tillåter. Dessa leverantörer inkluderar leverantörer av sociala identiteter som Facebook, Twitter, Google och Microsoft samt Azure Active Directory för företagsidentitet.

Du kan hitta exempel för varje användningsfall i [exempelkatalogen på GitHub].

## <a name="supported-platforms"></a>Plattformar som stöds

Mobile Apps Node.js SDK stöder den aktuella LTS-versionen av Nod och senare. För närvarande är den senaste LTS-versionen Node v4.5.0. Andra versioner av Nod kan fungera men stöds inte.

Mobile Apps Node.js SDK stöder två databasdrivrutiner: 

* Nod-mssql-drivrutinen stöder Azure SQL Database och lokala SQL Server-instanser.  
* Sqlite3-drivrutinen stöder endast SQLite-databaser på en enda instans.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Skapa en enkel nod.js-backdel med hjälp av kommandoraden

Varje Mobile Apps Node.js back end börjar som ett ExpressJS-program. ExpressJS är det mest populära webbtjänstramverket som är tillgängligt för Node.js. Du kan skapa ett grundläggande [Express-program] på följande sätt:

1. Skapa en katalog för projektet i ett kommando- eller PowerShell-fönster:

        mkdir basicapp

1. Kör `npm init` för att initiera paketstrukturen:

        cd basicapp
        npm init

   Kommandot `npm init` ställer en uppsättning frågor för att initiera projektet. Se exempelutdata:

   ![Npm init-utgången][0]

1. Installera `express` biblioteken och `azure-mobile-apps` biblioteken från npm-databasen:

        npm install --save express azure-mobile-apps

1. Skapa en app.js-fil för att implementera den grundläggande mobila servern:

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

Det här programmet skapar ett mobiloptimerat webb-API med en enda slutpunkt (`/tables/TodoItem`) som ger oautentiserade åtkomst till ett underliggande SQL-datalager med hjälp av ett dynamiskt schema. Den är lämplig för att följa klientbibliotekets snabbstarter:

* [Snabbstart för Android-klient]
* [Apache Cordova klient snabbstart]
* [snabbstart för iOS-klient]
* [Snabbstart för Windows Store-klient]
* [Xamarin.iOS-klient snabbstart]
* [Xamarin.Android-klientens snabbstart]
* [Xamarin.Forms klient snabbstart]

Du hittar koden för det här grundläggande programmet i [exemplet basicapp på GitHub].

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Skapa en nod.js-backdel med Visual Studio 2015

Visual Studio 2015 kräver ett tillägg för att utveckla Node.js-program inom IDE. Starta genom att installera [Node.js Tools 1.1 för Visual Studio]. När du är klar med installationen skapar du ett Express 4.x-program:

1. Öppna dialogrutan **Nytt projekt** (från **Arkiv** > **nytt** > **projekt**).
1. Expandera **mallar** > **JavaScript Node.js** > **Node.js**.
1. Välj **Grundläggande Azure Node.js Express 4-program**.
1. Fyll i projektnamnet. Välj **OK**.

   ![Visual Studio 2015 nytt projekt][1]
1. Högerklicka på **npm-noden** och välj **Installera nya npm-paket**.
1. Du kan behöva uppdatera npm-katalogen när du har skapat ditt första Node.js-program. Välj **Uppdatera** om det behövs.
1. Ange **azure-mobile-apps** i sökrutan. Välj **azure-mobile-apps 2.0.0-paketet** och välj sedan **Installera paket**.

   ![Installera nya npm-paket][2]
1. Välj **Stäng**.
1. Öppna filen app.js för att lägga till stöd för Mobile Apps SDK. Vid rad 6 längst ned `require` i biblioteksutdragen lägger du till följande kod:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Vid ungefär rad 27 `app.use` efter de andra uttalandena lägger du till följande kod:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Spara filen.

1. Kör antingen programmet lokalt (API:et visas på) `http://localhost:3000`eller publicera till Azure.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Skapa en nod.js-backdel med hjälp av Azure-portalen

Du kan skapa en mobile apps-backend direkt i [Azure-portalen]. Du kan antingen utföra följande steg eller skapa en klient och server tillsammans genom att följa självstudien [Skapa en mobilapp.](app-service-mobile-ios-get-started.md) Handledningen innehåller en förenklad version av dessa instruktioner och är bäst för proof-of-concept-projekt.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Tillbaka i fönstret **Kom igång** under Skapa **ett tabell-API**väljer du **Nod.js** som serverdelsspråk.
Markera rutan för **jag bekräftar att detta kommer att skriva över allt webbplatsinnehåll**och välj sedan **Skapa TodoItem-tabell**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>Hämta snabbstartskodprojektet node.js med hjälp av Git

När du skapar en nod.js Mobile Apps-backend med hjälp av portalens **snabbstartsfönster** skapas ett Node.js-projekt åt dig och distribueras till din webbplats. I portalen kan du lägga till tabeller och API:er och redigera kodfiler för nod.js-backend. Du kan också använda olika distributionsverktyg för att hämta backend-projektet så att du kan lägga till eller ändra tabeller och API:er och sedan publicera om projektet. Mer information finns i [distributionsguiden för Azure App Service].

I följande procedur används en Git-databas för att hämta snabbstartsprojektkoden:

1. Installera Git, om du inte redan har gjort det. Stegen som krävs för att installera Git varierar mellan operativsystem. För operativsystemspecifika distributioner och installationsvägledning finns i [Installera Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Se [Förbereda databasen](../app-service/deploy-local-git.md#prepare-your-repository) för att aktivera Git-databasen för backend-webbplatsen. Anteckna användarnamn och lösenord för distributionen.
3. I fönstret för din bärbara mobilappars baksida noterar du **url-inställningen för Git-klon.**
4. Kör `git clone` kommandot med hjälp av Git-klon-URL:en. Ange ditt lösenord vid behov, som i följande exempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Bläddra till den`/todolist` lokala katalogen (i föregående exempel) och observera att projektfiler har hämtats. Leta reda på filen todoitem.json i katalogen. `/tables` Den här filen definierar behörigheter i tabellen. Hitta också todoitem.js-filen i samma katalog. Den definierar CRUD-åtgärdsskripten för tabellen.
6. När du har gjort ändringar i projektfiler kör du följande kommandon för att lägga till, genomföra och sedan överföra ändringarna till webbplatsen:

        $ git commit -m "updated the table script"
        $ git push origin master

   När du lägger till nya filer i `git add .` projektet måste du först köra kommandot.

Webbplatsen publiceras på nytt varje gång en ny uppsättning commits skjuts till platsen.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>Publicera din Node.js-backend till Azure

Microsoft Azure innehåller många mekanismer för att publicera din Mobile Apps Node.js-backend till Azure-tjänsten. Dessa mekanismer inkluderar distributionsverktyg som är integrerade i Visual Studio, kommandoradsverktyg och alternativ för kontinuerlig distribution baserat på källkontroll. Mer information finns i [distributionsguiden för Azure App Service].

Azure App Service har specifika råd för Node.js-program som du bör granska innan du publicerar backend:Azure App Service has specific advice for Node.js applications that you should review before you publish the back end:

* Så här [anger du nodversionen]
* Så här [använder du nodmoduler]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Aktivera en startsida för ditt program

Många program är en kombination av webb- och mobilappar. Du kan använda ExpressJS-ramverket för att kombinera de två aspekterna. Ibland kanske du bara vill implementera ett mobilt gränssnitt. Det är användbart att tillhandahålla en startsida för att säkerställa att apptjänsten är igång. Du kan antingen ange din egen hemsida eller aktivera en tillfällig startsida. Om du vill aktivera en tillfällig startsida använder du följande kod för att instansiera mobilappar:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Om du bara vill att det här alternativet ska vara tillgängligt när du utvecklar lokalt kan du lägga till den här inställningen i filen azureMobile.js.

## <a name="table-operations"></a><a name="TableOperations"></a>Tabellåtgärder

Azure-mobile-apps Node.js Server SDK tillhandahåller mekanismer för att exponera datatabeller som lagras i Azure SQL Database som ett webb-API. Det ger fem operationer:

| Åtgärd | Beskrivning |
| --- | --- |
| GET */tabeller/tabellnamn* |Få alla poster i tabellen. |
| GET /tabeller/*tabellnamn*/:id |Få en specifik post i tabellen. |
| POST */tabeller/tabellnamn* |Skapa en post i tabellen. |
| PATCH /tabeller/*tabellnamn*/:id |Uppdatera en post i tabellen. |
| DELETE /tables/*tabellnamn*/:id |Ta bort en post i tabellen. |

Det här webb-API:et stöder [OData] och utökar tabellschemat så att det stöder [offlinedatasynkronisering].

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Definiera tabeller med hjälp av ett dynamiskt schema

Innan du kan använda en tabell måste du definiera den. Du kan definiera tabeller med hjälp av ett statiskt schema (där du definierar kolumnerna i schemat) eller dynamiskt (där SDK styr schemat baserat på inkommande begäranden). Dessutom kan du styra specifika aspekter av webb-API:et genom att lägga till JavaScript-kod i definitionen.

Du bör också definiera varje tabell i en JavaScript-fil i `tables` `tables.import()` katalogen och sedan använda metoden för att importera tabellerna. Om du utökar exemplet med grundläggande appar justerar du filen app.js:

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

Definiera tabellen i ./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabeller använder som standard ett dynamiskt schema. Om du vill inaktivera det dynamiska `MS_DynamicSchema` schemat globalt anger du appinställningen till false i Azure-portalen.

Du hittar ett komplett exempel i [exemplet med att göra på GitHub].

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Definiera tabeller med hjälp av ett statiskt schema

Du kan uttryckligen definiera kolumnerna som ska visas via webb-API:et. Azure-mobile-apps Node.js SDK lägger automatiskt till eventuella extra kolumner som krävs för offlinedatasynkronisering i listan som du anger. Snabbstartsklientprogrammen kräver till exempel en `text` tabell med två `complete` kolumner: (en sträng) och (en boolesk).  
Tabellen kan definieras i tabelldefinitionen JavaScript-fil `tables` (finns i katalogen) på följande sätt:

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

Om du definierar tabeller statiskt `tables.initialize()` måste du också anropa metoden för att skapa databasschemat vid start. Metoden `tables.initialize()` returnerar ett [löfte] så att webbtjänsten inte betjänar begäranden innan databasen initieras.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Använda SQL Server Express som ett utvecklingsdatalager på din lokala dator

Mobile Apps Node.js SDK innehåller tre alternativ för att visa data direkt:

* Använd **minnesdrivrutinen** för att tillhandahålla ett icke-beständigt exempelarkiv.
* Använd **mssql-drivrutinen** för att tillhandahålla ett SQL Server Express-datalager för utveckling.
* Använd **mssql-drivrutinen** för att tillhandahålla ett Azure SQL Database-datalager för produktion.

Mobile Apps Node.js SDK använder [paketet mssql Node.js] för att upprätta och använda en anslutning till både SQL Server Express och SQL Database. Det här paketet kräver att du aktiverar TCP-anslutningar på SQL Server Express-instansen.

> [!TIP]
> Minnesdrivrutinen tillhandahåller inte en fullständig uppsättning anläggningar för testning. Om du vill testa serverdelen lokalt rekommenderar vi att du använder ett SQL Server Express-datalager och mssql-drivrutinen.

1. Ladda ned och installera [Microsoft SQL Server 2014 Express]. Se till att du installerar SQL Server 2014 Express med Tools edition. Om du inte uttryckligen behöver 64-bitars stöd förbrukar 32-bitarsversionen mindre minne när du kör.
1. Kör KONFIGURATIONSHANTERAREN FÖR SQL Server 2014:

   a. Expandera noden **SQL Server-nätverkskonfiguration** på träd-menyn.

   b. Välj **protokoll för SQLEXPRESS**.

   c. Högerklicka på **TCP/IP** och välj **Aktivera**. Välj **OK** i popup-dialogrutan.

   d. Högerklicka på **TCP/IP** och välj **Egenskaper**.

   e. Välj fliken **IP-adresser.**

   f. Leta reda på **IPAll-noden.** Ange **1433**i fältet **TCP-port** .

      ![Konfigurera SQL Server Express för TCP/IP][3]

   g. Välj **OK**. Välj **OK** i popup-dialogrutan.

   h. Välj **SQL Server Services** på trädmenyn.

   i. Högerklicka på **SQL Server (SQLEXPRESS)** och välj **Starta om**.

   j. Stäng KONFIGURATIONSHANTERAREN FÖR SQL Server 2014.

1. Kör SQL Server 2014 Management Studio och anslut till din lokala SQL Server Express-instans:

   1. Högerklicka på förekomsten i Objektutforskaren och välj **Egenskaper**.
   1. Välj sidan **Säkerhet.**
   1. Kontrollera att **SQL Server- och Windows-autentiseringsläge** är markerat.
   1. Välj **OK**.

      ![Konfigurera SQL Server Express-autentisering][4]
   1. Expandera > **säkerhetsinloggningar** i Objektutforskaren. **Security**
   1. Högerklicka på **Inloggningar** och välj **Ny inloggning**.
   1. Ange ett inloggningsnamn. Välj **SQL Server-autentisering**. Ange ett lösenord och ange sedan samma lösenord i **Bekräfta lösenord**. Lösenordet måste uppfylla Windows-komplexitetskraven.
   1. Välj **OK**.

      ![Lägga till en ny användare i SQL Server Express][5]
   1. Högerklicka på din nya inloggning och välj **Egenskaper**.
   1. Välj sidan **Serverroller.**
   1. Markera kryssrutan för **dbcreator-serverrollen.**
   1. Välj **OK**.
   1. Stäng SQL Server 2015 Management Studio.

Var noga med att registrera användarnamn och lösenord som du har valt. Du kan behöva tilldela ytterligare serverroller eller behörigheter, beroende på databasens krav.

Node.js-programmet läser `SQLCONNSTR_MS_TableConnectionString` av miljövariabeln för anslutningssträngen för den här databasen. Du kan ställa in den här variabeln i din miljö. Du kan till exempel använda PowerShell för att ange den här miljövariabeln:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Öppna databasen via en TCP/IP-anslutning. Ange ett användarnamn och lösenord för anslutningen.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>Konfigurera projektet för lokal utveckling

Mobilappar läser en JavaScript-fil som heter *azureMobile.js* från det lokala filsystemet. Använd inte den här filen för att konfigurera Mobile Apps SDK i produktionen. Använd i stället **appinställningarna** i [Azure-portalen].

AzureMobile.js-filen ska exportera ett konfigurationsobjekt. De vanligaste inställningarna är:

* Databasinställningar
* Inställningar för diagnostikloggning
* Alternativa CORS-inställningar

Det här exemplet **azureMobile.js-filen** implementerar föregående databasinställningar:

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

Vi rekommenderar att du lägger till **azureMobile.js** i **din gitignore-fil** (eller annan källkodskontroll ignorera fil) för att förhindra att lösenord lagras i molnet. Konfigurera alltid produktionsinställningar i **appinställningarna** i [Azure-portalen].

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Konfigurera appinställningar för mobilappen

De flesta inställningar i filen azureMobile.js har en motsvarande appinställning i [Azure-portalen]. Använd följande lista för att konfigurera appen i **Appinställningar:**

| Appinställning | azureMobile.js-inställning | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |namn |Appens namn |sträng |
| **MS_MobileLoggingLevel** |loggning.nivå |Lägsta loggnivå för meddelanden att logga |fel, varning, info, utförlig, felsökning, dum |
| **MS_DebugMode** |felsökning |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_TableSchema** |data.schema |Standardschemanamn för SQL-tabeller |sträng (standard: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_DisableVersionHeader** |version (inställd på odefinierad) |Inaktiverar X-ZUMO-Server-Versionshuvudet |SANT, FALSKT |
| **MS_SkipVersionCheck** |hoppa över omcheckning |Inaktiverar kontrollen av klient-API-versionen |SANT, FALSKT |

Så här anger du en appinställning:

1. Logga in på [Azure-portalen].
1. Välj **Alla resurser** eller App **Services**och välj sedan namnet på mobilappen.
1. Fönstret **Inställningar** öppnas som standard. Om den inte gör det väljer du **Inställningar**.
1. Välj **Programinställningar**på MENYN **ALLMÄNT** .
1. Bläddra till avsnittet **Appinställningar.**
1. Om appinställningen redan finns väljer du värdet för appinställningen för att redigera värdet.
   Om appinställningen inte finns anger du appinställningen i rutan **Nyckel** och värdet i rutan **Värde.**
1. Välj **Spara**.

Om du ändrar de flesta appinställningar måste en omstart av tjänsten startas om.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Använda SQL Database som produktionsdatalager

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Att använda Azure SQL Database som datalager är identiskt mellan alla Azure App Service-programtyper. Om du inte redan har gjort det följer du dessa steg för att skapa en backdel för mobilappar:

1. Logga in på [Azure-portalen].
1. Längst upp till vänster i fönstret väljer du knappen **+NY** > **Web + Mobile** > **Mobile App**och anger sedan ett namn för din mobilappars baksida.
1. Ange samma namn som appen i rutan **Resursgrupp.**
1. Standardprogramtjänstplanen är markerad. Om du vill ändra apptjänstplanen:

   a. Välj **App serviceplan** > **+Skapa ny**.

   b. Ange ett namn på den nya App Service-planen och välj en lämplig plats.

   c. Välj en lämplig prisnivå för tjänsten. Välj **Visa alla** om du vill visa fler prisalternativ, till exempel **Kostnadsfria** och **Delade**.

   d. Klicka på knappen **Markera.**

   e. Välj **OK**i **fönstret App Service-plan.**
1. Välj **Skapa**.

Det kan ta ett par minuter att etablera en slutbetalning i mobilappar. När backend för mobilappar har etablerats öppnas fönstret **Inställningar** för backend för mobilappar.

Du kan välja att antingen ansluta en befintlig SQL-databas till din mobile apps-serverdel eller skapa en ny SQL-databas. I det här avsnittet skapar vi en SQL-databas.

> [!NOTE]
> Om du redan har en databas på samma plats som den bakre delen av mobilapparna kan du i stället välja **Använd en befintlig databas** och sedan välja databasen. Vi rekommenderar inte användning av en databas på en annan plats på grund av högre fördröjningar.

1. I den nya backend för mobilappar väljer du **Inställningar** > **Av mobilappsdata** > **Data** > **+Lägg till**.
1. I fönstret **Lägg till dataanslutning** väljer du **SQL Database - Konfigurera obligatoriska inställningar** > **Skapa en ny databas**. Ange namnet på den nya databasen i rutan **Namn.**
1. Välj **Server**. Ange ett unikt servernamn i rutan **Servernamn** i fönstret **Nytt servernamn** och ange en lämplig inloggning och lösenord för serveradministratör. Kontrollera att **Tillåt azure-tjänster för åtkomst till servern** är markerat. Välj **OK**.

   ![Skapa en Azure SQL-databas][6]
1. Välj **OK**i fönstret **Ny databas** .
1. Tillbaka i fönstret **Lägg till dataanslutning** väljer du **Anslutningssträng**och anger inloggningen och lösenordet som du angav när du skapade databasen. Om du använder en befintlig databas anger du inloggningsuppgifterna för databasen. Välj **OK**.
1. I fönstret **Lägg till dataanslutning** igen väljer du **OK** för att skapa databasen.

<!--- END OF ALTERNATE INCLUDE -->

Det kan ta några minuter att skapa databasen. Använd området **Meddelanden** för att övervaka hur distributionen fortskrider. Förloppet inte förrän databasen har distribuerats. När databasen har distribuerats skapas en anslutningssträng för SQL Database-instansen i backend-appinställningarna för mobilappar. Du kan se den här appinställningen i **Inställningar** > **Programinställningar** > **Anslutningssträngar**.

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Kräv autentisering för åtkomst till tabeller

Om du vill använda App `tables` Service-autentisering med slutpunkten måste du först konfigurera App Service-autentisering i [Azure-portalen.] Mer information finns i konfigurationsguiden för den identitetsprovider som du tänker använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Googles autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Varje tabell har en åtkomstegenskap som du kan använda för att styra åtkomsten till tabellen. Följande exempel visar en statiskt definierad tabell med autentisering krävs.

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

Åtkomstegenskapen kan ta ett av tre värden:

* *anonym* anger att klientprogrammet tillåts läsa data utan autentisering.
* *autentiseras* anger att klientprogrammet måste skicka en giltig autentiseringstoken med begäran.
* *inaktiverad* anger att den här tabellen är inaktiverad.

Om åtkomstegenskapen är odefinierad tillåts oautentiserade åtkomster.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Använda autentiseringsanspråk med tabellerna
Du kan ställa in olika anspråk som begärs när autentisering ställs in. Dessa anspråk är normalt `context.user` inte tillgängliga via objektet. Du kan dock hämta dem `context.user.getIdentity()` med hjälp av metoden. Metoden `getIdentity()` returnerar ett löfte som matchas till ett objekt. Objektet är knappat med autentiseringsmetoden `microsoftaccount`( `aad``facebook`, `google`, `twitter`, , eller ).

Om du till exempel konfigurerar autentisering av Microsoft-konton och begär anspråk på e-postadresser kan du lägga till e-postadressen i posten med följande tabellkontrollant:

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

Om du vill se vilka anspråk som `/.auth/me` är tillgängliga använder du en webbläsare för att visa slutpunkten för din webbplats.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Inaktivera åtkomst till specifika tabellåtgärder

Förutom att visas i tabellen kan åtkomstegenskapen användas för att styra enskilda operationer. Det finns fyra operationer:

* `read`är RESTful GET-funktionen på bordet.
* `insert`är den RESTful POST-funktionen som ligger på bordet.
* `update`är RESTful PATCH-funktionen på bordet.
* `delete`är funktionen RESTful DELETE på bordet.

Du kanske till exempel vill ange en skrivskyddad oautentiserade tabell:

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

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>Justera frågan som används med tabellåtgärder

Ett vanligt krav för tabellåtgärder är att tillhandahålla en begränsad vy av data. Du kan till exempel ange en tabell som är taggad med det autentiserade användar-ID:t så att du bara kan läsa eller uppdatera dina egna poster. Följande tabelldefinition innehåller följande funktioner:

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

Åtgärder som normalt kör en fråga har en frågeegenskap som du kan justera med hjälp av en `where` sats. Frågeegenskapen är ett [QueryJS-objekt] som används för att konvertera en OData-fråga till något som databack end kan bearbeta. För enkla jämställdhetsärenden (som föregående) kan du använda en karta. Du kan också lägga till specifika SQL-satser:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Konfigurera en mjuk borttagning i en tabell

En mjuk borttagning tar faktiskt inte bort poster. I stället markeras de som borttagna i databasen genom att ange den borttagna kolumnen till true. Mobile Apps SDK tar automatiskt bort borttagna poster från resultat om `IncludeDeleted()`inte Mobile Client SDK använder . Om du vill konfigurera en tabell `softDelete` för en mjuk borttagning anger du egenskapen i tabelldefinitionsfilen:

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

Du bör upprätta en mekanism för att ta bort poster: ett klientprogram, ett WebJob, en Azure-funktion eller ett anpassat API.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Dirigera databasen med data

När du skapar ett nytt program kanske du vill dirigera en tabell med data. Du kan göra detta i tabelldefinitionen JavaScript-fil på följande sätt:

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

Såddning av data sker bara när du har använt Mobile Apps SDK för att skapa tabellen. Om tabellen redan finns i databasen injiceras inga data i tabellen. Om det dynamiska schemat är aktiverat härleds schemat från de dirigerade data.

Vi rekommenderar att du `tables.initialize()` uttryckligen anropar metoden för att skapa tabellen när tjänsten börjar köras.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Aktivera Swagger-stöd
Mobilappar levereras med inbyggt [Swagger-stöd.] Om du vill aktivera Swagger-stöd installerar du först swagger-ui som ett beroende:

    npm install --save swagger-ui

Du kan sedan aktivera Swagger-stöd i konstruktorn för mobilappar:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Du vill förmodligen bara aktivera Swagger-stöd i utvecklingsutgåvor. Du kan göra detta `NODE_ENV` med hjälp av appinställningen:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Slutpunkten `swagger` finns på http://*din plats*.azurewebsites.net/swagger. Du kan komma åt Swagger-användargränssnittet via `/swagger/ui` slutpunkten. Om du väljer att kräva autentisering i hela programmet skapar Swagger ett fel. Bäst resultat får du om du väljer att tillåta oautentiserade begäranden i autentiserings-/auktoriseringsinställningarna för Azure App Service och sedan styra autentiseringen med hjälp av egenskapen. `table.access`

Du kan också lägga till Swagger-alternativet i filen azureMobile.js om du bara vill ha Swagger-stöd för att utveckla lokalt.

## <a name="push-notifications"></a><a name="push"/>Push-meddelanden

Mobilappar integreras med Azure Notification Hubs så att du kan skicka riktade push-meddelanden till miljontals enheter på alla större plattformar. Genom att använda Meddelandehubbar kan du skicka push-meddelanden till iOS-, Android- och Windows-enheter. Mer information om allt du kan göra med meddelandehubbar finns i [översikten meddelandehubbar](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push-notifications"></a><a name="send-push"></a>Skicka push-meddelanden

Följande kod visar hur `push` du använder objektet för att skicka ett broadcast push-meddelande till registrerade iOS-enheter:

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

Genom att skapa en mallöverföringsregistrering från klienten kan du i stället skicka ett mallfushmeddelande till enheter på alla plattformar som stöds. Följande kod visar hur du skickar ett mallmeddelande:

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

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Skicka push-meddelanden till en autentiserat användare med hjälp av taggar
När en autentrad användare registrerar för push-meddelanden läggs en användar-ID-tagg automatiskt till i registreringen. Med den här taggen kan du skicka push-meddelanden till alla enheter som registrerats av en viss användare. Följande kod hämtar SID för användaren som gör begäran och skickar en mall push-meddelande till varje enhetsregistrering för den användaren:

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

När du registrerar dig för push-meddelanden från en autentrad klient kontrollerar du att autentiseringen är klar innan du försöker registrera dig.

## <a name="custom-apis"></a><a name="CustomAPI"></a>Anpassade API:er

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Definiera ett anpassat API

Förutom DATA ACCESS API via `/tables` slutpunkten kan mobilappar tillhandahålla anpassad API-täckning. Anpassade API:er definieras på ett liknande sätt som tabelldefinitionerna och kan komma åt alla samma funktioner, inklusive autentisering.

Om du vill använda App Service-autentisering med ett anpassat API måste du först konfigurera App Service-autentisering i [Azure-portalen.] Mer information finns i konfigurationsguiden för den identitetsprovider som du tänker använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Googles autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Anpassade API:er definieras på ungefär samma sätt som tabell-API:et:

1. Skapa `api` en katalog.
1. Skapa en JavaScript-fil `api` för API-definition i katalogen.
1. Använd importmetoden för `api` att importera katalogen.

Här är prototypen API definition baserad på grundläggande-app exempel som vi använde tidigare:

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

Låt oss ta ett exempel API som returnerar serverdatum med hjälp av `Date.now()` metoden. Här är api/date.js-filen:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Varje parameter är ett av de vanliga RESTful verbs: GET, POST, PATCH eller DELETE. Metoden är en standard [ExpressJS middleware-funktion] som skickar den nödvändiga utdata.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Kräv autentisering för åtkomst till ett anpassat API

Mobile Apps SDK implementerar autentisering på `tables` samma sätt för både slutpunkten och anpassade API:er. Om du vill lägga till autentisering i `access` API:et som utvecklats i föregående avsnitt lägger du till en egenskap:

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

Du kan också ange autentisering på specifika åtgärder:

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

Samma token som används `tables` för slutpunkten måste användas för anpassade API:er som kräver autentisering.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Hantera stora filuppladdningar

Mobile Apps SDK använder [body-parser middleware](https://github.com/expressjs/body-parser) för att acceptera och avkoda kroppsinnehåll i ditt bidrag. Du kan förkonfigurera body-parser för att acceptera större filuppladdningar:

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

Filen är base-64 kodad före överföringen. Den här kodningen ökar storleken på den faktiska överföringen (och storleken som du måste ta hänsyn till).

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Kör anpassade SQL-uttryck

Mobile Apps SDK ger åtkomst till hela kontexten via begärandeobjektet. Du kan enkelt köra parameteriserade SQL-uttryck till den definierade dataprovidern:

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

## <a name="debugging"></a><a name="Debugging"></a>Felsökning

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Felsöka, diagnostisera och felsöka mobilappar

Azure App Service innehåller flera felsöknings- och felsökningstekniker för Node.js-program.
Mer om du vill komma igång med felsökning av node.js Mobile Apps-baksidan finns i följande artiklar:

* [Övervaka Azure App-tjänsten]
* [Aktivera diagnostikloggning i Azure App Service]
* [Felsöka Azure App Service i Visual Studio]

Node.js-program har tillgång till ett brett utbud av diagnostiska loggverktyg. Internt använder Mobile Apps Node.js SDK [Winston] för diagnostikloggning. Loggning aktiveras automatiskt när du aktiverar felsökningsläge eller ställer in appinställningen `MS_DebugMode` till true i [Azure-portalen]. Genererade loggar visas i diagnostikloggarna i [Azure-portalen].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Snabbstart för Android-klient]: app-service-mobile-android-get-started.md
[Apache Cordova Klient snabbstart]: app-service-mobile-cordova-get-started.md
[snabbstart för iOS-klient]: app-service-mobile-ios-get-started.md
[Xamarin.iOS-klient snabbstart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android-klient snabbstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-klientens snabbstart]: app-service-mobile-xamarin-forms-get-started.md
[Snabbstart för Windows Store-klient]: app-service-mobile-windows-store-dotnet-get-started.md
[synkronisering av offlinedata]: app-service-mobile-offline-data-sync.md
[Konfigurera Azure Active Directory-autentisering]: ../app-service/configure-authentication-provider-aad.md
[Konfigurera Facebook-autentisering]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurera Googles autentisering]: ../app-service/configure-authentication-provider-google.md
[Konfigurera Microsoft-autentisering]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurera Twitter-autentisering]: ../app-service/configure-authentication-provider-twitter.md
[Distributionsguide för Azure App Service]: ../app-service/deploy-local-git.md
[Övervaka Azure App-tjänsten]: ../app-service/web-sites-monitor.md
[Aktivera diagnostikloggning i Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Felsöka Azure App Service i Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[ange nodversionen]: ../nodejs-specify-node-version-azure-apps.md
[använda nodmoduler]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure-portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Löfte]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exempel på basicapp på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo-exempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[exempelkatalog på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[FrågaJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 för Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[paketet mssql Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS mellanprogram]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

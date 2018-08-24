---
title: Hur du arbetar med Node.js-backend-Server-SDK för Mobile Apps | Microsoft Docs
description: Lär dig hur du arbetar med backend-Server-SDK för Node.js för Azure App Service Mobile Apps.
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
ms.openlocfilehash: cbedb17bb7563620d0d9db81333d9a79301b4ee0
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817516"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Hur du använder Mobile Apps Node.js-SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Den här artikeln innehåller detaljerad information och exempel som visar hur du arbetar med en Node.js-serverdel i funktionen Mobile Apps i Azure App Service.

## <a name="Introduction"></a>Introduktion

Mobile Apps ger möjlighet att lägga till en mobiloptimerad dataåtkomst webb-API till ett webbprogram. SDK för Mobile Apps tillhandahålls för ASP.NET och Node.js-webbprogram. SDK innehåller följande åtgärder:

* Tabellåtgärder (läsa, infoga, uppdatera, ta bort) för dataåtkomst
* Anpassat API-åtgärder

Både ger för autentisering på alla identitetsleverantörer som gör att Azure App Service. Dessa providers omfattar sociala identitetsleverantörer, till exempel Facebook, Twitter, Google, Microsoft, samt och Azure Active Directory för företagsidentitet.

Du kan hitta exempel för varje användningsfall i den [katalogen samples på GitHub].

## <a name="supported-platforms"></a>Plattformar som stöds

Mobile Apps Node.js-SDK har stöd för den aktuella LTS-versionen av noden och senare. Den senaste LTS-versionen är för närvarande noden v4.5.0. Andra versioner av noden fungerar men stöds inte.

Node.js-SDK för Mobile Apps stöder två databasdrivrutiner: 

* Noden mssql-drivrutinen har stöd för Azure SQL Database och lokala SQL Server-instanser.  
* Sqlite3-drivrutinen stöder SQLite databaser på en enda instans.

### <a name="howto-cmdline-basicapp"></a>Skapa en grundläggande Node.js-serverdel med hjälp av kommandoraden

Varje Mobile Apps Node.js-serverdel startar som ett ExpressJS program. ExpressJS är den mest populära service webbramverk tillgängliga för Node.js. Du kan skapa en grundläggande [Express] program på följande sätt:

1. Skapa en katalog för ditt projekt i ett kommando eller en PowerShell-fönster:

        mkdir basicapp

1. Kör `npm init` att initiera paket-struktur:

        cd basicapp
        npm init

   Den `npm init` kommandot frågar en uppsättning frågor att initiera projektet. Se Exempelutdata:

   ![Npm init-utdata][0]

1. Installera den `express` och `azure-mobile-apps` bibliotek från npm-databasen:

        npm install --save express azure-mobile-apps

1. Skapa en app.js-filen om du vill implementera grundläggande mobila server:

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

Det här programmet skapar en mobiloptimerad webb-API med en enda slutpunkt (`/tables/TodoItem`) som ger oautentiserad åtkomst till en underliggande SQL-datalager genom att använda ett dynamiskt schema. Det är lämpligt för följande snabbstarter för klient-biblioteket:

* [Snabbstart för Android-klient]
* [Snabbstart för Apache Cordova-klient]
* [iOS klienten Snabbstart]
* [Snabbstart för Windows Store-klienten]
* [Snabbstart för Xamarin.iOS-klienten]
* [Snabbstart för Xamarin.Android-klienten]
* [Snabbstart för Xamarin.Forms-klienten]

Du hittar koden för det här grundläggande programmet i den [basicapp exemplet på GitHub].

### <a name="howto-vs2015-basicapp"></a>Skapa en Node.js-serverdel genom att använda Visual Studio 2015

Visual Studio 2015 kräver ett tillägg för att utveckla Node.js-program i din IDE. Börja genom att installera den [1.1 för Node.js Tools för Visual Studio]. Skapa en Express 4.x-program när du är klar med installationen:

1. Öppna den **nytt projekt** dialogrutan (från **filen** > **New** > **projekt**).
1. Expandera **mallar** > **JavaScript** > **Node.js**.
1. Välj **grundläggande Azure Node.js Express 4-program**.
1. Fyll i projektets namn. Välj **OK**.

   ![Visual Studio 2015 nytt projekt][1]
1. Högerklicka på den **npm** noden och väljer **installera nya npm-paket**.
1. Du kan behöva uppdatera npm-katalogen när du har skapat ditt första Node.js-program. Välj **uppdatera** om det behövs.
1. Ange **azure mobile apps** i sökrutan. Välj den **azure mobile apps 2.0.0** paketera och välj sedan **installera paket**.

   ![Installera nya npm-paket][2]
1. Välj **Stäng**.
1. Öppna filen app.js för att lägga till stöd för Mobile Apps-SDK. AT rad 6 at längst ned i biblioteket `require` instruktioner, Lägg till följande kod:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    AT rad ungefär 27 efter andra `app.use` instruktioner, Lägg till följande kod:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Spara filen.

1. Antingen köra programmet lokalt (API: et levereras på http://localhost:3000) eller publicera till Azure.

### <a name="create-node-backend-portal"></a>Skapa en Node.js-serverdel med hjälp av Azure portal

Du kan skapa en Mobile Apps-serverdel direkt i den [Azure Portal]. Du kan utföra följande steg eller skapa en klient och server tillsammans genom att följa den [skapa en mobilapp](app-service-mobile-ios-get-started.md) självstudien. Självstudien innehåller en förenklad version av dessa anvisningar och är bäst för proof of concept-projekt.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

I den **Kom igång** fönstret under **skapa ett tabell-API**, Välj **Node.js** som backend-språk.
Markera kryssrutan för **bekräftar jag att allt webbinnehåll skrivs**, och välj sedan **skapa TodoItem-tabell**.

### <a name="download-quickstart"></a>Ladda ned Node.js backend-Snabbstart Kodprojekt med hjälp av Git

När du skapar en Node.js Mobile Apps-serverdelen med hjälp av portalen **Snabbstart** fönstret ett Node.js-projekt skapas och distribueras till din webbplats. Du kan lägga till tabeller och API: er och redigera kodfiler för Node.js-serverdel i portalen. Du kan också använda olika distributionsverktyg för att ladda ned backend-projektet så att du kan lägga till eller ändra tabeller och API: er och sedan publicera projektet. Mer information finns i den [Distributionsguide för Azure App Service].

Följande procedur använder en Git-lagringsplats för att hämta snabbstartskoden för projektet:

1. Installera Git om du inte redan gjort det. De steg som krävs för att installera Git variera mellan olika operativsystem. Operativsystemspecifika distributioner och installationer finns i [installerar Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
1. Se [förbereda din lagringsplats](../app-service/app-service-deploy-local-git.md#prepare-your-repository) att aktivera Git-lagringsplats för backend-webbplatsen. Anteckna distributionsanvändarnamn och lösenord.
1. I fönstret för Mobile Apps-serverdel, notera den **URL för Git-klonen** inställningen.
1. Kör den `git clone` kommandot med hjälp av URL för Git-klonen. Ange lösenordet vid behov, som i följande exempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

1. Bläddra till den lokala katalogen (`/todolist` i föregående exempel), och Lägg märke till att project-filer har laddats ned. Leta upp filen todoitem.json i den `/tables` directory. Den här filen definierar behörigheter för tabellen. Filen todoitem.js finns i samma katalog. Den definierar CRUD-skript för åtgärden för tabellen.
1. När du gör ändringar i projektfiler, kör följande kommandon för att lägga till, genomför och överför sedan ändringarna till platsen:

        $ git commit -m "updated the table script"
        $ git push origin master

   När du lägger till nya filer i projektet måste du först köra den `git add .` kommando.

Platsen publiceras varje gång en ny uppsättning incheckningar skickas till platsen.

### <a name="howto-publish-to-azure"></a>Publicera din Node.js-serverdel i Azure

Microsoft Azure tillhandahåller många metoder för att publicera din Mobile Apps Node.js-serverdel till Azure-tjänsten. Dessa mekanismer är distributionsverktyg som integreras i Visual Studio, kommandoradsverktyg och kontinuerlig distributionsalternativ baserat på källkontroll. Mer information finns i den [Distributionsguide för Azure App Service].

Azure App Service har råd för Node.js-program som du bör granska innan du publicerar backend-servern:

* Så här [ange nod-version]
* Så här [använder Node-moduler]

### <a name="howto-enable-homepage"></a>Aktivera en startsida för ditt program

Många program är en kombination av webb- och mobilappar. Du kan använda ExpressJS framework för att kombinera två aspekter. Ibland, men kanske du vill bara implementera ett mobila gränssnitt. Det är praktiskt att ange en hemsida för att säkerställa att app service är igång och körs. Du kan ange egna startsida eller aktivera en tillfällig startsida. Om du vill aktivera en tillfällig startsida, Använd följande kod för att skapa en instans av Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Om du bara vill det här alternativet som är tillgängliga när du utvecklar lokalt kan du lägga till den här inställningen azureMobile.js-fil.

## <a name="TableOperations"></a>Tabellåtgärder

Azure mobile apps Node.js Server SDK tillhandahåller mekanismer för att exponera datatabeller som lagras i Azure SQL Database som webb-API. Den innehåller fem:

| Operation | Beskrivning |
| --- | --- |
| GET-/tables/*tabellnamn* |Hämta alla poster i tabellen. |
| GET-/tables/*tablename*/:id |Få en viss post i tabellen. |
| POST /tables/*tabellnamn* |Skapa en post i tabellen. |
| PATCH /tables/*tablename*/:id |Uppdatera en post i tabellen. |
| Ta bort /tables/*tablename*/:id |Ta bort en post i tabellen. |

Den här webb-API: et stöder [OData] och utökar tabellschemat för [synkronisering av offlinedata].

### <a name="howto-dynamicschema"></a>Definiera tabeller genom att använda ett dynamiskt schema

Innan du kan använda en tabell, måste du definiera den. Du kan definiera tabeller med hjälp av ett statiskt schema (definierar kolumnerna i schemat) eller dynamiskt (där SDK styr schemat baserat på inkommande begäranden). Du kan dessutom styra specifika aspekter av webb-API genom att lägga till JavaScript-kod definitionen.

Som bästa praxis bör du definiera varje tabell i en JavaScript-fil i den `tables` directory, och sedan använda den `tables.import()` metod för att importera tabellerna. Utöka basic-app-exemplet skulle du justera app.js-filen:

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

Definiera tabellen i. / tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabeller använder en dynamiskt schema som standard. Om du vill inaktivera dynamiskt schema globalt, ange den `MS_DynamicSchema` appinställningen till false i Azure-portalen.

Du hittar ett komplett exempel i den [todo-exemplet på GitHub].

### <a name="howto-staticschema"></a>Definiera tabeller genom att använda ett statiskt schema

Du kan uttryckligen definiera kolumnerna som ska exponera via webb-API. Azure mobile apps Node.js SDK lägger automatiskt till några extra kolumner som krävs för synkronisering av offlinedata i listan som du anger. Till exempel klientprogrammen Snabbstart kräver en tabell med två kolumner: `text` (en sträng) och `complete` (ett booleskt värde).  
Tabellen kan definieras i tabellen definitionsfilen för JavaScript (finns i den `tables` directory) på följande sätt:

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

Om du definierar tabeller statiskt, måste du även anropa den `tables.initialize()` metod för att skapa databasschemat vid start. Den `tables.initialize()` metoden returnerar en [löftet] så att webbtjänsten inte hanterar några begäranden innan databasen har initierats.

### <a name="howto-sqlexpress-setup"></a>Använda SQL Server Express som datalager för utveckling på den lokala datorn

Node.js-SDK för Mobile Apps har tre alternativ för att betjäna data direkt:

* Använd den **minne** drivrutinen att tillhandahålla en icke-beständiga exempel butik.
* Använd den **mssql** drivrutinen att tillhandahålla ett datalager som SQL Server Express för utveckling.
* Använd den **mssql** drivrutinen att tillhandahålla ett datalager i Azure SQL Database för produktion.

Mobile Apps Node.js-SDK använder den [mssql Node.js-paket] upprätta och använda en anslutning till SQL Server Express och SQL-databas. Det här paketet kräver att du aktiverar TCP-anslutningar på din instans av SQL Server Express.

> [!TIP]
> Minne-drivrutinen ger inte en fullständig uppsättning funktioner för testning. Om du vill testa serverdelen lokalt rekommenderar vi använda ett SQL Server Express datalager och mssql-drivrutinen.

1. Ladda ned och installera [Microsoft SQL Server 2014 Express]. Se till att du installerar SQL Server 2014 Express med verktyg-versionen. Om du inte uttryckligen behöver stöd för 64 bitar, förbrukar 32-bitarsversionen mindre minne vid körning.
1. Kör Konfigurationshanteraren för SQL Server 2014:

   a. Expandera den **nätverkskonfiguration för SQL Server** nod i träd-menyn.

   b. Välj **protokoll för SQLEXPRESS**.

   c. Högerklicka på **TCP/IP** och välj **aktivera**. Välj **OK** i popup-dialogrutan.

   d. Högerklicka på **TCP/IP** och välj **egenskaper**.

   e. Välj den **IP-adresser** fliken.

   f. Hitta den **IPAll** noden. I den **TCP-Port** anger **1433**.

      ![Konfigurera SQL Server Express för TCP/IP][3]

   g. Välj **OK**. Välj **OK** i popup-dialogrutan.

   h. Välj **SQL Server Services** i träd-menyn.

   i. Högerklicka på **SQL Server (SQLEXPRESS)** och välj **starta om**.

   j. Stäng Konfigurationshanteraren för SQL Server 2014.

1. Kör SQL Server 2014 Management Studio och ansluta till din lokala SQL Server Express-instans:

   1. Högerklicka på din instans i Object Explorer och välj **egenskaper**.
   1. Välj den **Security** sidan.
   1. Se till att **SQL Server och Windows-autentisering läge** har valts.
   1. Välj **OK**.

      ![Konfigurera SQL Server Express-autentisering][4]
   1. Expandera **Security** > **inloggningar** i Object Explorer.
   1. Högerklicka på **inloggningar** och välj **ny inloggning**.
   1. Ange ett inloggningsnamn. Välj **SQL Server-autentisering**. Ange ett lösenord och sedan anger du samma lösenord i **Bekräfta lösenord**. Lösenordet måste uppfylla kraven på komplexitet Windows.
   1. Välj **OK**.

      ![Lägga till en ny användare i SQL Server Express][5]
   1. Högerklicka på din nya inloggning och välj **egenskaper**.
   1. Välj den **serverroller** sidan.
   1. Markera kryssrutan för den **dbcreator** serverrollen.
   1. Välj **OK**.
   1. Stäng SQL Server 2015 Management Studio.

Kom ihåg att registrera användarnamnet och lösenordet som du har valt. Du kan behöva tilldela ytterligare serverroller eller behörigheter, beroende på dina databaskrav.

Node.js-program läser den `SQLCONNSTR_MS_TableConnectionString` miljövariabeln för anslutningssträngen för den här databasen. Du kan ange den här variabeln i din miljö. Du kan till exempel använda PowerShell för att ställa in den här miljövariabeln:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Åtkomst till databasen via en TCP/IP-anslutning. Ange ett användarnamn och lösenord för anslutningen.

### <a name="howto-config-localdev"></a>Konfigurera ditt projekt för lokal utveckling

Mobile Apps läser en JavaScript-fil som heter *azureMobile.js* från det lokala filsystemet. Använd inte den här filen för att konfigurera SDK för Mobile Apps i produktion. Använd i stället **appinställningar** i den [Azure Portal].

Filen azureMobile.js exporterar ett konfigurationsobjekt. De vanligaste inställningarna är:

* Databasinställningar
* Inställningar för Diagnostisk loggning
* Alternativa CORS-inställningar

Det här exemplet **azureMobile.js** filen implementerar föregående databasinställningarna:

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

Vi rekommenderar att du lägger till **azureMobile.js** till din **.gitignore** filen (eller andra källkodskontroll ignorera filen) till att förhindra att lösenord lagras i molnet. Konfigurera inställningar för produktion i alltid **appinställningar** inom den [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurera inställningar för din mobilapp

De flesta inställningarna i filen azureMobile.js har en motsvarande appinställning i den [Azure Portal]. Använd listan nedan för att konfigurera din app i **appinställningar**:

| App-inställning | azureMobile.js inställningen | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |namn |Namnet på appen |sträng |
| **MS_MobileLoggingLevel** |Logging.level |Minimiloggnivå meddelanden att logga in |fel, varning, information, utförlig, felsökning, stökigt – |
| **MS_DebugMode** |felsök |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_TableSchema** |data.schema |Standardnamnet för schemat för SQL-tabeller |String (standard: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktiverar eller inaktiverar felsökningsläge |SANT, FALSKT |
| **MS_DisableVersionHeader** |version (inställd till odefinierat) |Inaktiverar rubriken X-ZUMO-Server-Version |SANT, FALSKT |
| **MS_SkipVersionCheck** |skipversioncheck |Inaktiverar versionskontroll för klient-API |SANT, FALSKT |

Ange en appinställning:

1. Logga in på [Azure Portal].
1. Välj **alla resurser** eller **Apptjänster**, och välj sedan namnet på din mobila app.
1. Den **inställningar** öppnas fönstret som standard. Om det inte, väljer **inställningar**.
1. På den **Allmänt** menyn och välj **programinställningar**.
1. Bläddra till den **appinställningar** avsnittet.
1. Om din appinställningen redan finns, väljer du värdet på appinställningen som du vill redigera värdet.
   Om din appinställningen inte finns, anger du appinställningen i den **nyckel** box och värdet i den **värdet** box.
1. Välj **Spara**.

Ändrar inställningar för de flesta måste tjänsten startas om.

### <a name="howto-use-sqlazure"></a>Använd SQL-databas som din produktionsdata lagra

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Med Azure SQL Database som ett datalager är identiska för alla Azure App Service-programtyper. Om du redan inte har gjort det, Följ stegen nedan för att skapa en Mobile Apps-serverdel:

1. Logga in på [Azure Portal].
1. I det övre vänstra hörnet i fönstret väljer du den **+ ny** knappen > **webb + mobilt** > **Mobilapp**, och sedan ange ett namn för Mobile Apps-serverdel.
1. I den **resursgrupp** ange samma namn som din app.
1. Standard App Service-plan har valts. Om du vill ändra App Service-planen:

   a. Välj **Apptjänstplan** > **+ Skapa ny**.

   b. Ange ett namn på den nya App Service-planen och välj en lämplig plats.

   c. Välj en prisnivå för tjänsten. Välj **visa alla** att visa fler prisalternativ, till exempel **kostnadsfri** och **delade**.

   d. Klicka på den **Välj** knappen.

   e. I den **apptjänstplan** väljer **OK**.
1. Välj **Skapa**.

Etablera en Mobile Apps kan serverdel ta några minuter. När Mobile Apps tillbaka slutet etableras, öppnas den **inställningar** för Mobile Apps-serverdel.

Du kan välja att antingen ansluta en befintlig SQL-databas till serverdelen för Mobile Apps eller skapa en ny SQL-databas. I det här avsnittet ska skapa vi en SQL-databas.

> [!NOTE]
> Om du redan har en databas på samma plats som Mobile Apps-serverdel, kan du istället välja **Använd en befintlig databas** och välj sedan den databasen. Vi rekommenderar inte användning av en databas på en annan plats på grund av ökad latens.

1. I den nya Mobile Apps-serverdelen, Välj **inställningar** > **Mobilapp** > **Data** > **+ Lägg till**.
1. I den **Lägg till dataanslutning** väljer **SQL Database - Konfigurera obligatoriska inställningar** > **skapa en ny databas**. Ange namnet på den nya databasen i den **namn** box.
1. Välj **Server**. I den **ny server** rutan Ange ett unikt namn i den **servernamn** rutan och ange en lämplig användarnamn och lösenord. Se till att **ge azure-tjänster åtkomst till servern** har valts. Välj **OK**.

   ![Skapa en Azure SQL Database][6]
1. I den **ny databas** väljer **OK**.
1. I den **Lägg till dataanslutning** väljer **anslutningssträngen**, och ange det användarnamn och lösenord som du angav när du skapade databasen. Om du använder en befintlig databas, kan du ange inloggningsuppgifterna för den här databasen. Välj **OK**.
1. I den **Lägg till dataanslutning** panelen igen och väljer **OK** att skapa databasen.

<!--- END OF ALTERNATE INCLUDE -->

Skapa databasen kan ta några minuter. Använd den **meddelanden** område för att övervaka förloppet för distributionen. Fortsätter inte förrän databasen har distribuerats. När databasen har distribuerats skapas en anslutningssträng för SQL Database-instans i inställningarna för Mobile Apps serverdelsappen. Du kan se den här appinställningen i **inställningar** > **programinställningar** > **anslutningssträngar**.

### <a name="howto-tables-auth"></a>Kräv autentisering för åtkomst till tabeller

Om du vill använda App Service-autentisering med den `tables` slutpunkt, måste du konfigurera App Service-autentisering i den [Azure Portal] första. Mer information finns i konfigurationsguiden för den identitetsprovider som du planerar att använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Varje tabell har en åtkomst-egenskap som du kan använda för att styra åtkomsten till tabellen. I följande exempel visas en statiska definierade tabell med autentisering krävs.

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

Åtkomst-egenskapen kan ha ett av tre värden:

* *anonym* indikerar att klientprogrammet har tillåtelse att läsa data utan autentisering.
* *autentiserad* indikerar att klientprogrammet måste skicka en giltig autentiseringstoken med begäran.
* *inaktiverad* anger att den här tabellen är för närvarande inaktiverad.

Om åtkomst-egenskapen är odefinierad tillåts oautentiserad åtkomst.

### <a name="howto-tables-getidentity"></a>Använda anspråk för autentisering med dina tabeller
Du kan ställa in olika anspråk som begärs när autentisering har ställts in. De här anspråken inte är normalt tillgängliga via den `context.user` objekt. Men du kan hämta dem med hjälp av den `context.user.getIdentity()` metoden. Den `getIdentity()` metoden returnerar ett löfte som motsvarar ett objekt. Objektet ställs in med den aktuella autentiseringsmetoden (`facebook`, `google`, `twitter`, `microsoftaccount`, eller `aad`).

Om du har konfigurerat Microsoft-kontoautentisering och begäran som gör anspråk på e-postadresser du lägga till e-postadressen till posten med följande tabellkontrollanten:

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

Om du vill se vilka anspråk som är tillgängliga genom att använda en webbläsare för att visa den `/.auth/me` slutpunkten för din webbplats.

### <a name="howto-tables-disabled"></a>Inaktivera åtkomst till specifika tabellåtgärder

Egenskapen åtkomst kan användas till att styra enskilda åtgärder förutom visas i tabellen. Det finns fyra åtgärder:

* `read` är RESTful GET-åtgärd på tabellen.
* `insert` är RESTful POST-åtgärd på tabellen.
* `update` är RESTful PATCH-åtgärden för tabellen.
* `delete` är RESTful ta bort åtgärden för tabellen.

Du kanske vill ange en skrivskyddad oautentiserade tabell:

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

### <a name="howto-tables-query"></a>Justera den fråga som används med tabellåtgärder

Ett vanligt krav för tabellåtgärder är att tillhandahålla en begränsad vy av data. Du kan till exempel ange en tabell som har märkts med autentiserat användar-ID så att du kan bara läsa eller uppdatera dina egna poster. Följande tabelldefinitionen innehåller den här funktionen:

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

Åtgärder som vanligtvis köra en fråga har en fråga-egenskap som du kan justera med hjälp av en `where` satsen. Frågeegenskapen är en [QueryJS] objekt som används för att konvertera en OData-fråga till något som serverdel data kan bearbeta. Du kan använda en karta för enkel likhet fall (t.ex. den föregående som). Du kan också lägga till specifika SQL-satser:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurera en mjuk borttagning för en tabell

En mjuk borttagning faktiskt bort inte poster. Istället markeras dem som Borttaget i databasen genom att ange den borttagna kolumnen till true. SDK för Mobile Apps automatiskt att ta bort ej permanent borttagna poster från resultaten, såvida inte klient-SDK för Mobile använder `IncludeDeleted()`. Om du vill konfigurera en tabell för en mjuk borttagning, ange den `softDelete` egenskapen i definitionsfilen för tabellen:

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

Du bör fastställa en mekanism för att ta bort poster: ett klientprogram, ett Webbjobb, en Azure-funktion eller ett anpassat API.

### <a name="howto-tables-seeding"></a>Dirigera din databas med data

När du skapar ett nytt program, kanske du vill dirigera en tabell med data. Du kan göra i tabellen definition JavaScript-fil på följande sätt:

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

Seeding av data sker bara när du har använt SDK för Mobile Apps för att skapa tabellen. Om tabellen redan finns i databasen har inga data matas in i tabellen. Om dynamiskt schema är aktiverat härleds schemat från förpopulerad data.

Vi rekommenderar att du explicit anropa den `tables.initialize()` metod för att skapa tabellen när tjänsten startar.

### <a name="Swagger"></a>Aktivera stöd för Swagger
Mobile Apps levereras med inbyggda [Swagger] stöd. Om du vill aktivera stöd för Swagger, installera swagger-användargränssnitt som ett beroende:

    npm install --save swagger-ui

Sedan kan du aktivera stöd för Swagger i konstruktorn Mobile Apps:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Du förmodligen bara vill aktivera stöd för Swagger i utveckling utgåvor. Du kan göra detta med hjälp av den `NODE_ENV` appinställningen:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Den `swagger` slutpunkten är placerad på http://*yoursite*.azurewebsites.net/swagger. Du kan komma åt Användargränssnittet för Swagger via den `/swagger/ui` slutpunkt. Om du vill kräva autentisering i hela programmet genererar ett fel i Swagger. För bästa resultat bör du välja att tillåta oautentiserade begäranden i inställningarna för Azure App Service autentisering/auktorisering och därefter kontrollera autentisering med hjälp av den `table.access` egenskapen.

Du kan också lägga till alternativet Swagger azureMobile.js-filen om du bara vill ha stöd för Swagger för att utveckla lokalt.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Push-meddelanden

Mobile Apps kan integreras med Azure Notification Hubs så att du kan skicka riktade push-meddelanden till miljontals enheter på alla större plattformar. Med Meddelandehubbar kan du skicka push-meddelanden till iOS, Android och Windows enheter. Mer information om alla som du kan göra med Notification Hubs finns i [översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Skicka push-meddelanden

Följande kod visar hur du använder den `push` -objektet skickar skicka push-meddelanden till registrerade iOS-enheter:

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

Du kan i stället skicka en mall för push-meddelande till enheter på alla plattformar som stöds genom att skapa en mall för push-registrering från klienten. Följande kod visar hur du skickar ett meddelande i mallen:

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

### <a name="push-user"></a>Skicka push-meddelanden till en autentiserad användare med hjälp av taggar
När en autentiserad användare registrerar för push-meddelanden, läggs en användartagg ID automatiskt till registreringen. Du kan skicka push-meddelanden till alla enheter som registrerats av en viss användare med hjälp av den här taggen. Följande kod hämtar SID för användaren som ansvarar för att göra begäran och skickar ett push-meddelande för mallen till varje enhetsregistrering för användaren:

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

När du registrerat för push-meddelanden från en autentiserad klient kan du se till att autentiseringen har slutförts innan du försöker registrera.

## <a name="CustomAPI"></a> Anpassade API: er

### <a name="howto-customapi-basic"></a>Definiera en anpassad API

Förutom API: et för Data-åtkomst via den `/tables` slutpunkt, Mobile Apps kan tillhandahålla anpassade API-täckning. Anpassade API: er har definierats i ett liknande sätt att tabelldefinitionerna och kan komma åt samma anläggningar, inklusive autentisering.

Om du vill använda App Service-autentisering med ett anpassat API måste du konfigurera App Service-autentisering i den [Azure Portal] första. Mer information finns i konfigurationsguiden för den identitetsprovider som du planerar att använda:

* [Konfigurera Azure Active Directory-autentisering]
* [Konfigurera Facebook-autentisering]
* [Konfigurera Google-autentisering]
* [Konfigurera Microsoft-autentisering]
* [Konfigurera Twitter-autentisering]

Anpassade API: er har definierats på ungefär samma sätt som tabeller-API:

1. Skapa en `api` directory.
1. Skapa en API-definition JavaScript-fil i den `api` directory.
1. Använda importmetoden för att importera den `api` directory.

Här är prototyp API-definition som är baserat på basic-appexempel som vi använde tidigare:

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

Låt oss ta ett exempel-API som returnerar datum server med hjälp av den `Date.now()` metoden. Här är filen api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Varje parameter är en av de RESTful standardverb: hämta, publicera, uppdatera eller ta bort. Metoden är en standard [ExpressJS mellanprogram] funktion som skickar krävs utdata.

### <a name="howto-customapi-auth"></a>Kräv autentisering för åtkomst till ett anpassat API

SDK för Mobile Apps implementerar autentisering på samma sätt för både den `tables` slutpunkt och anpassade API: er. Om du vill lägga till autentisering i API: et har utvecklats i föregående avsnitt, lägger du till en `access` egenskap:

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

Samma token som används för den `tables` slutpunkt måste användas för anpassade API: er som kräver autentisering.

### <a name="howto-customapi-auth"></a>Hantera stora filöverföringar

SDK för Mobile Apps använder den [body-parsern mellanprogram](https://github.com/expressjs/body-parser) att acceptera och avkoda brödtext i ditt bidrag. Du kan förkonfigurera body-parsern för att godkänna större filöverföringar:

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

Filen är base 64-kodad före överföringen. Den här kodningen ökar storleken på den faktiska överföringen (och den storlek som du måste ta hänsyn till).

### <a name="howto-customapi-sql"></a>Köra anpassade SQL-uttryck

SDK för Mobile Apps ger tillgång till hela kontexten via Begäranobjektet. Du kan enkelt köra SQL-instruktioner till definierade dataleverantören:

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

## <a name="Debugging"></a>Felsökning, enkla tabeller och enkla API: er

### <a name="howto-diagnostic-logs"></a>Felsöka, diagnostisera och felsöka Mobile Apps

Azure App Service tillhandahåller flera felsökning och felsökningstekniker för Node.js-program.
Börja felsöka dina Node.js Mobile Apps-serverdel, finns i följande artiklar:

* [Övervakning av Azure App Service]
* [Aktivera Diagnostisk loggning i Azure App Service]
* [Felsöka Azure App Service i Visual Studio]

Node.js-program har åtkomst till en mängd olika diagnostiklogg verktyg. Node.js-SDK för Mobile Apps använder internt, [Winston] för Diagnostisk loggning. Loggning aktiveras automatiskt när du aktiverar debug läge eller anger den `MS_DebugMode` appinställningen på Sant i den [Azure Portal]. Genererade loggar visas i diagnostikloggar i den [Azure Portal].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Arbeta med enkla tabeller i Azure portal

Du kan använda enkla tabeller för att skapa och arbeta med tabeller direkt i portalen. Du kan ladda upp datauppsättningen enkla tabeller i CSV-format. Observera att du inte kan använda egenskapsnamn (i din CSV-datauppsättning) som står i konflikt med egenskapsnamn som Mobile Apps-serverdel. System-egenskapsnamnen är:
* createdAt
* updatedAt
* borttagen
* version

Du kan även redigera tabellåtgärder med hjälp av App Service Editor. När du väljer **enkla tabeller** i platsinställningarna för backend-du lägga till, ändra eller ta bort en tabell. Du kan också se data i tabellen.

![Arbeta med enkla tabeller](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Följande kommandon är tillgängliga i kommandofältet för en tabell:

* **Ändra behörigheter**: ändra behörigheten för Läs, infoga, uppdatera och ta bort åtgärder på tabellen.
 Alternativen är att tillåta anonym åtkomst, kan kräva autentisering eller inaktiverar all åtkomst till åtgärden.
* **Redigera skriptet**: skriptfilen för tabellen har öppnats i App Service Editor.
* **Hantera scheman**: lägga till eller ta bort kolumner eller ändra Tabellindex.
* **Rensa tabell**: trunkera en befintlig tabell genom att ta bort alla rader med data, men lämna schemat oförändrade.
* **Ta bort rader**: ta bort enskilda datarader.
* **Visa direktuppspelningsloggar**: ansluta till strömmande Loggtjänsten för din webbplats.

### <a name="work-easy-apis"></a>Arbeta med enkla API: er i Azure portal

Du kan använda enkla API: er för att skapa och arbeta med anpassade API: er direkt i portalen. Du kan redigera API-skript med hjälp av App Service Editor.

När du väljer **enkla API: er** i platsinställningarna för backend-du lägga till, ändra eller ta bort en anpassad API-slutpunkt.

![Arbeta med enkla API: er](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

I portalen kan du ändra åtkomstbehörighet för en HTTP-åtgärd, redigera skriptfilen API i App Service Editor eller visa direktuppspelningsloggarna.

### <a name="online-editor"></a>Redigera kod i App Service Editor

Du kan redigera dina Node.js-backend-skriptfiler i App Service Editor utan att behöva ladda ned projektet till den lokala datorn med hjälp av Azure portal. Redigera skriptfiler i online-redigeraren:

1. I fönstret för Mobile Apps-serverdel, väljer **alla inställningar** > antingen **enkla tabeller** eller **enkla API: er**. Välj en tabell eller API och välj sedan **Redigera skript**. Skriptfilen öppnas i App Service Editor.

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. Gör dina ändringar till kodfil i online-redigeraren. Ändringar sparas automatiskt när du skriver.

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
[Snabbstart för Apache Cordova-klient]: app-service-mobile-cordova-get-started.md
[iOS klienten Snabbstart]: app-service-mobile-ios-get-started.md
[Snabbstart för Xamarin.iOS-klienten]: app-service-mobile-xamarin-ios-get-started.md
[Snabbstart för Xamarin.Android-klienten]: app-service-mobile-xamarin-android-get-started.md
[Snabbstart för Xamarin.Forms-klienten]: app-service-mobile-xamarin-forms-get-started.md
[Snabbstart för Windows Store-klienten]: app-service-mobile-windows-store-dotnet-get-started.md
[synkronisering av offlinedata]: app-service-mobile-offline-data-sync.md
[Konfigurera Azure Active Directory-autentisering]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurera Facebook-autentisering]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurera Google-autentisering]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Konfigurera Microsoft-autentisering]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurera Twitter-autentisering]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Distributionsguide för Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Övervakning av Azure App Service]: ../app-service/web-sites-monitor.md
[Aktivera Diagnostisk loggning i Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Felsöka Azure App Service i Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[ange nod-version]: ../nodejs-specify-node-version-azure-apps.md
[använder Node-moduler]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Löftet]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp exemplet på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo-exemplet på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[katalogen Samples på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[1.1 för Node.js Tools för Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node.js-paket]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS mellanprogram]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

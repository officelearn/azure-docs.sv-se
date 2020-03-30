---
title: Aktivera offlinesynkronisering (Cordova)
description: Lär dig hur du använder App Service Mobile App för att cachelagra och synkronisera offlinedata i ditt Cordova-program.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459426"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Aktivera offlinesynkronisering för din Cordova-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien introducerar offlinesynkroniseringsfunktionen i Azure Mobile Apps för Cordova. Offlinesynkronisering gör det möjligt&mdash;för slutanvändare att interagera&mdash;med en mobilapp som visar, lägger till eller ändrar data även när det inte finns någon nätverksanslutning. Ändringar lagras i en lokal databas.  När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

Den här självstudien är baserad på Cordova snabbstart lösning för mobilappar som du skapar när du slutför handledningen [Apache Cordova snabbstart]. I den här självstudien uppdaterar du snabbstartslösningen för att lägga till offlinefunktioner i Azure Mobile Apps.  Vi lyfter också fram den offlinespecifika koden i appen.

Mer information om synkroniseringsfunktionen offline finns i avsnittet [Offlinedatasynkning i Azure Mobile Apps]. Mer information om API-användning finns i [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Lägga till offlinesynkronisering i snabbstartslösningen
Offlinesynkroniseringskoden måste läggas till i appen. Offlinesynkronisering kräver plugin:en cordova-sqlite-storage, som automatiskt läggs till i din app när azure mobile apps-insticksprogrammet ingår i projektet. Quickstart-projektet innehåller båda dessa plugins.

1. Öppna index.js i Visual Studio's Solution Explorer och ersätt följande kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    med denna kod:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ersätt sedan följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    med denna kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    De föregående kodtilläggen initierar det lokala arkivet och definierar en lokal tabell som matchar kolumnvärdena som används i din Azure-backdel. (Du behöver inte inkludera alla kolumnvärden i den här koden.)  Fältet `version` underhålls av den mobila backend och används för konfliktlösning.

    Du får en referens till synkroniseringskontexten genom att anropa **getSyncContext**. Synkroniseringskontexten hjälper till att bevara tabellrelationer genom att `.push()` spåra och trycka på ändringar i alla tabeller som en klientapp har ändrat när den anropas.

3. Uppdatera program-URL:en till url:en för mobilappen.

4. Ersätt sedan den här koden:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    med denna kod:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Den föregående koden initierar synkroniseringskontexten och anropar sedan getSyncTable (i stället för getTable) för att få en referens till den lokala tabellen.

    Den här koden använder den lokala databasen för alla tabellåtgärder för att skapa, läsa, uppdatera och ta bort ..This code uses the local database for all create, read, update, and delete (CRUD) table operations.

    Det här exemplet utför enkel felhantering vid synkroniseringskonflikter. Ett verkligt program skulle hantera de olika felen som nätverksvillkor, serverkonflikter och andra. Kodexempel finns i [exemplet offlinesynkronisering].

5. Lägg sedan till den här funktionen för att utföra den faktiska synkroniseringen.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Du bestämmer när ändringar ska ändras i mobile app-backend genom att anropa **syncContext.push()**. Du kan till exempel anropa **syncBackend** i en knapphändelsehanterare knuten till en synkroniseringsknapp.

## <a name="offline-sync-considerations"></a>Överväganden för offlinesynkronisering

I exemplet anropas **push-metoden** **för syncContext** endast vid appstart i motringningsfunktionen för inloggning.  I ett verkligt program kan du också göra den här synkroniseringsfunktionen som utlöses manuellt eller när nätverkstillståndet ändras.

När ett handtag körs mot en tabell som har väntande lokala uppdateringar spåras av kontexten, utlöser den pull-åtgärden automatiskt en push. När du uppdaterar, lägger till och slutför objekt **push** i det här exemplet kan du utelämna det explicita push-anropet, eftersom det kan vara överflödigt.

I den angivna koden efterfrågas alla poster i tabellen todoItem på fjärrkontrollen, men det är också möjligt att filtrera poster genom att skicka ett fråge-ID och fråga för att **skicka**. Mer information finns i avsnittet *Inkrementell synkronisering* i [offlinedatasynkronisering i Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Valfritt) Inaktivera autentisering

Om du inte vill konfigurera autentisering innan du testar offlinesynkronisering kommenterar du återuppringningsfunktionen för inloggning, men lämnar koden i motringningsfunktionen okommenterad.  Efter att ha kommenterat inloggningsraderna följer koden:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu synkroniseras appen med Azure-backend när du kör appen.

## <a name="run-the-client-app"></a>Kör klientappen
När offlinesynkronisering nu är aktiverat kan du köra klientprogrammet minst en gång på varje plattform för att fylla i den lokala butiksdatabasen. Senare simulerar du ett offlinescenario och ändrar data i den lokala butiken medan appen är offline.

## <a name="optional-test-the-sync-behavior"></a>(Valfritt) Testa synkroniseringsbeteendet
I det här avsnittet ändrar du klientprojektet för att simulera ett offlinescenario med hjälp av en ogiltig program-URL för din backend. När du lägger till eller ändrar dataobjekt lagras dessa ändringar i det lokala arkivet, men synkroniseras inte med backend-dataarkivet förrän anslutningen återupprättas.

1. Öppna projektfilen index.js i Lösningsutforskaren och ändra program-URL:en så att den pekar på en ogiltig URL, till exempel följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Uppdatera CSP-elementet `<meta>` med samma ogiltiga URL i index.html.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skapa och kör klientappen och observera att ett undantag loggas i konsolen när appen försöker synkronisera med serverdan efter inloggning. Alla nya objekt som du lägger till finns bara i den lokala butiken tills de skjuts till den mobila backend. Klientappen fungerar som om den är ansluten till backend.

4. Stäng appen och starta om den för att kontrollera att de nya objekten som du har skapat sparas i den lokala arkivet.

5. (Valfritt) Använd Visual Studio för att visa din Azure SQL Database-tabell för att se att data i serveringsdatabasen inte har ändrats.

    Öppna **Server Explorer**i Visual Studio . Navigera till databasen i **Azure**->**SQL Databases**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Valfritt) Testa återanslutningen till din mobila backend

I det här avsnittet återansluter du appen till den mobila backend, som simulerar att appen kommer tillbaka till ett onlinetillstånd. När du loggar in synkroniseras data med din mobila backend.

1. Öppna index.js igen och återställa program-URL:en.
2. Öppna index.html igen och korrigera program-URL:en i CSP-elementet. `<meta>`
3. Återskapa och kör klientappen. Appen försöker synkronisera med mobilappens backend efter inloggning. Kontrollera att inga undantag är inloggade i felsökningskonsolen.
4. (Valfritt) Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan serverdelsdatabasen och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och den lokala butiken och innehåller de objekt som du lade till medan appen kopplades från.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Visual Studio Verktyg för Apache Cordova]

## <a name="next-steps"></a>Nästa steg
* Granska mer avancerade offlinesynkroniseringsfunktioner, till exempel konfliktlösning i [exemplet med offlinesynkronisering]
* Granska API-referensen för offlinesynkronisering i [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova snabbstart]: app-service-mobile-cordova-get-started.md
[Exempel på offlinesynkronisering]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Verktyg för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

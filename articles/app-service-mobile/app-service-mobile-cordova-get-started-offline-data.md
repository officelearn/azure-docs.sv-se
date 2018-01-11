---
title: "Aktivera offlinesynkronisering för Azure Mobile App (Cordova) | Microsoft Docs"
description: "Lär dig mer om Apptjänst Mobile App till cache och synkronisera offlinedata i Cordova-program"
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Aktivera offlinesynkronisering av Cordova-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Den här självstudiekursen beskriver funktionen offlinesynkronisering i Azure Mobile Apps för Cordova. Offlinesynkronisering kan slutanvändarna interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas.  När enheten är online igen kan har de här ändringarna synkroniserats med tjänsten remote.

Den här kursen är baserad på Cordova quickstart lösning för Mobilappar som du skapar när du har slutfört vägledningen [Apache Cordova Snabbstart]. I den här kursen kan du uppdatera quickstart lösningen för att lägga till offline funktioner i Azure Mobile Apps.  Vi kan också markera offline-specifika koden i appen.

Mer information om funktionen offlinesynkronisering finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps]. Mer information om API-användning finns i [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Lägga till offlinesynkronisering i Snabbstart-lösning
Synkronisering offline-koden måste läggas till appen. Offlinesynkronisering kräver pluginprogrammet cordova-sqlite-storage hämtar läggs automatiskt till din app när plugin-program för Azure Mobile Apps ingår i projektet. Snabbstartsprojektet innehåller båda dessa plugin-program.

1. Öppna index.js i Visual Studio Solution Explorer och Ersätt följande kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    med den här koden:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ersätt sedan följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    med den här koden:

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

    Föregående kod tilläggen initiera det lokala arkivet och definiera en lokal tabell som matchar kolumnvärdena används i din Azure-serverdel. (Du behöver inte inkludera alla kolumnvärdena i den här koden.)  Den `version` underhålls av mobila serverdel och används för konfliktlösning.

    Du kan hämta en referens till sync-kontexten genom att anropa **getSyncContext**. Kontexten sync bevaras relationerna mellan tabellerna genom att spåra och överföra ändringar i alla tabeller som ett klientprogram har ändrat när `.push()` anropas.

3. Uppdatera programmets URL till din Mobilapp programmets URL.

4. Ersätt sedan den här koden:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    med den här koden:

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

    Föregående kod initierar sync-kontexten och anropar sedan getSyncTable (i stället för getTable) för att hämta en referens till den lokala tabellen.

    Den här koden använder den lokala databasen för alla skapa, läsa, uppdatera och ta bort CRUD-tabellåtgärder.

    Det här exemplet utför enkel felhantering på eventuella konflikter. Ett riktigt program skulle hantera olika fel som nätverksförhållanden och servern är i konflikt. Kodexempel, finns det [offlinesynkronisering exempel].

5. Lägg till den här funktionen om du vill utföra den faktiska synkroniseringen.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Du har bestämt när att skicka ändringar till serverdelen för Mobilappen genom att anropa **syncContext.push()**. Du kan exempelvis kalla **syncBackend** i en händelsehanterare för knappen knuten till en knapp för synkronisering.

## <a name="offline-sync-considerations"></a>Offlinesynkronisering överväganden

I det här exemplet i **push** metod för **syncContext** endast anropas på appen startas i Återanropsfunktionen för inloggning.  I ett riktigt program kan du också göra funktionen sync utlöses manuellt eller när nätverket tillstånd ändras.

När en pull körs mot en tabell som har väntande spåras lokala uppdateringar av sammanhanget, som hämtar åtgärden automatiskt utlösare en push. När du uppdaterar, lägga till och slutföra objekt i det här exemplet kan du utelämna en explicit **push** anropa, eftersom det kan vara redundant.

I den angivna koden, alla poster i tabellen remote todoItem frågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och fråga till **push**. Mer information finns i avsnittet *inkrementell synkronisering* i [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Valfritt) Inaktivera autentisering

Om du inte vill konfigurera autentisering innan tester offlinesynkronisering kommentera ut Återanropsfunktionen för inloggning, men lämna kod inuti Återanropsfunktionen tagit bort kommentarstecken från.  Efter kommentera bort inloggningen raderna koden som visas nedan:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu kan de app synkroniseras med Azure-serverdel när du kör appen.

## <a name="run-the-client-app"></a>Kör klientapp
Med offlinesynkronisering har nu aktiverats, kan du köra klientprogrammet minst en gång på varje plattform för att fylla i databasen för lokal lagring. Senare, simulera ett offline scenario och ändra data i det lokala arkivet medan appen är offline.

## <a name="optional-test-the-sync-behavior"></a>(Valfritt) Testa sync-beteende
I det här avsnittet kan du ändra klientprojektet för att simulera ett offline-scenario med en ogiltig programmets URL för din serverdel. När du lägger till eller ändra dataobjekt ändringarna lagras i det lokala arkivet, men synkroniserats inte med datalagret backend tills anslutningen har återupprättats.

1. Öppna projektfilen index.js i Solution Explorer och ändra URL-Adressen för programmet att peka till en ogiltig URL som följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Uppdatera CSP: N i index.html `<meta>` element med samma ogiltig URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skapa och köra klientappen och Lägg märke till att ett undantag loggas i konsolen när appen försöker synkronisera med serverdelen efter inloggning. Alla nya objekt som du lägger till finns bara i det lokala arkivet tills de pushas till mobila serverdel. Klientappen fungerar som om den är ansluten till serverdelen.

4. Stänga appen och starta om den för att kontrollera att de nya objekt som du skapade sparas på det lokala arkivet.

5. (Valfritt) Du kan använda Visual Studio för att visa din Azure SQL Database-tabellen för att se data i databasen inte har ändrats.

    Öppna i Visual Studio **Server Explorer**. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL-databastabell och dess innehåll.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Valfritt) Testa återanslutning till din mobila serverdel

I det här avsnittet kan du ansluta appen till mobila serverdelen som simulerar appen komma tillbaka till onlineläge. När du loggar in, synkroniseras data till din mobila serverdel.

1. Öppna index.js och återställa programmets URL.
2. Öppna index.html och korrigera programmets URL i CSP `<meta>` element.
3. Bygg och kör klientapp. Appen försöker synkronisera med serverdelen för mobilappen efter inloggning. Kontrollera att inga undantag loggas i Felsökningskonsolen.
4. (Valfritt) Visa den uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan backend-databas och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och det lokala arkivet och innehåller objekt som du har lagt till medan kopplades från din app.

## <a name="additional-resources"></a>Ytterligare resurser
* [offlinesynkronisering Data i Azure Mobile Apps]
* [Visual Studio Tools för Apache Cordova]

## <a name="next-steps"></a>Nästa steg
* Granska mer avancerade offlinesynkronisering funktioner, till exempel konfliktlösning i den [offlinesynkronisering exempel]
* Granska offlinesynkronisering API-referens i den [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova Snabbstart]: app-service-mobile-cordova-get-started.md
[offlinesynkronisering exempel]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[offlinesynkronisering Data i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

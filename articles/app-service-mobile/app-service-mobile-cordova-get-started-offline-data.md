---
title: Aktivera synkronisering offline för din Azure-Mobilapp (Cordova) | Microsoft Docs
description: Lär dig hur du använder App Service Mobile App till cache och synkronisering av offlinedata i din Cordova-App
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972158"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Aktivera synkronisering offline för din Cordova-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Den här självstudien innehåller funktionen offline-synkronisering i Azure Mobile Apps för Cordova. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas.  När enheten är online igen kan har de här ändringarna synkroniserats med fjärrtjänsten.

Den här självstudien är baserad på lösning för Cordova-Snabbstart för Mobile Apps som du skapar när du slutför självstudien [Snabbstart för Apache Cordova]. I den här självstudien får uppdatera du quickstart-lösningen för att lägga till Offlinefunktioner för Azure Mobile Apps.  Vi kan också markera offline-specifik kod i appen.

Mer information om funktionen offline-synkronisering, finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps]. Information om API-användning finns på [API-dokumentation](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Lägg till offlinesynkronisering i Snabbstart-lösning
Offlinesynkronisering-koden måste läggas till appen. Offlinesynkronisering kräver plugin cordova-sqlite-lagring, som hämtar läggs automatiskt till din app när plugin-programmet Azure Mobile Apps som ingår i projektet. Quickstart-projektet innehåller båda dessa plugin-program.

1. I Visual Studio Solution Explorer, Öppna index.js och Byt ut koden

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    med den här koden:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ersätt följande kod:

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

    Föregående kod tilläggen initiera det lokala arkivet och definiera en lokal tabell som matchar kolumnvärden som används i din Azure-serverdel. (Du behöver inte inkludera alla kolumnvärdena i den här koden.)  Den `version` underhålls av mobil serverdel och används för konfliktlösning.

    Du får en referens till sync-kontexten genom att anropa **getSyncContext**. Synkronisera kontext bevaras tabellrelationer genom att spåra och push-överföra ändringarna i alla tabeller som en klientapp har ändrat när `.push()` anropas.

3. Uppdatera programmets URL till din Mobilapp programmets URL.

4. Ersätt därefter den här koden:

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

    Föregående kod Initierar synkronisering kontexten och anropar sedan getSyncTable (i stället för getTable) för att hämta en referens till den lokala tabellen.

    Den här koden använder den lokala databasen för alla skapa, läsa, uppdatera och ta bort (CRUD) tabellåtgärder.

    Det här exemplet utför enkla felhantering på synkroniseringskonflikter. Ett riktigt program skulle hantera olika fel som nätverksförhållanden och servern är i konflikt. Kodexempel, finns det [offlinesynkronisering exempel].

5. Lägg sedan till den här funktionen om du vill utföra den faktiska synkroniseringen.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Du bestämmer dig för när du ska skicka ändringar till serverdelen för Mobilappen genom att anropa **syncContext.push()**. Du kan exempelvis kalla **syncBackend** i en händelsehanterare för knappen som är knutna till en knapp för synkronisering.

## <a name="offline-sync-considerations"></a>Överväganden för offline-synkronisering

I det här exemplet på **push** -metoden för **syncContext** endast anropas vid start av appen i Återanropsfunktionen för inloggning.  I ett verkligt program kan du också göra den här sync-funktionen som utlöses manuellt eller när nätverket tillstånd ändras.

När en pull körs mot en tabell som har väntande spåras lokala uppdateringar av den kontext som hämtar åtgärden automatiskt utlösare en push. När du uppdaterar, att lägga till och slutföra objekt i det här exemplet kan du utelämna den explicita **push** anropar, eftersom det kan vara redundant.

I den angivna koden alla poster i remote todoItem-tabellen tillfrågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och skicka frågor till **push**. Mer information finns i avsnittet *inkrementell synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Valfritt) Inaktivera autentisering

Om du inte vill konfigurera autentisering innan du testar offlinesynkronisering, kommentera ut återanropsfunktion för inloggningen, men lämna koden i Återanropsfunktionen tagit bort kommentarstecken från.  När du kommentera ut rader inloggning koden visas nedan:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu kan synkroniseringar app med Azure-serverdel när du kör appen.

## <a name="run-the-client-app"></a>Kör klientappen
Med offlinesynkronisering har nu aktiverats kan köra du klientprogrammet minst en gång på varje plattform för att fylla i lokalt Arkiv-databasen. Senare kan simulera en offlinescenario och ändra data i det lokala arkivet medan appen är offline.

## <a name="optional-test-the-sync-behavior"></a>(Valfritt) Testa sync-beteende
I det här avsnittet ska ändra du klientprojektet för att simulera en offlinescenario med hjälp av en ogiltig programmets URL för din serverdel. När du lägger till eller ändra dataobjekt dessa ändringar som hålls kvar i det lokala arkivet, men är inte synkroniserade med datalagret serverdelen tills anslutningen har återupprättats.

1. Öppna filen index.js projektet i Solution Explorer och ändra programmets URL så att den pekar till en ogiltig URL, som följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Uppdatera CSP: N i index.html, `<meta>` element med samma ogiltig URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skapa och kör klientappen och Lägg märke till att ett undantag loggas i konsolen när appen försöker synkronisera med serverdelen efter inloggning. Alla nya objekt som du lägger till finns bara i det lokala arkivet tills de skickas till den mobila serverdelstjänst. Klientappen fungerar som om den är ansluten till serverdelen.

4. Stäng appen och starta om den för att kontrollera att de nya objekt som du har skapat sparas till det lokala arkivet.

5. (Valfritt) Använda Visual Studio för att visa din Azure SQL Database-tabell för att se att data i databasen i serverdelen inte har ändrats.

    Öppna i Visual Studio **Server Explorer**. Gå till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Valfritt) Testa återanslutning till din mobila serverdel

I det här avsnittet kan du återansluta app på mobil serverdel, vilket simulerar appen kommer tillbaka till ett onlinetillstånd. När du loggar in, synkroniseras data till din mobila serverdelstjänst.

1. Öppna index.js och återställa programmets URL.
2. Öppna index.html och korrigera programmets URL i CSP: N `<meta>` element.
3. Bygg och kör klientappen. Appen försöker synkronisera med serverdelen för mobilappen efter inloggning. Kontrollera att inga undantag loggas i Felsökningskonsolen.
4. (Valfritt) Visa uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att dessa data har synkroniserats mellan databasen och det lokala arkivet.

    Observera att dessa data har synkroniserats mellan databasen och det lokala arkivet och innehåller de objekt som du har lagt till medan kopplades från din app.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Visual Studio Tools för Apache Cordova]

## <a name="next-steps"></a>Nästa steg
* Läs mer avancerade funktioner för offline-synkronisering, till exempel konfliktlösning i den [offlinesynkronisering exempel]
* Granska offlinesynkronisering API-referens i den [API-dokumentation](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Snabbstart för Apache Cordova]: app-service-mobile-cordova-get-started.md
[offlinesynkronisering exempel]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

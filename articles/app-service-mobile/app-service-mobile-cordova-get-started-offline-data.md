---
title: Aktivera synkronisering offline för din Azure-mobilapp (Cordova) | Microsoft Docs
description: Lär dig hur du använder App Service mobilapp för att cachelagra och synkronisera offline-data i ditt Cordova-program
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c5fcc98bf3da79d1978da6e67535a5a93a8fe321
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388855"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Aktivera synkronisering offline för din Cordova-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
I den här självstudien beskrivs funktionen offline-synkronisering i Azure Mobile Apps för Cordova. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp @ no__t-0viewing, lägga till eller ändra data @ no__t-1even när det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas.  När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

Den här självstudien baseras på Cordova snabb starts lösning för Mobile Apps som du skapar när du är klar med självstudien [Snabb start för Apache Cordova]. I den här självstudien uppdaterar du snabb starts lösningen för att lägga till offline-funktioner i Azure Mobile Apps.  Vi markerar också den offline-/regionsspecifika koden i appen.

Mer information om funktionen offline-synkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps]. Mer information om API-användning finns i [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Lägg till offline-synkronisering i snabb starts lösningen
Koden för offlinesynkronisering måste läggas till i appen. Offline-synkronisering kräver plugin-programmet Cordova-sqlite-Storage, som automatiskt läggs till i din app när Azure Mobile Apps-plugin-programmet ingår i projektet. Snabb starts projektet innehåller båda dessa plugin-program.

1. I Visual Studios Solution Explorer öppnar du index. js och ersätter följande kod

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

    Föregående kod lägger till i den lokala lagringen och definierar en lokal tabell som matchar de kolumn värden som används i Azures Server del. (Du behöver inte inkludera alla kolumn värden i den här koden.)  Fältet `version` underhålls av mobil Server delen och används för konflikt lösning.

    Du får en referens till Sync-kontexten genom att anropa **getSyncContext**. Den synkroniserade kontexten hjälper till att bevara tabell relationer genom att spåra och överföra ändringar i alla tabeller som en klient app har ändrat när `.push()` anropas.

3. Uppdatera programmets URL till appens URL för mobilapp.

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

    Föregående kod initierar kontexten för synkronisering och anropar sedan getSyncTable (i stället för getTable) för att få en referens till den lokala tabellen.

    I den här koden används den lokala databasen för alla åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) tabell.

    Det här exemplet utför enkel fel hantering vid synkroniseringskonflikter. Ett verkligt program hanterar de olika felen som nätverks förhållanden, Server konflikter och andra. Exempel på kod exempel finns i [offline Sync-exempel].

5. Lägg sedan till den här funktionen för att utföra den faktiska synkroniseringen.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Du bestämmer när du vill skicka ändringar till den mobila appens Server del genom att anropa **syncContext. push ()** . Du kan till exempel anropa **syncBackend** i en knapp händelse hanterare som är bunden till en synkronisera-knapp.

## <a name="offline-sync-considerations"></a>Överväganden offline-synkronisering

I exemplet kallas **push** -metoden för **syncContext** bara för att starta appen i motringningsfunktionen för inloggning.  I ett verkligt program kan du också göra den här synkroniseringsfunktionen aktive rad manuellt eller när nätverks tillstånd ändras.

När en pull körs mot en tabell som har väntande lokala uppdateringar som spåras av kontexten utlöser pull-åtgärden automatiskt en push. När du uppdaterar, lägger till och slutför objekt i det här exemplet kan du utelämna det explicita **push** -anropet eftersom det kan vara redundant.

I den angivna koden frågas alla poster i den fjärranslutna todoItem-tabellen, men det går också att filtrera poster genom att skicka ett fråge-ID och fråga till **push**. Mer information finns i avsnittet *stegvis synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>Valfritt Inaktivera autentisering

Om du inte vill konfigurera autentisering innan du testar offline-synkronisering, så kommentera ut funktionen motringning för inloggning, men lämna koden inuti motringningsfunktionen utan att kommenteras.  När du har kommenterat ut inloggnings raderna följer koden:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Appen synkroniseras nu med Azure-Server delen när du kör appen.

## <a name="run-the-client-app"></a>Kör klient programmet
Om du har aktiverat offline-synkronisering kan du köra klient programmet minst en gång på varje plattform för att fylla i den lokala lagrings databasen. Du kan senare simulera ett offline-scenario och ändra data i det lokala arkivet när appen är offline.

## <a name="optional-test-the-sync-behavior"></a>Valfritt Testa beteendet för synkronisering
I det här avsnittet ändrar du klient projektet för att simulera ett offline-scenario genom att använda en ogiltig program-URL för din server del. När du lägger till eller ändrar data objekt lagras dessa ändringar i det lokala arkivet, men synkroniseras inte till Server dels data lagret förrän anslutningen har återupprättats.

1. Öppna projekt filen index. js i Solution Explorer och ändra programmets URL så att den pekar på en ogiltig URL, t. ex. följande kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Uppdatera CSP-`<meta>`-elementet med samma ogiltiga URL i index. html.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skapa och kör klient programmet och Lägg märke till att ett undantag loggas i-konsolen när appen försöker synkronisera med Server delen efter inloggningen. Alla nya objekt som du lägger till finns bara i den lokala butiken tills de skickas till den mobila Server delen. Klient programmet fungerar som om det är anslutet till Server delen.

4. Stäng appen och starta om den för att kontrol lera att de nya objekt som du har skapat är sparade i det lokala arkivet.

5. Valfritt Använd Visual Studio för att visa din Azure SQL Database tabell för att se att data i backend-databasen inte har ändrats.

    Öppna **Server Explorer**i Visual Studio. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL Database-tabellen och dess innehåll.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Valfritt Testa åter anslutningen till din mobila Server del

I det här avsnittet ansluter du appen till den mobila Server delen, som simulerar appen som kommer tillbaka till ett online-tillstånd. När du loggar in synkroniseras data till din mobil Server del.

1. Öppna index. js igen och Återställ programmets URL.
2. Öppna index. html igen och korrigera programmets URL i CSP-`<meta>`-elementet.
3. Återskapa och kör klient programmet. Appen försöker synkronisera med den mobila appens Server del efter inloggningen. Kontrol lera att inga undantag loggas i fel söknings konsolen.
4. Valfritt Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan server dels databasen och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och den lokala lagrings platsen och innehåller de objekt som du lade till när din app kopplades från.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Visual Studio Tools för Apache Cordova]

## <a name="next-steps"></a>Nästa steg
* Granska mer avancerade funktioner för offline-synkronisering, till exempel konflikt lösning i [offline Sync-exempel]
* Granska offline Sync API-referensen i [API-dokumentationen](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Snabb start för Apache Cordova]: app-service-mobile-cordova-get-started.md
[offline Sync-exempel]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
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

---
title: "Aktivera offlinesynkronisering för Azure Mobile App (Xamarin iOS)"
description: "Lär dig mer om Apptjänst Mobile App till cache och synkronisering offline data i ditt Xamarin iOS-program"
documentationcenter: xamarin
author: ggailey777
manager: cfowler
editor: 
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: b5878d8a2e18cf08b6e9074ecf40cd732624f0c0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Aktivera offlinesynkronisering för en Xamarin.iOS-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen introducerar funktionen offlinesynkronisering i Azure Mobilappar för Xamarin.iOS. Offlinesynkronisering kan slutanvändarna interagera med en mobil app – visa, lägga till eller ändra data, även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med tjänsten remote.

I den här självstudiekursen uppdatera Xamarin.iOS-app-projekt från [skapa en Xamarin iOS-app] att stödja Azure Mobile Apps offline funktioner. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tilläggspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offlinesynkronisering finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner
Azure Mobile App offline funktionerna kan du samverka med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app, initiera en [SyncContext] till ett lokalt Arkiv. Referera till tabellen via gränssnittet [IMobileServiceSyncTable]. SQLite används som det lokala arkivet på enheten.

1. Öppna NuGet-Pakethanteraren i projektet som du har fyllt i den [skapa en Xamarin iOS-app] kursen, och sedan söka efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
2. Öppna filen QSTodoService.cs och Avkommentera den `#define OFFLINE_SYNC_ENABLED` definition.
3. Bygg och kör klientapp. Appen fungerar på samma sätt som innan du har aktiverat offlinesynkronisering. Den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen
I det här avsnittet bryta anslutningen till din mobilappsserverdel för att simulera en offline-situation. När du lägger till dataobjekt din undantagshanterare talar om att appen är i offline-läge. I det här tillståndet nya objekt lades till i det lokala arkivet och kommer att synkroniseras till serverdelen för mobilappen när push kör nästa gång i anslutet tillstånd.

1. Redigera QSToDoService.cs i delat projekt. Ändra den **applicationURL** att peka till en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller använda Flygplansläge.
2. Skapa och köra appen. Observera synkroniseringen misslyckades på Uppdatera när appen startas.
3. Ange nya objekt och Lägg märke till att push misslyckas med status [CancelledByNetworkError] varje gång du klickar på **spara**. Dock finns nya todo-objekt i det lokala arkivet tills de flyttas till serverdelen för mobilappen.  I ett produktionsprogram om du förhindra sådana undantag fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stänga appen och starta om den för att kontrollera att de nya objekt som du skapade sparas på det lokala arkivet.
5. (Valfritt) Om du har Visual Studio installerat på en dator kan du öppna **Server Explorer**. Navigera till din databas i **Azure**-> **SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL-databastabell och dess innehåll. Kontrollera att data i databasen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg, till exempel Fiddler eller Postman fråga din mobila serverdel med en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera program för att återansluta din mobilappsserverdel
Anslut appen till mobilappsserverdelen i det här avsnittet. Det här simulerar appen flyttar från ett offline-tillstånd till onlineläge med serverdelen för mobilappen.   Om du simulerade brott nätverket genom att inaktivera nätverksanslutningen, behövs inga kodändringar.
Aktivera nätverkets igen.  När du först kör programmet, den `RefreshDataAsync` metoden anropas. Detta i sin tur anropar `SyncAsync` att synkronisera dina lokala arkivet med backend-databasen.

1. Öppna QSToDoService.cs i delat projekt och återställa din ändring av den **applicationURL** egenskapen.
2. Bygg och kör appen. Appen synkroniserar dina lokala ändringar med Azure mobilappsserverdel med push och pull-åtgärder när den `OnRefreshItemsSelected` metoden Kör.
3. (Valfritt) Visa den uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Meddelande data har synkroniserats mellan Azure Mobile App backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `CompleteItemAsync`anrop `SyncAsync` till varje slutförd synkroniseringsobjekt med serverdelen för Mobilappen. `SyncAsync`anropar både sändning och mottagning.
   **När du kör en pull mot en tabell som klienten har gjort ändringar i en push på klientkontexten synkronisering utförs alltid först automatiskt**. Implicit push säkerställer att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Mer information om det här problemet finns i [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering
Klienten Xamarin-projektet som du hämtade när du har slutfört självstudien [skapa en Xamarin iOS-app] redan innehåller kod som stöder offline synkronisering med en lokal SQLite-databas. Här är en kort översikt över vad ingår redan i självstudiekursen koden. En översikt över funktionen finns [offlinesynkronisering Data i Azure Mobile Apps].

* Det lokala arkivet måste initieras innan alla tabellåtgärder kan utföras. Lokalt Arkiv databasen initieras när `QSTodoListViewController.ViewDidLoad()` kör `QSTodoService.InitializeStoreAsync()`. Den här metoden skapar en ny lokal SQLite databasen med hjälp av `MobileServiceSQLiteStore` klass som tillhandahålls av Azure Mobile App klient-SDK.

    Den `DefineTable` metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen `ToDoItem` i det här fallet. Typen måste inte innehålla alla kolumner som tillhör fjärrdatabasen. Det är möjligt att lagra bara en delmängd med kolumner.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Den `todoTable` tillhör `QSTodoService` är av den `IMobileServiceSyncTable` Skriv i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla skapa, läsa, uppdatera och ta bort CRUD-tabellåtgärder till databasen för lokal lagring.

    Du bestämmer dig för när ändringarna pushas till mobilappsserverdel i Azure genom att anropa `IMobileServiceSyncContext.PushAsync()`. Kontexten sync bevaras relationerna mellan tabellerna genom att spåra och överföra ändringar i alla tabeller som ett klientprogram har ändrat när `PushAsync` anropas.

    De angivna koden anropar `QSTodoService.SyncAsync()` ska synkroniseras när listan todoitem uppdateras eller ett todoitem läggs till eller slutförts. Appen synkroniseringar efter alla lokala ändringar. Om en pull körs mot en tabell som har väntande uppdateringar lokala spåras av sammanhanget, utlöser pull åtgärden automatiskt en kontext push först.

    I den angivna koden, alla poster i fjärransluten `TodoItem` tabell frågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och fråga till `PushAsync`. Mer information finns i avsnittet *inkrementell synkronisering* i [offlinesynkronisering Data i Azure Mobile Apps].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Ytterligare resurser
* [offlinesynkronisering Data i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK ta][8]

<!-- Images -->

<!-- URLs. -->
[skapa en Xamarin iOS-app]: app-service-mobile-xamarin-ios-get-started.md
[offlinesynkronisering Data i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

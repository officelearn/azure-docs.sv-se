---
title: Aktivera offlinesynkronisering (Xamarin iOS)
description: Lär dig hur du använder App Service Mobile App för att cachelagra och synkronisera offlinedata i ditt Xamarin iOS-program.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461308"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Aktivera offlinesynkronisering för din Xamarin.iOS-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien introducerar offlinesynkroniseringsfunktionen i Azure Mobile Apps för Xamarin.iOS. Offlinesynkronisering gör det möjligt för slutanvändare att interagera med en mobilapp – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringar lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

I den här självstudien uppdaterar du Xamarin.iOS-appprojektet från [Skapa en Xamarin iOS-app] för att stödja offlinefunktionerna i Azure Mobile Apps. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till paketen för dataåtkomsttillägg i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om synkroniseringsfunktionen offline finns i avsnittet [Offlinedatasynkning i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen så att den stöder offlinefunktioner
Med offlinefunktionerna i Azure Mobile App kan du interagera med en lokal databas när du befinner dig i ett offlinescenario. Om du vill använda dessa funktioner i appen initierar du en [SyncContext] till en lokal butik. Referera till tabellen via gränssnittet [IMobileServiceSyncTable].Reference your table through the [IMobileServiceSyncTable] interface. SQLite används som lokal butik på enheten.

1. Öppna NuGet-pakethanteraren i projektet som du slutförde i självstudien [Skapa en Xamarin iOS-app] och sök sedan efter och installera **paketet Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
2. Öppna QSTodoService.cs filen och ta av `#define OFFLINE_SYNC_ENABLED` dig den.
3. Återskapa och kör klientappen. Appen fungerar på samma sätt som den gjorde innan du aktiverade offlinesynkronisering. Den lokala databasen fylls dock nu med data som kan användas i ett offlinescenario.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Uppdatera appen för att koppla från backend
I det här avsnittet bryter du anslutningen till din mobile app-backend för att simulera en offlinesituation. När du lägger till dataobjekt talar undantagshanteraren om att appen är i offlineläge. I det här läget har nya objekt som lagts till i den lokala butiken och synkroniseras till mobilappens backend när push körs nästa gång i ett anslutet tillstånd.

1. Redigera QSToDoService.cs i det delade projektet. Ändra **applicationURL** så att den pekar på en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offlinebeteende genom att inaktivera wifi och mobilnät på enheten eller använda flygplansläge.
2. Skapa och kör appen. Observera att synkroniseringen misslyckades vid uppdatering när appen startades.
3. Ange nya objekt och märker att push misslyckas med en [CancelledByNetworkError]-status varje gång du klickar på **Spara**. De nya todo-objekten finns dock i den lokala butiken tills de kan skickas till mobilappens backend.  Om du undertrycker dessa undantag i en produktionsapp fungerar klientappen som om den fortfarande är ansluten till mobilappens backend.
4. Stäng appen och starta om den för att kontrollera att de nya objekten som du har skapat sparas i den lokala arkivet.
5. (Valfritt) Om du har Installerat Visual Studio på en dator öppnar du **Server Explorer**. Navigera till databasen i **Azure**-> **SQL Databases**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll. Kontrollera att data i backend-databasen inte har ändrats.
6. (Valfritt) Använd ett REST-verktyg som Fiddler eller Postman för att fråga din `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`mobila backend med hjälp av en GET-fråga i formuläret .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Uppdatera appen för att återansluta din mobile app-backend
I det här avsnittet ansluter du appen igen till mobilappens backend. Detta simulerar appen som flyttar från ett offlinetillstånd till ett onlinetillstånd med mobilappens backend.   Om du simulerade nätverksavbrottet genom att stänga av nätverksanslutningen behövs inga kodändringar.
Slå på nätverket igen.  När du kör programmet `RefreshDataAsync` första gången anropas metoden. Detta i `SyncAsync` sin tur anlöp för att synkronisera din lokala butik med backend-databasen.

1. Öppna QSToDoService.cs i det delade projektet och återställ ändringen av **applicationURL-egenskapen.**
2. Återskapa och kör appen. Appen synkroniserar dina lokala ändringar med Azure Mobile App-backend `OnRefreshItemsSelected` med hjälp av push- och pull-åtgärder när metoden körs.
3. (Valfritt) Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan Azure Mobile App-serverdelsdatabasen och det lokala arkivet.
4. I appen klickar du på kryssrutan bredvid några objekt för att slutföra dem i den lokala butiken.

   `CompleteItemAsync`för `SyncAsync` att synkronisera varje slutförd objekt med mobile-appens backend. `SyncAsync`samtal både tryck och dra.
   **När du kör ett handtag mot en tabell som klienten har gjort ändringar i, körs alltid en push på klientsynkroniseringskontexten först automatiskt**. Den implicita push säkerställer att alla tabeller i den lokala butiken tillsammans med relationer förblir konsekventa. Mer information om det här beteendet finns [i Offlinedatasynkronisering i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska klientsynkroniseringskoden
Xamarin-klientprojektet som du hämtade när du slutförde självstudien [Skapa en Xamarin iOS-app] innehåller redan kod som stöder offlinesynkronisering med hjälp av en lokal SQLite-databas. Här är en kort översikt över vad som redan ingår i handledningskoden. En begreppsmässig översikt över funktionen finns [i Offlinedatasynkronisering i Azure Mobile Apps].

* Innan några tabellåtgärder kan utföras måste det lokala arkivet initieras. Den lokala arkivdatabasen `QSTodoListViewController.ViewDidLoad()` initieras `QSTodoService.InitializeStoreAsync()`när den körs . Den här metoden skapar en ny lokal `MobileServiceSQLiteStore` SQLite-databas med hjälp av klassen som tillhandahålls av Azure Mobile App-klienten SDK.

    Metoden `DefineTable` skapar en tabell i det lokala arkivet som `ToDoItem` matchar fälten i den angivna typen, i det här fallet. Typen behöver inte innehålla alla kolumner som finns i fjärrdatabasen. Det är möjligt att lagra bara en delmängd av kolumner.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Medlemmen `todoTable` i `QSTodoService` är `IMobileServiceSyncTable` av typen `IMobileServiceTable`i stället för . IMobileServiceSyncTable dirigerar alla tabellåtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) till den lokala butiksdatabasen.

    Du bestämmer när ändringarna ska skjutas till Azure `IMobileServiceSyncContext.PushAsync()`Mobile App-backend genom att anropa . Synkroniseringskontexten hjälper till att bevara tabellrelationer genom att `PushAsync` spåra och trycka på ändringar i alla tabeller som en klientapp har ändrat när den anropas.

    Den angivna `QSTodoService.SyncAsync()` koden anropar för synkronisering när todoitem-listan uppdateras eller en todoitem läggs till eller slutförs. Appen synkroniseras efter varje lokal förändring. Om ett handtag körs mot en tabell som har väntande lokala uppdateringar spåras av kontexten, utlöser den pull-åtgärden automatiskt en kontext push först.

    I den angivna koden efterfrågas `TodoItem` alla poster i fjärrtabellen, men det är också möjligt `PushAsync`att filtrera poster genom att skicka ett fråge-ID och fråga till . Mer information finns i avsnittet *Inkrementell synkronisering* i [offlinedatasynkronisering i Azure Mobile Apps].

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
* [Datasynkronisering offline i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Skapa en Xamarin iOS-app]: app-service-mobile-xamarin-ios-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

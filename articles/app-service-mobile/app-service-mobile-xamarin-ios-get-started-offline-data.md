---
title: Aktivera synkronisering offline för din Azure-Mobilapp (Xamarin-iOS)
description: Lär dig hur du använder App Service Mobile App till cache och synkronisering av offlinedata i ditt Xamarin iOS-program
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 6a43ed0a50082cc37587752631c707bf9b5059ab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097471"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Aktivera offlinesynkronisering för en Xamarin.iOS-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien innehåller funktionen offline-synkronisering i Azure Mobile Apps för Xamarin.iOS. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med fjärrtjänsten.

I de här självstudierna uppdatera Xamarin.iOS app-projekt från [skapa en Xamarin iOS-app] för Offlinefunktioner för Azure Mobile Apps. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tillägg-paket i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering, finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner
Offlinefunktioner för Azure Mobile-appen kan du interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app måste initiera en [SyncContext] till ett lokalt Arkiv. Referera till tabellen via gränssnittet [IMobileServiceSyncTable]. SQLite används som det lokala arkivet på enheten.

1. Öppna NuGet-Pakethanteraren i projektet som du har slutfört i den [skapa en Xamarin iOS-app] självstudien och sedan söka efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
2. Öppna filen QSTodoService.cs och ta bort kommentarerna i `#define OFFLINE_SYNC_ENABLED` definition.
3. Bygg och kör klientappen. Appen fungerar på samma sätt som innan du har aktiverat synkronisering offline. Den lokala databasen fylls men nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen
I det här avsnittet ska bryta du anslutningen till din mobilappsserverdel att simulera en offline situation. När du lägger till dataobjekt anger din undantagshanterare att appen är i offline-läge. I det här tillståndet nya objekt har lagts till i det lokala arkivet och kommer att synkroniseras till serverdelen för mobilappen när push nästa körning i anslutet tillstånd.

1. Redigera QSToDoService.cs i delade projekt. Ändra den **applicationURL** så att den pekar till en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller med hjälp av Flygplansläge.
2. Skapa och kör appen. Lägg märke till din synkroniseringen misslyckades vid uppdatering när appen startas.
3. Ange nya objekt och Observera att push misslyckas med statusen [CancelledByNetworkError] varje gång du klickar på **spara**. Men finns de nya objekt i det lokala arkivet tills de kan skickas till serverdelen för mobilappen.  I en produktionsapp är om du ignorera de här undantagen fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stäng appen och starta om den för att kontrollera att de nya objekt som du har skapat sparas till det lokala arkivet.
5. (Valfritt) Om du har Visual Studio installerat på en dator, öppna **Server Explorer**. Gå till din databas i **Azure**-> **SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll. Kontrollera att data i databasen i serverdelen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg som Fiddler eller Postman för att fråga din mobila serverdelstjänst, med hjälp av en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta din mobilappsserverdel
Återanslut till appen till serverdelen för mobilappen i det här avsnittet. Det här simulerar appen flyttar från ett offline-tillstånd till ett onlinetillstånd med serverdelen för mobilappen.   Om du simulerade brott nätverk genom att stänga av nätverksanslutning, behövs inga kodändringar alls.
Aktivera nätverket igen.  Första gången du kör programmet och den `RefreshDataAsync` metoden anropas. Detta i sin tur anropar `SyncAsync` att synkronisera din lokala arkivet med backend-databas.

1. Öppna QSToDoService.cs i delat projekt och återställa din ändring av den **applicationURL** egenskapen.
2. Återskapa och köra appen. Appen synkroniserar dina lokala ändringar med Azure Mobile App-serverdel push och pull-åtgärder när den `OnRefreshItemsSelected` metoden Kör.
3. (Valfritt) Visa uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att dessa data har synkroniserats mellan Azure-Mobilapp backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `CompleteItemAsync` anrop `SyncAsync` att synkroniseringen varje har slutförts objekt med serverdelen för Mobilappen. `SyncAsync` anropar både sändning och mottagning.
   **När du kör en hämtning mot en tabell som klienten har gjort ändringar i en push-installation på synkronisering klientkontexten utförs alltid först automatiskt**. Implicit push säkerställer att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Mer information om detta finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering
Klienten Xamarin-projektet som du laddade ned när du har slutfört självstudien [skapa en Xamarin iOS-app] redan innehåller koden som har stöd för offlinesynkronisering med hjälp av en lokal SQLite-databas. Här är en kort översikt över vad som redan ingår i självstudiekursen koden. Läs en översikt över funktionen [Datasynkronisering offline i Azure Mobile Apps].

* Innan tabellåtgärder kan utföras, måste det lokala arkivet initieras. Databasen för lokal lagring har initierats när `QSTodoListViewController.ViewDidLoad()` kör `QSTodoService.InitializeStoreAsync()`. Den här metoden skapar en ny lokal SQLite databas med den `MobileServiceSQLiteStore` klass som tillhandahålls av SDK för Azure Mobile App-klient.

    Den `DefineTable` metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen `ToDoItem` i det här fallet. Typen har inte att inkludera alla kolumner som finns i fjärrdatabasen. Det är möjligt att lagra bara en delmängd med kolumner.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Den `todoTable` medlem `QSTodoService` är av den `IMobileServiceSyncTable` Skriv i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla skapa, läsa, uppdatera och ta bort (CRUD) tabellåtgärder till lokalt Arkiv-databasen.

    Du bestämmer dig för när ändringarna skickas till serverdelen för Mobilappen i Azure genom att anropa `IMobileServiceSyncContext.PushAsync()`. Synkronisera kontext bevaras tabellrelationer genom att spåra och push-överföra ändringarna i alla tabeller som en klientapp har ändrat när `PushAsync` anropas.

    De angivna koden anropar `QSTodoService.SyncAsync()` ska synkroniseras när todoitem listan uppdateras eller ett todoitem läggs till eller har slutförts. App-synkroniseringar efter varje lokal ändring. Om en hämtning körs mot en tabell som har väntande lokala uppdateringar spåras av sammanhanget, utlöser pull åtgärden automatiskt en kontext push först.

    I den angivna koden alla poster i fjärransluten `TodoItem` tabell tillfrågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och skicka frågor till `PushAsync`. Mer information finns i avsnittet *inkrementell synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

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

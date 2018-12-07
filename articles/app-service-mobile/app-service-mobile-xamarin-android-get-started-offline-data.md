---
title: Aktivera synkronisering offline för din Azure-Mobilapp (Android, Xamarin)
description: Lär dig hur du använder App Service Mobile App till cache och synkronisering av offlinedata i Xamarin Android-program
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: ca0eaf9e47b88bc0df8e7f050d8558c23d884f78
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999309"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Aktivera synkronisering offline för din mobila Xamarin.Android-app

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt

Den här självstudien innehåller funktionen offline-synkronisering i Azure Mobile Apps för Xamarin.Android. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas.
När enheten är online igen kan har de här ändringarna synkroniserats med fjärrtjänsten.

I den här självstudien får du uppdatera klientprojektet från självstudierna [skapa en Xamarin Android-app] för Offlinefunktioner för Azure Mobile Apps. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tillägg-paket i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering, finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner

Offlinefunktioner för Azure Mobile-appen kan du interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app måste du initiera en [SyncContext] till ett lokalt Arkiv. Referenstabell via den [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) gränssnitt. SQLite används som det lokala arkivet på enheten.

1. I Visual Studio, öppnar du NuGet-Pakethanteraren i projektet som du har slutfört i den [skapa en Xamarin Android-app] självstudien.  Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
2. Öppna filen ToDoActivity.cs och ta bort kommentarerna i `#define OFFLINE_SYNC_ENABLED` definition.
3. I Visual Studio trycker du på den **F5** att omkompilera och kör klientappen. Appen fungerar på samma sätt som innan du har aktiverat synkronisering offline. Den lokala databasen fylls men nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen

I det här avsnittet ska bryta du anslutningen till din mobilappsserverdel att simulera en offline situation. När du lägger till dataobjekt anger din undantagshanterare att appen är i offline-läge. I det här tillståndet nya objekt har lagts till i det lokala arkivet och synkroniseras till serverdelen för mobilappen när en push körs i ett anslutet tillstånd.

1. Redigera ToDoActivity.cs i delade projekt. Ändra den **applicationURL** så att den pekar till en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller med hjälp av Flygplansläge.
2. Tryck på **F5** att bygga och köra appen. Lägg märke till din synkroniseringen misslyckades vid uppdatering när appen startas.
3. Ange nya objekt och Observera att push misslyckas med statusen [CancelledByNetworkError] varje gång du klickar på **spara**. Men finns de nya objekt i det lokala arkivet tills de kan skickas till serverdelen för mobilappen.  I en produktionsapp är om du ignorera de här undantagen fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stäng appen och starta om den för att kontrollera att de nya objekt som du har skapat sparas till det lokala arkivet.
5. (Valfritt) Öppna i Visual Studio **Server Explorer**. Gå till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll. Kontrollera att data i databasen i serverdelen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg som Fiddler eller Postman för att fråga din mobila serverdelstjänst, med hjälp av en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta din mobilappsserverdel

Återanslut till appen till serverdelen för mobilappen i det här avsnittet. Första gången du kör programmet och den `OnCreate` händelse hanteraren anrop `OnRefreshItemsSelected`. Den här metoden anropar `SyncAsync` att synkronisera din lokala arkivet med backend-databas.

1. Öppna ToDoActivity.cs i delat projekt och återställa din ändring av den **applicationURL** egenskapen.
2. Tryck på den **F5** att bygga och köra appen. Appen synkroniserar dina lokala ändringar med Azure Mobile App-serverdel push och pull-åtgärder när den `OnRefreshItemsSelected` metoden Kör.
3. (Valfritt) Visa uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att dessa data har synkroniserats mellan Azure-Mobilapp backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `CheckItem` anrop `SyncAsync` att synkroniseringen varje har slutförts objekt med serverdelen för Mobilappen. `SyncAsync` anropar både sändning och mottagning. **När du kör en hämtning mot en tabell som klienten har gjort ändringar i en push alltid körs automatiskt**. Detta säkerställer att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Det här beteendet kan resultera i ett oväntat push. Mer information om detta finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering

Klienten Xamarin-projektet som du laddade ned när du har slutfört självstudien [skapa en Xamarin Android-app] redan innehåller koden som har stöd för offlinesynkronisering med hjälp av en lokal SQLite-databas. Här är en kort översikt över vad som redan ingår i självstudiekursen koden. Läs en översikt över funktionen [Datasynkronisering offline i Azure Mobile Apps].

* Innan tabellåtgärder kan utföras, måste det lokala arkivet initieras. Databasen för lokal lagring har initierats när `ToDoActivity.OnCreate()` kör `ToDoActivity.InitLocalStoreAsync()`. Den här metoden skapar en lokal SQLite databas med den `MobileServiceSQLiteStore` klass som tillhandahålls av SDK för Azure Mobile Apps-klient.

    Den `DefineTable` metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen `ToDoItem` i det här fallet. Typen har inte att inkludera alla kolumner som finns i fjärrdatabasen. Det är möjligt att lagra bara en delmängd med kolumner.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Den `toDoTable` medlem `ToDoActivity` är av den `IMobileServiceSyncTable` Skriv i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla skapa, läsa, uppdatera och ta bort (CRUD) tabellåtgärder till lokalt Arkiv-databasen.

    Du bestämmer dig för när ändringarna skickas till serverdelen för Mobilappen i Azure genom att anropa `IMobileServiceSyncContext.PushAsync()`. Synkronisera kontext bevaras tabellrelationer genom att spåra och push-överföra ändringarna i alla tabeller som en klientapp har ändrat när `PushAsync` anropas.

    De angivna koden anropar `ToDoActivity.SyncAsync()` ska synkroniseras när todoitem listan uppdateras eller ett todoitem läggs till eller har slutförts. Kod synkroniseringar efter varje lokal ändring.

    I den angivna koden alla poster i fjärransluten `TodoItem` tabell tillfrågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och skicka frågor till `PushAsync`. Mer information finns i avsnittet *inkrementell synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Ytterligare resurser

* [Datasynkronisering offline i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[Skapa en Xamarin Android-app]: ./app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Skapa en Xamarin Android-app]: app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

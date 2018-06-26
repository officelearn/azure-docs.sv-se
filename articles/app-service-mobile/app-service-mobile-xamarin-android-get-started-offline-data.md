---
title: Aktivera offlinesynkronisering för Azure Mobile App (Xamarin Android)
description: Lär dig mer om Apptjänst Mobile App till cache och synkronisera offlinedata i Xamarin Android-program
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
ms.openlocfilehash: e0146be345215701cf1afe86345afc286933d51b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750976"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Aktivera offlinesynkronisering av din mobila Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen introducerar funktionen offlinesynkronisering i Azure Mobilappar för Xamarin.Android. Offlinesynkronisering kan slutanvändarna interagera med en mobil app – visa, lägga till eller ändra data, även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas.
När enheten är online igen kan har de här ändringarna synkroniserats med tjänsten remote.

I kursen får du uppdaterar klientprojektet från kursen [skapa en Xamarin Android-app] att stödja Azure Mobile Apps offline funktioner. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tilläggspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offlinesynkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner
Azure Mobile App offline funktionerna kan du samverka med en lokal databas när du är i ett offline-scenario. För att använda funktionerna i din app måste du initiera en [SyncContext] till ett lokalt Arkiv. Referenstabell via den [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) gränssnitt. SQLite används som det lokala arkivet på enheten.

1. Öppna i Visual Studio NuGet-Pakethanteraren i projektet som du har slutfört i den [skapa en Xamarin Android-app] kursen.  Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
2. Öppna filen ToDoActivity.cs och Avkommentera den `#define OFFLINE_SYNC_ENABLED` definition.
3. I Visual Studio trycker du på den **F5** för att omkompilera och köra klientapp-. Appen fungerar på samma sätt som innan du har aktiverat offlinesynkronisering. Den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen
I det här avsnittet bryta anslutningen till din mobilappsserverdel för att simulera en offline-situation. När du lägger till dataobjekt din undantagshanterare talar om att appen är i offline-läge. I det här tillståndet nya objekt och lagts till i det lokala arkivet synkroniseras till serverdelen för mobilappen när en push körs i ett anslutet tillstånd.

1. Redigera ToDoActivity.cs i delat projekt. Ändra den **applicationURL** att peka till en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller använda Flygplansläge.
2. Tryck på **F5** att skapa och köra appen. Observera synkroniseringen misslyckades på Uppdatera när appen startas.
3. Ange nya objekt och Lägg märke till att push misslyckas med status [CancelledByNetworkError] varje gång du klickar på **spara**. Dock finns nya todo-objekt i det lokala arkivet tills de flyttas till serverdelen för mobilappen.  I ett produktionsprogram om du förhindra sådana undantag fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stänga appen och starta om den för att kontrollera att de nya objekt som du skapade sparas på det lokala arkivet.
5. (Valfritt) Öppna i Visual Studio **Server Explorer**. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL-databastabell och dess innehåll. Kontrollera att data i databasen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg, till exempel Fiddler eller Postman fråga din mobila serverdel med en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera program för att återansluta din mobilappsserverdel
Anslut appen till mobilappsserverdelen i det här avsnittet. När du först kör programmet, den `OnCreate` händelsehanteraranrop `OnRefreshItemsSelected`. Den här metoden anropar `SyncAsync` att synkronisera dina lokala arkivet med backend-databasen.

1. Öppna ToDoActivity.cs i delat projekt och återställa din ändring av den **applicationURL** egenskapen.
2. Tryck på den **F5** för att omkompilera och kör appen. Appen synkroniserar dina lokala ändringar med Azure mobilappsserverdel med push och pull-åtgärder när den `OnRefreshItemsSelected` metoden Kör.
3. (Valfritt) Visa den uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Meddelande data har synkroniserats mellan Azure Mobile App backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `CheckItem` anrop `SyncAsync` till varje slutförd synkroniseringsobjekt med serverdelen för Mobilappen. `SyncAsync` anropar både sändning och mottagning. **När du kör en pull mot en tabell som klienten har gjort ändringar i en push alltid körs automatiskt**. Detta säkerställer att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Detta kan resultera i ett oväntat push. Mer information om det här problemet finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering
Klienten Xamarin-projektet som du hämtade när du har slutfört självstudien [skapa en Xamarin Android-app] redan innehåller kod som stöder offline synkronisering med en lokal SQLite-databas. Här är en kort översikt över vad ingår redan i självstudiekursen koden. En översikt över funktionen finns [Datasynkronisering offline i Azure Mobile Apps].

* Det lokala arkivet måste initieras innan alla tabellåtgärder kan utföras. Lokalt Arkiv databasen initieras när `ToDoActivity.OnCreate()` kör `ToDoActivity.InitLocalStoreAsync()`. Den här metoden skapar en lokal SQLite databasen med hjälp av den `MobileServiceSQLiteStore` klass som tillhandahålls av Azure Mobile Apps klient-SDK.

    Den `DefineTable` metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen `ToDoItem` i det här fallet. Typen måste inte innehålla alla kolumner som tillhör fjärrdatabasen. Det är möjligt att lagra bara en delmängd med kolumner.

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
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Den `toDoTable` tillhör `ToDoActivity` är av den `IMobileServiceSyncTable` Skriv i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla skapa, läsa, uppdatera och ta bort CRUD-tabellåtgärder till databasen för lokal lagring.

    Du bestämmer dig för när ändringarna pushas till mobilappsserverdel i Azure genom att anropa `IMobileServiceSyncContext.PushAsync()`. Kontexten sync bevaras relationerna mellan tabellerna genom att spåra och överföra ändringar i alla tabeller som ett klientprogram har ändrat när `PushAsync` anropas.

    De angivna koden anropar `ToDoActivity.SyncAsync()` ska synkroniseras när listan todoitem uppdateras eller ett todoitem läggs till eller slutförts. Koden synkroniseringar efter alla lokala ändringar.

    I den angivna koden, alla poster i fjärransluten `TodoItem` tabell frågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och fråga till `PushAsync`. Mer information finns i avsnittet *inkrementell synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

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
* [Azure Mobile Apps .NET SDK ta][8]

<!-- URLs. -->
[Skapa en Xamarin Android-app]: ../app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Skapa en Xamarin Android-app]: app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

---
title: Aktivera synkronisering offline för din Azure-mobilapp (Xamarin iOS)
description: Lär dig hur du använder App Service mobilapp för att cachelagra och Synkronisera offlinedata i din Xamarin iOS-app
documentationcenter: xamarin
author: elamalani
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fc4fd639f364a0d0852ed066d26eb0e3cf73e68c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388373"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Aktivera offlinesynkronisering för en Xamarin.iOS-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
I den här självstudien beskrivs funktionen offline-synkronisering i Azure Mobile Apps för Xamarin. iOS. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp – Visa, lägga till eller ändra data, även när det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

I den här självstudien uppdaterar du Xamarin. iOS-exempelprojektet från [skapa en Xamarin iOS-app] som stöder offline-funktionerna i Azure Mobile Apps. Om du inte använder det hämtade projektet för snabb starts Server måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klient appen så att den stöder offline-funktioner
Offline-funktioner i Azure Mobile Apps gör att du kan interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app initierar du en [SyncContext] till ett lokalt Arkiv. Referera till din tabell via gränssnittet [IMobileServiceSyncTable]. SQLite används som lokal lagring på enheten.

1. Öppna NuGet Package Manager i projektet som du har slutfört i själv studie kursen [skapa en Xamarin iOS-app] och Sök sedan efter och installera paketet **Microsoft. Azure. Mobile. client. SQLiteStore** NuGet.
2. Öppna QSTodoService.cs-filen och ta bort kommentaren till `#define OFFLINE_SYNC_ENABLED`-definitionen.
3. Återskapa och kör klient programmet. Appen fungerar på samma sätt som innan du aktiverade offlinesynkronisering. Men den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen för att koppla från Server delen
I det här avsnittet bryter du anslutningen till Server delen för mobilappar för att simulera en offline-situation. När du lägger till data objekt visar undantags hanteraren att appen är i offline-läge. I det här läget kommer nya objekt som lagts till i det lokala arkivet att synkroniseras till Server delen för mobilappar när push körs nästa gång den körs i ett anslutet tillstånd.

1. Redigera QSToDoService.cs i det delade projektet. Ändra **applicationURL** så att den pekar på en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline-beteende genom att inaktivera trådlösa nätverk på enheten eller i flyg Plans läge.
2. Skapa och kör appen. Observera att synkroniseringen misslyckades vid uppdatering när appen startades.
3. Ange nya objekt och Lägg märke till att push-meddelandet Miss lyckas med statusen [CancelledByNetworkError] varje gången du klickar på **Spara**. De nya att göra-objekt finns dock i den lokala lagrings platsen tills de kan flyttas till Server delen för mobilappar.  Om du under en produktions-app utelämnar de här undantagen fungerar klient programmet som om det fortfarande är anslutet till den mobila appens Server del.
4. Stäng appen och starta om den för att kontrol lera att de nya objekt som du har skapat är sparade i det lokala arkivet.
5. Valfritt Om du har Visual Studio installerat på en dator öppnar du **Server Explorer**. Navigera till din databas i **Azure**-> **SQL-databaser**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL Database-tabellen och dess innehåll. Kontrol lera att data i backend-databasen inte har ändrats.
6. Valfritt Använd ett REST-verktyg som Fiddler eller Postman för att fråga din mobil Server del med en GET-fråga i formatet `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta till Server delen för mobilappen
I det här avsnittet ansluter du appen till Server delen för mobilappar. Detta simulerar att appen flyttas från ett offline-tillstånd till ett online-tillstånd med den mobila appens Server del.   Om du har simulerat nätverks brott genom att stänga av nätverks anslutningen behövs inga kod ändringar.
Aktivera nätverket igen.  Första gången du kör programmet anropas metoden `RefreshDataAsync`. Detta i tur anrop `SyncAsync` för att synkronisera ditt lokala lager med backend-databasen.

1. Öppna QSToDoService.cs i det delade projektet och Återställ ändringen av egenskapen **applicationURL** .
2. Återskapa och kör appen. Appen synkroniserar dina lokala ändringar med Server delen för Azure Mobile-appen med push-och pull-åtgärder när metoden `OnRefreshItemsSelected` körs.
3. Valfritt Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan server dels databasen för Azure Mobile app och det lokala arkivet.
4. I appen klickar du på kryss rutan bredvid några objekt för att slutföra dem i det lokala arkivet.

   `CompleteItemAsync`-anrop `SyncAsync` för att synkronisera varje slutfört objekt med Server delen för mobilappar. `SyncAsync` anropar både push och pull.
   **När du kör en pull-överföring mot en tabell som klienten har gjort ändringar i, utförs alltid en push-överföring på klientens Sync-kontext först automatiskt**. Implicit push garanterar att alla tabeller i den lokala lagringen tillsammans med relationer förblir konsekventa. Mer information om det här problemet finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska koden för klientens synkronisering
Det Xamarin-klientcertifikat som du laddade ned när du slutförde självstudien [skapa en Xamarin iOS-app] innehåller redan kod som stöder offline-synkronisering med en lokal SQLite-databas. Här är en kort översikt över vad som redan finns med i själv studie kursen. En översikt över funktionen finns i [Datasynkronisering offline i Azure Mobile Apps].

* Innan alla tabell åtgärder kan utföras måste det lokala arkivet initieras. Den lokala lagrings databasen initieras när `QSTodoListViewController.ViewDidLoad()` kör `QSTodoService.InitializeStoreAsync()`. Den här metoden skapar en ny lokal SQLite-databas med hjälp av klassen `MobileServiceSQLiteStore` som tillhandahålls av klient-SDK: n för Azure Mobile app.

    Metoden `DefineTable` skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen, `ToDoItem` i det här fallet. Typen behöver inte innehålla alla kolumner som finns i fjärrdatabasen. Det går att lagra bara en delmängd av kolumnerna.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* @No__t-0-medlemmen i `QSTodoService` är av `IMobileServiceSyncTable`-typ i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla tabell åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) till den lokala lagrings databasen.

    Du bestämmer när ändringarna skickas till Server delen för Azure Mobile-appen genom att anropa `IMobileServiceSyncContext.PushAsync()`. Den synkroniserade kontexten hjälper till att bevara tabell relationer genom att spåra och överföra ändringar i alla tabeller som en klient app har ändrat när `PushAsync` anropas.

    Det angivna kod anropet `QSTodoService.SyncAsync()` synkroniseras när todoitem-listan uppdateras eller en todoitem läggs till eller slutförs. Appen synkroniseras efter varje lokal ändring. Om en pull körs mot en tabell som har väntande lokala uppdateringar som spåras av kontexten, utlöser pull-åtgärden automatiskt en kontext-push först.

    I den angivna koden frågas alla poster i den fjärranslutna `TodoItem`-tabellen, men det går också att filtrera poster genom att skicka ett fråge-ID och fråga till `PushAsync`. Mer information finns i avsnittet *stegvis synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

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
* [Azure Mobile Apps .NET SDK-HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Skapa en Xamarin iOS-app]: app-service-mobile-xamarin-ios-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

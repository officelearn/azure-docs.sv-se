---
title: Aktivera synkronisering offline för din Azure-mobilapp (Xamarin Android)
description: Lär dig hur du använder App Service mobilapp för att cachelagra och Synkronisera offlinedata i din Xamarin Android-app
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a7c9672db0a7cc12e78846bf82c54e3ee383c66
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025114"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Aktivera synkronisering offline för din Xamarin. Android-mobilapp

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt

I den här självstudien beskrivs funktionen offline-synkronisering i Azure Mobile Apps för Xamarin. Android. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp – Visa, lägga till eller ändra data, även när det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas.
När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

I den här självstudien uppdaterar du klient projektet från självstudien [skapa en Xamarin Android-app] som stöder offline-funktionerna i Azure Mobile Apps. Om du inte använder det hämtade projektet för snabb starts Server måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klient appen så att den stöder offline-funktioner

Offline-funktioner i Azure Mobile Apps gör att du kan interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app initierar du en [SyncContext] till ett lokalt Arkiv. Referera sedan till tabellen via [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) -gränssnittet. SQLite används som lokal lagring på enheten.

1. I Visual Studio öppnar du NuGet Package Manager i projektet som du har slutfört i själv studie kursen [skapa en Xamarin Android-app] .  Sök efter och installera **Microsoft. Azure. Mobile. client. SQLiteStore** NuGet-paketet.
2. Öppna ToDoActivity.cs-filen och ta bort kommentaren till `#define OFFLINE_SYNC_ENABLED`-definitionen.
3. I Visual Studio trycker du på **F5** för att återskapa och köra klient programmet. Appen fungerar på samma sätt som innan du aktiverade offlinesynkronisering. Men den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen för att koppla från Server delen

I det här avsnittet bryter du anslutningen till Server delen för mobilappar för att simulera en offline-situation. När du lägger till data objekt visar undantags hanteraren att appen är i offline-läge. I det här läget kommer nya objekt som lagts till i det lokala arkivet att synkroniseras till Server delen för mobilappar när en push körs i ett anslutet tillstånd.

1. Redigera ToDoActivity.cs i det delade projektet. Ändra **applicationURL** så att den pekar på en ogiltig URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan också visa offline-beteende genom att inaktivera trådlösa nätverk på enheten eller i flyg Plans läge.
2. Tryck på **F5** för att skapa och köra appen. Observera att synkroniseringen misslyckades vid uppdatering när appen startades.
3. Ange nya objekt och Lägg märke till att push-meddelandet Miss lyckas med statusen [CancelledByNetworkError] varje gången du klickar på **Spara**. De nya att göra-objekt finns dock i den lokala lagrings platsen tills de kan flyttas till Server delen för mobilappar.  Om du under en produktions-app utelämnar de här undantagen fungerar klient programmet som om det fortfarande är anslutet till den mobila appens Server del.
4. Stäng appen och starta om den för att kontrol lera att de nya objekt som du har skapat är sparade i det lokala arkivet.
5. Valfritt Öppna **Server Explorer**i Visual Studio. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL Database-tabellen och dess innehåll. Kontrol lera att data i backend-databasen inte har ändrats.
6. Valfritt Använd ett REST-verktyg som Fiddler eller Postman för att fråga din mobil Server del med en GET-fråga i formatet `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta till Server delen för mobilappen

I det här avsnittet ansluter du appen till Server delen för mobilappar. Första gången du kör programmet anropar händelse hanteraren `OnCreate` `OnRefreshItemsSelected`. Med den här metoden anropas `SyncAsync` för att synkronisera ditt lokala lager med backend-databasen.

1. Öppna ToDoActivity.cs i det delade projektet och Återställ ändringen av egenskapen **applicationURL** .
2. Tryck på **F5** för att återskapa och köra appen. Appen synkroniserar dina lokala ändringar med Server delen för Azure Mobile-appen med push-och pull-åtgärder när metoden `OnRefreshItemsSelected` körs.
3. Valfritt Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan server dels databasen för Azure Mobile app och det lokala arkivet.
4. I appen klickar du på kryss rutan bredvid några objekt för att slutföra dem i det lokala arkivet.

   `CheckItem`-anrop `SyncAsync` för att synkronisera varje slutfört objekt med Server delen för mobilappar. `SyncAsync` anropar både push och pull. **När du kör en pull-överföring mot en tabell som klienten har gjort ändringar i, körs alltid en push-överföring automatiskt**. Detta säkerställer att alla tabeller i den lokala butiken tillsammans med relationer förblir konsekventa. Detta kan leda till att en oväntad push-överföring sker. Mer information om det här problemet finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Granska koden för klientens synkronisering

Det Xamarin-klientcertifikat som du laddade ned när du slutförde självstudien [skapa en Xamarin Android-app] innehåller redan kod som stöder offline-synkronisering med en lokal SQLite-databas. Här är en kort översikt över vad som redan finns med i själv studie kursen. En översikt över funktionen finns i [Datasynkronisering offline i Azure Mobile Apps].

* Innan alla tabell åtgärder kan utföras måste det lokala arkivet initieras. Den lokala lagrings databasen initieras när `ToDoActivity.OnCreate()` kör `ToDoActivity.InitLocalStoreAsync()`. Den här metoden skapar en lokal SQLite-databas med hjälp av klassen `MobileServiceSQLiteStore` som tillhandahålls av Azure Mobile Apps client SDK.

    Metoden `DefineTable` skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen, `ToDoItem` i det här fallet. Typen behöver inte innehålla alla kolumner som finns i fjärrdatabasen. Det går att lagra bara en delmängd av kolumnerna.

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
* @No__t-0-medlemmen i `ToDoActivity` är av `IMobileServiceSyncTable`-typ i stället för `IMobileServiceTable`. IMobileServiceSyncTable dirigerar alla tabell åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) till den lokala lagrings databasen.

    Du bestämmer när ändringar ska skickas till Server delen för Azure Mobile-appen genom att anropa `IMobileServiceSyncContext.PushAsync()`. Den synkroniserade kontexten hjälper till att bevara tabell relationer genom att spåra och överföra ändringar i alla tabeller som en klient app har ändrat när `PushAsync` anropas.

    Det angivna kod anropet `ToDoActivity.SyncAsync()` synkroniseras när todoitem-listan uppdateras eller en todoitem läggs till eller slutförs. Koden synkroniseras efter varje lokal ändring.

    I den angivna koden frågas alla poster i den fjärranslutna `TodoItem`-tabellen, men det går också att filtrera poster genom att skicka ett fråge-ID och fråga till `PushAsync`. Mer information finns i avsnittet *stegvis synkronisering* i [Datasynkronisering offline i Azure Mobile Apps].

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
* [Azure Mobile Apps .NET SDK-HOWTO][8]

<!-- URLs. -->
[Skapa en Xamarin Android-app]: ./app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Skapa en Xamarin Android-app]: app-service-mobile-xamarin-android-get-started.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

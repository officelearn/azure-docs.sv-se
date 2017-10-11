---
title: Aktivera offlinesynkronisering av appen universella Windowsplattformen (UWP) med Mobilappar | Microsoft Docs
description: "Lär dig hur du använder en Azure Mobile App till cache och synkronisering offline data i appen universella Windowsplattformen (UWP)."
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 4b0a57c3bab688487eb9a50461b406e1a6e477c6
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivera offlinesynkronisering av Windows-appen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen visar hur du lägger till offline-stöd för en universella Windowsplattformen (UWP)-app med en mobilappsserverdel i Azure. Offlinesynkronisering kan slutanvändarna interagera med en mobil app – visa, lägga till eller ändra data – även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen, har ändringarna synkroniserats med fjärr-serverdelen.

I kursen får du uppdatera UWP-appsprojektet från kursen [skapa en Windows-app] att stödja Azure Mobile Apps offline funktioner. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tilläggspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offlinesynkronisering finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="requirements"></a>Krav
Den här kursen kräver följande förutsättningar:

* Visual Studio 2013 körs på Windows 8.1 eller senare.
* Slutförande av [skapa en Windows-app][Skapa en windows-app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite för Uwp-utveckling](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner
Azure Mobile App offline funktionerna kan du samverka med en lokal databas när du är i ett offline-scenario. För att använda funktionerna i din app måste du initiera en [SyncContext] [ synccontext] till ett lokalt Arkiv. Referenstabell via den [IMobileServiceSyncTable][IMobileServiceSyncTable] gränssnitt. SQLite används som det lokala arkivet på enheten.

1. Installera den [SQLite runtime för Uwp](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. Öppna i Visual Studio NuGet package manager för UWP-appsprojektet som du har slutfört i den [skapa en Windows-app] kursen.
    Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
3. I Solution Explorer högerklickar du på **referenser** > **Lägg till referens...** >**Universell Windows** > **tillägg**, aktivera båda **SQLite för Uwp** och **Visual C++ 2015 Runtime för Universal Windows Platform appar**.

    ![Lägg till SQLite UWP-referens][1]
4. Öppna filen MainPage.xaml.cs och Avkommentera den `#define OFFLINE_SYNC_ENABLED` definition.
5. I Visual Studio trycker du på den **F5** för att omkompilera och köra klientapp-. Appen fungerar på samma sätt som innan du har aktiverat offlinesynkronisering. Den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen
I det här avsnittet bryta anslutningen till din mobilappsserverdel för att simulera en offline-situation. När du lägger till dataobjekt din undantagshanterare talar om att appen är i offline-läge. I det här tillståndet nya objekt lades till i det lokala arkivet och kommer att synkroniseras till serverdelen för mobilappen när push kör nästa gång i anslutet tillstånd.

1. Redigera App.xaml.cs i delat projekt. Kommentera initieringen av den **MobileServiceClient** och Lägg till följande rad som använder en ogiltig mobila app-URL:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller använda Flygplansläge.
2. Tryck på **F5** att skapa och köra appen. Observera synkroniseringen misslyckades på Uppdatera när appen startas.
3. Ange nya objekt och Lägg märke till att push misslyckas med en [CancelledByNetworkError] varje gång du klickar på **spara**. Dock finns nya todo-objekt i det lokala arkivet tills de flyttas till serverdelen för mobilappen.  I ett produktionsprogram om du förhindra sådana undantag fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stänga appen och starta om den för att kontrollera att de nya objekt som du skapade sparas på det lokala arkivet.
5. (Valfritt) Öppna i Visual Studio **Server Explorer**. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL-databastabell och dess innehåll. Kontrollera att data i databasen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg, till exempel Fiddler eller Postman fråga din mobila serverdel med en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera program för att återansluta din mobilappsserverdel
I det här avsnittet kan du ansluta appen till serverdelen för mobilappen. De här ändringarna simulera nätverket återanslutning i appen.

När du först kör programmet, den `OnNavigatedTo` händelsehanteraranrop `InitLocalStoreAsync`. Den här metoden i sin tur anropar `SyncAsync` att synkronisera dina lokala arkivet med backend-databasen. Appen försöker synkronisera vid start.

1. Öppna App.xaml.cs i det delade projektet och Avkommentera din tidigare initieringen av `MobileServiceClient` att använda rätt mobila app-URL.
2. Tryck på den **F5** för att omkompilera och kör appen. Appen synkroniserar dina lokala ändringar med Azure mobilappsserverdel med push och pull-åtgärder när den `OnNavigatedTo` händelsehanterare körs.
3. (Valfritt) Visa den uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Meddelande data har synkroniserats mellan Azure Mobile App backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `UpdateCheckedTodoItem`anrop `SyncAsync` till varje slutförd synkroniseringsobjekt med serverdelen för Mobilappen. `SyncAsync`anropar både sändning och mottagning. Dock **när du kör en pull mot en tabell som klienten har gjort ändringar i en push alltid körs automatiskt**. Det här beteendet garanteras att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Detta kan resultera i ett oväntat push.  Mer information om det här problemet finns i [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="api-summary"></a>API-översikten
Om du vill stödja offline funktioner i mobila tjänster vi använde den [IMobileServiceSyncTable] gränssnitt och initieras [MobileServiceClient.SyncContext] [ synccontext] med en lokal SQLite-databas. När du är offline, arbeta normal CRUD-åtgärder för Mobile Apps som om appen fortfarande är ansluten medan åtgärder mot det lokala arkivet. Följande metoder används för att synkronisera det lokala arkivet med servern:

* **[PushAsync]**  eftersom den här metoden är en medlem av [IMobileServicesSyncContext], ändringar i alla tabeller pushas till serverdelen. Endast poster med lokala ändringar skickas till servern.
* **[PullAsync]**  en pull startas från en [IMobileServiceSyncTable]. När det finns ändringar i tabellen, kör en implicit push för att se till att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Den *pushOtherTables* parameter styr huruvida andra tabeller i kontexten push i en implicit push. Den *frågan* parametern tar ett [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] OData frågesträng att filtrera returnerade data. Den *fråge-ID* används för att definiera inkrementell synkronisering. Mer information finns i [Offline datasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  appen bör regelbundet anropa den här metoden om du vill ta bort inaktuella data från det lokala arkivet. Använd den *tvinga* parameter när du vill rensa alla ändringar som inte har synkroniserats.

Mer information om de här koncepten finns [Offline datasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mer information
Följande avsnitt finns bakgrundsinformation om funktionen offlinesynkronisering i Mobile Apps:

* [offlinesynkronisering Data i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK ta][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[offlinesynkronisering Data i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Skapa en windows-app]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

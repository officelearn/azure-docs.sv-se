---
title: Aktivera offlinesynkronisering för din UWP-app
description: Lär dig hur du använder en Azure Mobile-app för att cachelagra och synkronisera offlinedata i din UWP-app (Universal Windows Platform).
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458825"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivera offlinesynkronisering av Windows-appen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien visar hur du lägger till offlinesupport i en UWP-app (Universal Windows Platform) med hjälp av en Azure Mobile App-backend. Offlinesynkronisering gör det möjligt för slutanvändare att interagera med en mobilapp – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringar lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrserveringen.

I den här självstudien uppdaterar du UWP-appprojektet från självstudien [Skapa en Windows-app] för att stödja offlinefunktionerna i Azure Mobile Apps. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till paketen för dataåtkomsttillägg i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om synkroniseringsfunktionen offline finns i avsnittet [Offlinedatasynkning i Azure Mobile Apps].

## <a name="requirements"></a>Krav  
Den här självstudien kräver följande förutsättningar:

* Visual Studio 2013 körs på Windows 8.1 eller senare.
* Slutförs [av Skapa en Windows-app]skapa en[Windows-app].
* [SQLite-butik för Azure Mobile Services][sqlite store nuget]
* [SQLite för universell windowsplattformsutveckling](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen så att den stöder offlinefunktioner
Med offlinefunktionerna i Azure Mobile App kan du interagera med en lokal databas när du befinner dig i ett offlinescenario. Om du vill använda dessa funktioner i appen initierar du ett [SyncContext-gränssnitt.][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] SQLite används som lokal butik på enheten.

1. Installera [SQLite-körningen för Universal Windows-plattformen](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. Öppna NuGet-pakethanteraren för det UWP-appprojekt som du slutförde i självstudien [Skapa en Windows-app] i Visual Studio.
    Sök efter och installera **paketet Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
3. Högerklicka på Referenser i **Lösningsutforskaren...** > **Add Reference...** > **Universella Windows-tillägg** > **Extensions**och aktivera sedan både **SQLite för universell Windows-plattform** och **Visual C++ 2015 Runtime for Universal Windows Platform-appar**.

    ![Lägg till SQLite UWP-referens][1]
4. Öppna MainPage.xaml.cs filen och ta av `#define OFFLINE_SYNC_ENABLED` dig den.
5. I Visual Studio trycker du på **F5** för att återskapa och köra klientappen. Appen fungerar på samma sätt som den gjorde innan du aktiverade offlinesynkronisering. Den lokala databasen fylls dock nu med data som kan användas i ett offlinescenario.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Uppdatera appen för att koppla från backend
I det här avsnittet bryter du anslutningen till din mobile app-backend för att simulera en offlinesituation. När du lägger till dataobjekt talar undantagshanteraren om att appen är i offlineläge. I det här läget har nya objekt som lagts till i den lokala butiken och synkroniseras till mobilappens backend när push körs nästa gång i ett anslutet tillstånd.

1. Redigera App.xaml.cs i det delade projektet. Kommentera initieringen av **MobileServiceClient** och lägg till följande rad, som använder en ogiltig webbadress till mobilappen:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Du kan också visa offlinebeteende genom att inaktivera wifi och mobilnät på enheten eller använda flygplansläge.
2. Tryck på **F5** för att skapa och köra appen. Observera att synkroniseringen misslyckades vid uppdatering när appen startades.
3. Ange nya objekt och märker att push misslyckas med en [CancelledByNetworkError-status] varje gång du klickar på **Spara**. De nya todo-objekten finns dock i den lokala butiken tills de kan skickas till mobilappens backend.  Om du undertrycker dessa undantag i en produktionsapp fungerar klientappen som om den fortfarande är ansluten till mobilappens backend.
4. Stäng appen och starta om den för att kontrollera att de nya objekten som du har skapat sparas i den lokala arkivet.
5. (Valfritt) Öppna **Server Explorer**i Visual Studio . Navigera till databasen i **Azure**->**SQL Databases**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll. Kontrollera att data i backend-databasen inte har ändrats.
6. (Valfritt) Använd ett REST-verktyg som Fiddler eller Postman för att fråga din `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`mobila backend med hjälp av en GET-fråga i formuläret .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Uppdatera appen för att återansluta din mobile app-backend
I det här avsnittet återansluter du appen till mobilappens backend. Dessa ändringar simulerar en nätverksanslutning i appen.

När du kör programmet `OnNavigatedTo` första gången `InitLocalStoreAsync`anropar händelsehanteraren . Den här metoden `SyncAsync` anropar i sin tur för att synkronisera din lokala butik med backend-databasen. Appen försöker synkronisera vid start.

1. Öppna App.xaml.cs i det delade projektet och ta bort `MobileServiceClient` din tidigare initiering av att använda rätt webbadress för mobilappen.
2. Tryck på **F5** för att bygga om och köra appen. Appen synkroniserar dina lokala ändringar med Azure Mobile App-backend `OnNavigatedTo` med hjälp av push- och pull-åtgärder när händelsehanteraren körs.
3. (Valfritt) Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan Azure Mobile App-serverdelsdatabasen och det lokala arkivet.
4. I appen klickar du på kryssrutan bredvid några objekt för att slutföra dem i den lokala butiken.

   `UpdateCheckedTodoItem`för `SyncAsync` att synkronisera varje slutförd objekt med mobile-appens backend. `SyncAsync`samtal både tryck och dra. Men **när du kör en pull mot en tabell som klienten har gjort ändringar i, körs en push alltid automatiskt**. Detta säkerställer att alla tabeller i den lokala butiken tillsammans med relationer förblir konsekventa. Detta kan resultera i en oväntad push.  Mer information om det här beteendet finns [i Offlinedatasynkronisering i Azure Mobile Apps].

## <a name="api-summary"></a>API-sammanfattning
För att stödja offlinefunktionerna i mobila tjänster använde vi [gränssnittet IMobileServiceSyncTable] och initierade [MobileServiceClient.SyncContext][synccontext] med en lokal SQLite-databas. När du är offline fungerar de normala CRUD-åtgärderna för mobilappar som om appen fortfarande är ansluten medan åtgärderna sker mot den lokala butiken. Följande metoder används för att synkronisera det lokala arkivet med servern:

* **[PushAsync (PushAsync)]** Eftersom den här metoden är medlem i [IMobileServicesSyncContext]skjuts ändringar över alla tabeller till backend. Endast poster med lokala ändringar skickas till servern.
* **[PullAsync (PullAsync)]** Ett drag startas från en [IMobileServiceSyncTable]. När det finns spårade ändringar i tabellen körs en implicit push för att se till att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Parametern *pushOtherTables* styr om andra tabeller i sammanhanget skjuts i en implicit push. *Frågeparametern* tar en [IMobileServiceTableQuery\<T>-][IMobileServiceTableQuery] eller OData-frågesträng för att filtrera returnerade data. *Parametern queryId* används för att definiera inkrementell synkronisering. Mer information finns [i Offlinedatasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Appen bör regelbundet anropa den här metoden för att rensa inaktuella data från det lokala arkivet. Använd *kraftparametern* när du behöver rensa alla ändringar som ännu inte har synkroniserats.

Mer information om dessa begrepp finns [i Offlinedatasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mer information
Följande avsnitt innehåller ytterligare bakgrundsinformation om synkroniseringsfunktionen offline i Mobilappar:

* [Datasynkronisering offline i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK HOWTO][8]

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
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[skapa en Windows-app]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[AvbrytsAvNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync (PullAsync)]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync (PushAsync)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

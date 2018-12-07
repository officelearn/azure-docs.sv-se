---
title: Aktivera offlinesynkronisering av appen Universal Windows Platform (UWP) med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder en Azure Mobile App till cache och synkronisering av offlinedata i din app för Universal Windows Platform (UWP).
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994932"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivera offlinesynkronisering av Windows-appen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien visar hur du lägger till offlinestöd till en Universal Windows Platform (UWP) med hjälp av en mobilappsserverdel i Azure. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app – visa, lägga till eller ändra data, även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med fjärr-serverdel.

I den här självstudien får du uppdatera UWP-appsprojektet från självstudierna [skapa en Windows-app] för Offlinefunktioner för Azure Mobile Apps. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tillägg-paket i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering, finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="requirements"></a>Krav  
Den här självstudien kräver följande förutsättningar:

* Visual Studio 2013 som körs på Windows 8.1 eller senare.
* Slutförandet av [skapa en Windows-app][skapa en windows-app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite för Universal Windows Platform-utveckling](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klientappen för att stödja Offlinefunktioner
Offlinefunktioner för Azure Mobile-appen kan du interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app måste du initiera en [SyncContext] [ synccontext] till ett lokalt Arkiv. Referenstabell via den [IMobileServiceSyncTable][IMobileServiceSyncTable] gränssnitt. SQLite används som det lokala arkivet på enheten.

1. Installera den [SQLite runtime för Universal Windows Platform](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. I Visual Studio, öppnar du NuGet package manager för UWP-appsprojektet har slutförts i den [skapa en Windows-app] självstudien.
    Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet.
3. I Solution Explorer högerklickar du på **referenser** > **Lägg till referens...** > **Universal Windows** > **tillägg**, aktivera båda **SQLite för Universal Windows Platform** och **Visual C++ 2015 Runtime för Universal Windows Plattformsappar**.

    ![Lägg till SQLite UWP-referens][1]
4. Öppna filen MainPage.xaml.cs och ta bort kommentarerna i `#define OFFLINE_SYNC_ENABLED` definition.
5. I Visual Studio trycker du på den **F5** att omkompilera och kör klientappen. Appen fungerar på samma sätt som innan du har aktiverat synkronisering offline. Den lokala databasen fylls men nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen att koppla från serverdelen
I det här avsnittet ska bryta du anslutningen till din mobilappsserverdel att simulera en offline situation. När du lägger till dataobjekt anger din undantagshanterare att appen är i offline-läge. I det här tillståndet nya objekt har lagts till i det lokala arkivet och kommer att synkroniseras till serverdelen för mobilappen när push nästa körning i anslutet tillstånd.

1. Redigera App.xaml.cs i delade projekt. Kommentera ut initieringen av den **MobileServiceClient** och Lägg till följande rad, som använder en ogiltig mobila app-URL:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Du kan också visa offline beteende genom att inaktivera Wi-Fi och mobila nätverk på enheten eller använda Flygplansläge.
2. Tryck på **F5** att bygga och köra appen. Lägg märke till din synkroniseringen misslyckades vid uppdatering när appen startas.
3. Ange nya objekt och Observera att push misslyckas med ett [CancelledByNetworkError] varje gång du klickar på **spara**. Men finns de nya objekt i det lokala arkivet tills de kan skickas till serverdelen för mobilappen.  I en produktionsapp är om du ignorera de här undantagen fungerar klientappen som om den fortfarande är ansluten till serverdelen för mobilappen.
4. Stäng appen och starta om den för att kontrollera att de nya objekt som du har skapat sparas till det lokala arkivet.
5. (Valfritt) Öppna i Visual Studio **Server Explorer**. Gå till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll. Kontrollera att data i databasen i serverdelen inte har ändrats.
6. (Valfritt) Använda ett REST-verktyg som Fiddler eller Postman för att fråga din mobila serverdelstjänst, med hjälp av en GET-fråga i formuläret `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta din mobilappsserverdel
I det här avsnittet kan du återansluta app på serverdelen för mobilappen. De här ändringarna simulera en nätverk återanslutning på appen.

Första gången du kör programmet och den `OnNavigatedTo` händelse hanteraren anrop `InitLocalStoreAsync`. Den här metoden i sin tur anropar `SyncAsync` att synkronisera din lokala arkivet med backend-databas. Appen försöker synkronisera vid start.

1. Öppna App.xaml.cs i delat projekt och ta bort kommentarerna för ditt tidigare initieringen av `MobileServiceClient` du använder rätt Webbadress till mobilapp.
2. Tryck på den **F5** att bygga och köra appen. Appen synkroniserar dina lokala ändringar med Azure Mobile App-serverdel push och pull-åtgärder när den `OnNavigatedTo` händelsehanterare körs.
3. (Valfritt) Visa uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att dessa data har synkroniserats mellan Azure-Mobilapp backend-databas och det lokala arkivet.
4. I appen, klickar du på kryssrutan bredvid några objekt att slutföra dem i det lokala arkivet.

   `UpdateCheckedTodoItem` anrop `SyncAsync` att synkroniseringen varje har slutförts objekt med serverdelen för Mobilappen. `SyncAsync` anropar både sändning och mottagning. Dock **när du kör en hämtning mot en tabell som klienten har gjort ändringar i en push alltid körs automatiskt**. Det här beteendet garanteras att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Det här beteendet kan resultera i ett oväntat push.  Mer information om detta finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="api-summary"></a>API-sammanfattning
För att stödja Offlinefunktioner i mobila tjänster, som vi använde den [IMobileServiceSyncTable] gränssnitt och initiera [MobileServiceClient.SyncContext] [ synccontext] med en lokala SQLite-databas. När du är offline, fungerar de normala CRUD-åtgärderna för Mobile Apps som om appen är fortfarande ansluten när åtgärderna som inträffar mot det lokala arkivet. Följande metoder används för att synkronisera det lokala arkivet med servern:

* **[PushAsync]**  eftersom den här metoden är medlem i [IMobileServicesSyncContext], ändringar över alla tabeller som skickas till serverdelen. Endast poster med lokala ändringar skickas till servern.
* **[PullAsync]**  en hämtning har startats från en [IMobileServiceSyncTable]. När det finns spårade ändringar i tabellen, körs en implicit push för att se till att alla tabeller i det lokala arkivet tillsammans med relationer förblir konsekventa. Den *pushOtherTables* parameter styr huruvida andra tabeller i kontexten skickas i en implicit push. Den *fråga* parametern tar ett [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] eller OData-frågesträngen för att filtrera data som returneras. Den *queryId* används för att definiera inkrementell synkronisering. Mer information finns i [Offline datasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  bör din app med jämna mellanrum att anropa den här metoden om du vill rensa inaktuella data från det lokala arkivet. Använd den *tvinga* parameter när du vill rensa alla ändringar som inte har synkroniserats.

Mer information om de här koncepten finns i [Offline datasynkronisering i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mer information
Följande avsnitt finns mer bakgrundsinformation om funktionen offline-synkronisering i Mobile Apps:

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
[Skapa en windows-app]: app-service-mobile-windows-store-dotnet-get-started.md
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
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

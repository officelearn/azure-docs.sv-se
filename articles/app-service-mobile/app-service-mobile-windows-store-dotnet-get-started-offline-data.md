---
title: Aktivera offline-synkronisering för din Universell Windows-plattform-app (UWP) med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder en Azure-mobilapp för att cachelagra och Synkronisera offlinedata i din Universell Windows-plattform-app (UWP).
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 2ffd80f60543c3136eff2d5774a850347eb68427
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027090"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivera offlinesynkronisering av Windows-appen
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Den här självstudien visar hur du lägger till offline-stöd till en Universell Windows-plattform-app (UWP) med hjälp av en Azure-mobilapp. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp – Visa, lägga till eller ändra data, även om det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas. När enheten är online igen synkroniseras de här ändringarna med fjärrservern.

I den här självstudien uppdaterar du UWP-Appaketet från självstudien [skapa en Windows-app] som stöder offline-funktionerna i Azure Mobile Apps. Om du inte använder det hämtade projektet för snabb starts Server måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="requirements"></a>Krav  
I den här självstudien krävs följande för hands krav:

* Visual Studio 2013 körs på Windows 8,1 eller senare.
* Slut för ande av [skapa en Windows-app][skapa en Windows-app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite för Universell Windows-plattform utveckling](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Uppdatera klient appen så att den stöder offline-funktioner
Offline-funktioner i Azure Mobile Apps gör att du kan interagera med en lokal databas när du är i ett offline-scenario. Om du vill använda dessa funktioner i din app initierar du ett [SyncContext][synccontext] -gränssnitt för to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite används som lokal lagring på enheten.

1. Installera [sqlite-körningen för universell Windows-plattform](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. I Visual Studio öppnar du NuGet Package Manager för det UWP app-projekt som du har slutfört i själv studie kursen [skapa en Windows-app] .
    Sök efter och installera **Microsoft. Azure. Mobile. client. SQLiteStore** NuGet-paketet.
3. I Solution Explorer högerklickar du på **referenser** > **Lägg till referens...** > **universella Windows** >- **tillägg**och aktivera sedan både **SQLite för universell Windows-plattform** och **Visual C++ 2015 runtime för universell Windows-plattform appar**.

    ![Lägg till SQLite UWP-referens][1]
4. Öppna MainPage.xaml.cs-filen och ta bort kommentaren till `#define OFFLINE_SYNC_ENABLED`-definitionen.
5. I Visual Studio trycker du på **F5** för att återskapa och köra klient programmet. Appen fungerar på samma sätt som innan du aktiverade offlinesynkronisering. Men den lokala databasen fylls nu med data som kan användas i ett offline-scenario.

## <a name="update-sync"></a>Uppdatera appen för att koppla från Server delen
I det här avsnittet bryter du anslutningen till Server delen för mobilappar för att simulera en offline-situation. När du lägger till data objekt visar undantags hanteraren att appen är i offline-läge. I det här läget kommer nya objekt som lagts till i det lokala arkivet att synkroniseras till Server delen för mobilappar när push körs nästa gång den körs i ett anslutet tillstånd.

1. Redigera App.xaml.cs i det delade projektet. Kommentera ut initieringen av **MobileServiceClient** och Lägg till följande rad, som använder en ogiltig URL för mobilappen:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Du kan också visa offline-beteende genom att inaktivera trådlösa nätverk på enheten eller Använd flyg Plans läge.
2. Tryck på **F5** för att skapa och köra appen. Observera att synkroniseringen misslyckades vid uppdatering när appen startades.
3. Ange nya objekt och Lägg märke till att push-meddelandet Miss lyckas med [CancelledByNetworkError] status varje gången du klickar på **Spara**. De nya att göra-objekt finns dock i den lokala lagrings platsen tills de kan flyttas till Server delen för mobilappar.  Om du under en produktions-app utelämnar de här undantagen fungerar klient programmet som om det fortfarande är anslutet till den mobila appens Server del.
4. Stäng appen och starta om den för att kontrol lera att de nya objekt som du har skapat är sparade i det lokala arkivet.
5. Valfritt Öppna **Server Explorer**i Visual Studio. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL Database-tabellen och dess innehåll. Kontrol lera att data i backend-databasen inte har ändrats.
6. Valfritt Använd ett REST-verktyg som Fiddler eller Postman för att fråga din mobil Server del med en GET-fråga i formatet `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Uppdatera appen för att återansluta till Server delen för mobilappen
I det här avsnittet ansluter du appen till Server delen för mobilappar. Dessa ändringar simulerar en nätverks åter anslutning på appen.

Första gången du kör programmet anropar händelse hanteraren `OnNavigatedTo` `InitLocalStoreAsync`. Den här metoden i tur anrop `SyncAsync` för att synkronisera ditt lokala lager med backend-databasen. Appen försöker synkronisera vid start.

1. Öppna App.xaml.cs i det delade projektet och ta bort kommentaren till den tidigare initieringen av `MobileServiceClient` om du vill använda rätt URL för mobilapp.
2. Tryck på **F5** för att återskapa och köra appen. Appen synkroniserar dina lokala ändringar med Server delen för Azure Mobile-appen med push-och pull-åtgärder när `OnNavigatedTo`-händelse hanteraren körs.
3. Valfritt Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler. Observera att data har synkroniserats mellan server dels databasen för Azure Mobile app och det lokala arkivet.
4. I appen klickar du på kryss rutan bredvid några objekt för att slutföra dem i det lokala arkivet.

   `UpdateCheckedTodoItem`-anrop `SyncAsync` för att synkronisera varje slutfört objekt med Server delen för mobilappar. `SyncAsync` anropar både push och pull. Men **när du kör en pull-överföring mot en tabell som klienten har gjort ändringar i, körs alltid en push-överföring automatiskt**. Det här beteendet säkerställer att alla tabeller i den lokala butiken tillsammans med relationer förblir konsekventa. Detta kan leda till att en oväntad push-överföring sker.  Mer information om det här problemet finns i [Datasynkronisering offline i Azure Mobile Apps].

## <a name="api-summary"></a>API-Sammanfattning
För att stödja offline-funktionerna i mobila tjänster använde vi [IMobileServiceSyncTable] -gränssnittet och initierade [MobileServiceClient. SyncContext][synccontext] med en lokal SQLite-databas. När du är offline är de normala CRUD åtgärder för Mobile Apps fungera som om appen fortfarande är ansluten medan åtgärderna sker mot det lokala arkivet. Följande metoder används för att synkronisera det lokala arkivet med-servern:

* **[PushAsync]** Eftersom den här metoden är medlem i [IMobileServicesSyncContext]flyttas ändringar i alla tabeller till Server delen. Endast poster med lokala ändringar skickas till servern.
* **[PullAsync]** En pull startas från en [IMobileServiceSyncTable]. När det finns spårade ändringar i tabellen körs en implicit push för att se till att alla tabeller i den lokala butiken tillsammans med relationer förblir konsekventa. Parametern *pushOtherTables* styr huruvida andra tabeller i kontexten pushas i en implicit push. *Frågeparametern* tar en [IMobileServiceTableQuery @ no__t-2T >][IMobileServiceTableQuery] eller OData-frågesträng för att filtrera returnerade data. Parametern *fråge* används för att definiera stegvis synkronisering. Mer information finns i [data synkronisering offline i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Din app bör regelbundet anropa den här metoden för att rensa inaktuella data från det lokala arkivet. Använd parametern *Force* när du behöver rensa eventuella ändringar som ännu inte har synkroniserats.

Mer information om dessa begrepp finns i [data synkronisering offline i Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mer information
I följande avsnitt finns ytterligare bakgrunds information om funktionen offline-synkronisering i Mobile Apps:

* [Datasynkronisering offline i Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK-HOWTO][8]

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
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

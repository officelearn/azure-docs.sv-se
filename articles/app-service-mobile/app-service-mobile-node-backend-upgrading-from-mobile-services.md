---
title: Uppgradera från mobila tjänster till Azure App Service – Node.js
description: Lär dig hur du lätt att uppgradera ditt program för mobiltjänster till en App Service Mobile App
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: e54ed6c526182cea57e2d40f356ad9236510d82c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128074"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Uppgradera din befintliga Node.js Azure-Mobiltjänst till App Service
App Service Mobile är ett nytt sätt att bygga mobilappar med Microsoft Azure. Mer information finns i [vad är Mobile Apps?].

Den här artikeln beskriver hur du uppgraderar ett befintligt Node.js-serverdel program från Azure Mobile Services till en ny App Service Mobile Apps. Medan du utför den här uppgraderingen kan ditt befintliga program för mobiltjänster fortsätta att fungera.  Om du vill uppgradera ett Node.js-program för serverdelen kan se [uppgradera din .NET-mobiltjänster](app-service-mobile-net-upgrading-from-mobile-services.md).

När en mobil serverdel uppgraderas till Azure App Service, den har åtkomst till alla App Service-funktioner och debiteras enligt [prissättning för App Service], inte Mobile Services priser.

## <a name="migrate-vs-upgrade"></a>Migrera jämfört med uppgraderingen
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Vi rekommenderar att du [utför en migrering](app-service-mobile-migrating-from-mobile-services.md) innan du går igenom en uppgradering. På så sätt kan du placera båda versioner av ditt program på samma App Service-planen och medföra utan extra kostnad.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Förbättringar i Mobile Apps Node.js server-SDK
Uppgradera till den nya [SDK för Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) innehåller många förbättringar, inklusive:

* Utifrån den [Express framework](https://expressjs.com/en/index.html), den nya noden SDK är lätt och utvecklat för att hålla jämna steg med den nya noden versioner när de kommer. Du kan anpassa funktionssättet för programmet med Express-mellanprogrammet.
* Betydande prestandaförbättringar jämfört med Mobile Services SDK.
* Du kan nu vara värd för en webbplats tillsammans med din mobila serverdelstjänst; på samma sätt är det enkelt att lägga till Azure Mobile SDK för befintliga express.v4 program.
* Bygger för flera plattformar och lokala utveckling och kan SDK för Mobile Apps utvecklas och köras lokalt på Windows, Linux och OS x-plattformar. Nu är det enkelt att använda vanliga noden utvecklingstekniker som körs [Mocha](https://mochajs.org/) tester före distributionen.

## <a name="overview"></a>Grundläggande översikt över Programuppgradering
För att hjälpa uppgraderar en Node.js-serverdel, tillhandahåller Azure App Service ett paket för kompatibilitet.  Efter uppgraderingen kan får du en ny plats som kan distribueras till en ny App Service-plats.

Mobile Services-klient SDK: erna är **inte** kompatibel med den nya Mobile Apps-servern SDK. För att tillhandahålla replikeringsmodellen för din app, bör du inte publicera ändringar till en plats som betjänar publicerade klienter. I stället bör du skapa en ny mobilapp som fungerar som en dubblett. Du kan publicera det här programmet på samma App Service-planen för att undvika extra kostnader.

Du kommer då ha två versioner av programmet: en som förblir oförändrat och hanterar appar som har publicerats i naturen, och den andra som du kan sedan uppgradera och målet med en ny klientversion. Du kan flytta och testa din kod i din takt, men du bör se till att alla felkorrigeringar som du gör gäller för båda. När du anser att ett önskat antal klient apps i naturen har uppdaterat till den senaste versionen, du kan ta bort den ursprungliga migrerade appen om du vill ha. Det innebära inte ytterligare monetära kostnader, i samma App Service-planen som din Mobilapp.

Fullständig kantlinje för uppgraderingen är följande:

1. Hämta din befintliga (migrerade) Azure-mobiltjänst.
2. Konvertera projektet till en Azure-Mobilapp med hjälp av paketet för kompatibilitet.
3. Korrigera eventuella skillnader (till exempel autentiseringsinställningar).
4. Distribuera projektet konverterade Azure Mobile App till en ny App Service.
5. Använda en ny version av ditt klientprogram som använder den nya Mobile-appen.
6. (Valfritt) Ta bort den ursprungliga migrerade Mobiltjänst-appen.

Borttagning kan inträffa när du inte ser någon trafik på din ursprungliga migrerade mobiltjänst.

## <a name="install-npm-package"></a> Installera krav
Du bör installera [Node] på den lokala datorn.  Du bör även installera paketet kompatibilitet.  När noden har installerats kan köra du följande kommando från en ny cmd eller PowerShell-Kommandotolken:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Hämta dina Azure Mobile Services-skript
* Logga in på [Azure Portal].
* Med hjälp av **alla resurser** eller **Apptjänster**, hitta Mobile Services-plats.
* Inom platsen klickar du på **verktyg** -> **Kudu** -> **Gå** att öppna Kudu-webbplatsen.
* Klicka på **Felsökningskonsolen** -> **PowerShell** att öppna Felsökningskonsolen.
* Gå till `site/wwwroot/App_Data/config` genom att klicka på varje katalog i sin tur
* Klicka på ikonen hämta bredvid den `scripts` directory.

Detta hämtar skripten i ZIP-format.  Skapa en ny katalog på den lokala datorn och packa upp den `scripts.ZIP` fil i katalogen.  Detta skapar en `scripts` directory.

## <a name="scaffold-app"></a> Autogenerera den nya Azure Mobile Apps-serverdelen
Kör följande kommando från den katalog som innehåller skriptkatalogen:

```scaffold-mobile-app scripts out```

Detta skapar en autogenererade Azure Mobile Apps-serverdel i den `out` directory.  Även om det inte krävs, är det en bra idé att kontrollera den `out` katalogen till ett lagringsplatsen för källkod valfri.

## <a name="deploy-ama-app"></a> Distribuera din Azure Mobile Apps-serverdel
Under distributionen måste du göra följande:

1. Skapa en ny Mobilapp i den [Azure Portal].
2. Kör den `createViews.sql` skript i dina anslutna databasen.
3. Länka den databas som är länkad till din Mobiltjänst till din nya App Service.
4. Länka andra resurser (till exempel Meddelandehubbar) till den nya App Service.
5. Distribuera den genererade koden till den nya platsen.

### <a name="create-a-new-mobile-app"></a>Skapa en ny Mobilapp
1. Logga in på [Azure Portal].
2. Klicka på **+NY** > **Webb + Mobil** > **Mobilapp** och ange sedan ett namn för din serverdel för mobilappen.
3. För **Resursgrupp** väljer du en befintlig resursgrupp, eller skapar en ny (med samma namn som din app).

    Du kan antingen välja en annan App Service-plan eller skapa en ny. Mer information om App Services-planer och hur du skapar en ny plan i en annan prisnivå på din önskade plats finns i [Djupgående översikt över Azure App Services-planer](../app-service/overview-hosting-plans.md).
4. För **App Service-plan** är standardplanen (i [standardnivån](https://azure.microsoft.com/pricing/details/app-service/)) vald. Du kan också välja en annan plan eller [skapa en ny](../app-service/app-service-plan-manage.md#create-an-app-service-plan). App Service-planens inställningar avgör den [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som är associerade med din app.

    Efter att du har valt planen klickar du på **Skapa**. Serverdelen för mobilappen skapas.

### <a name="run-createviewssql"></a>Kör CreateViews.SQL
Autogenererade appen innehåller en fil med namnet `createViews.sql`.  Det här skriptet måste köras mot måldatabasen.  Anslutningssträngen för måldatabasen kan hämtas från mobiltjänsten migrerade från den **inställningar** sidan **anslutningssträngar**.  Namnet är `MS_TableConnectionString`.

Du kan köra det här skriptet från inom SQL Server Management Studio eller Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Länka databasen till din App Service
Länka den befintliga databasen till din App Service:

* I den [Azure Portal], öppna din App Service.
* Välj **alla inställningar** -> **dataanslutningar**.
* Klicka på **+ Lägg till**.
* I listrutan, väljer **SQL-databas**
* Under **SQL Database**, Välj den befintliga databasen och sedan klicka på **Välj**.
* Under **anslutningssträngen**, anger användarnamnet och lösenordet för databasen och sedan klicka på **OK**.
* I den **lägga till dataanslutningar** klickar du på **OK**.

Användarnamnet och lösenordet kan hittas genom att visa anslutningssträngen för måldatabasen i dina migrerade mobila tjänster.

### <a name="set-up-authentication"></a>Konfigurera autentisering
Azure Mobile Apps kan du konfigurera Azure Active Directory, Facebook, Google, Microsoft och Twitter-autentisering i tjänsten.  Anpassad autentisering måste ha utvecklats separat.  Referera till den [Autentiseringskoncept] dokumentation och [Snabbstart för autentisering] dokumentationen för mer information.  

## <a name="updating-clients"></a>Uppdatera mobila klienter
När du har en operational mobilappsserverdel kan arbeta du med en ny version av ditt klientprogram som använder den. Mobile Apps innehåller också en ny version av klienten SDK: er och liknar serveruppgradering ovan, behöver du ta bort alla referenser till SDK: er för Mobile Services innan du installerar Mobile Apps-versioner.

En av de viktigaste ändringarna mellan versionerna är att konstruktorer inte längre behöver en programnyckel.
Du nu skicka bara URL-adressen till din Mobilapp. Till exempel på .NET-klienter den `MobileServiceClient` konstruktorn är nu:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Du kan läsa om att installera de nya SDK: er och använder den nya strukturen via länkarna nedan:

* [Android-versionen 2.2 eller senare](app-service-mobile-android-how-to-use-client-library.md)
* [iOS-version 3.0.0 eller senare](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 eller senare](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova version 2.0 eller senare](app-service-mobile-cordova-how-to-use-client-library.md)

Om din tillämpning utför använder push-meddelanden kan anteckna de specifika instruktionerna för varje plattform, har eftersom det ändringar det också.

När du har den nya klientversionen som är redo, prova mot projektet uppgraderade servern. När du har validerat att det fungerar, kan du släpper en ny version av programmet till kunder. Så småningom när kunderna har haft möjlighet att få dessa uppdateringar, du kan ta bort den mobiltjänster-versionen av din app. Nu kan har du uppgraderat till en App Service Mobile App som använder den senaste Mobile Apps-servern SDK.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Vad är Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Prissättning för App Service]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Autentiseringskoncept]: ../app-service/overview-authentication-authorization.md
[Snabbstart för autentisering]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

---
title: Uppgradera från Mobile Services till Azure App Service - Node.js
description: Lär dig hur du lätt att uppgradera ditt Mobile Services-program till en Apptjänst Mobile App
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
ms.openlocfilehash: 07ebf23270addc63793278d4e0510c187289b82c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154553"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Uppgradera din befintliga Mobiltjänst för Node.js-Azure till App Service
Apptjänst Mobile är ett nytt sätt att skapa mobila program med Microsoft Azure. Läs mer i [vad är Mobilappar?].

Den här artikeln beskriver hur du uppgraderar ett befintligt Node.js-serverdel program från Azure Mobile Services till en ny Apptjänst Mobilappar. Medan du utför uppgraderingen fortsätter befintliga mobiltjänster programmet att fungera.  Om du behöver uppgradera ett Node.js-program för serverdelen finns [uppgradera din Mobile Services .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

När en mobilserverdel uppgraderas till Azure App Service, den har åtkomst till alla funktioner i Apptjänst och debiteras enligt [priser för Apptjänst], inte Mobile Services priser.

## <a name="migrate-vs-upgrade"></a>Migrera kontra uppgradering
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Vi rekommenderar att du [utför en migrering](app-service-mobile-migrating-from-mobile-services.md) innan du fortsätter med uppgraderingen. På så sätt kan du spärra båda versionerna av programmet på samma App Service-Plan och innebära utan extra kostnad.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Förbättringar i Mobile Apps Node.js server-SDK
Uppgradera till den nya [Mobile Apps-SDK](https://www.npmjs.com/package/azure-mobile-apps) innehåller många förbättringar, inklusive:

* Baserat på de [Express framework](http://expressjs.com/en/index.html), nya nod SDK är inaktiverad och utformade för att hålla med den nya noden versioner när de kommer. Du kan anpassa beteendet program med Express-mellanprogrammet.
* Betydande prestandaförbättringar jämfört med Mobile Services SDK.
* Du kan nu vara värd för en webbplats tillsammans med din mobila serverdel; på samma sätt är det enkelt att lägga till Azure Mobile SDK eventuella befintliga express.v4-programmet.
* Bygger för flera plattformar och lokala utveckling kan Mobile Apps-SDK vara utvecklade och köras lokalt på Windows, Linux och OS x-plattformar. Nu är det enkelt att använda vanliga nod utvecklingstekniker som körs [Mocha](https://mochajs.org/) tester före distributionen.

## <a name="overview"></a>Uppgradera översikt
För att underlätta vid uppgradering av en Node.js-serverdel tillhandahåller Azure App Service ett paket för kompatibilitet.  Efter uppgraderingen måste en ny plats som kan distribueras till en ny plats i Apptjänst.

Klienten för Mobile Services SDK: er **inte** kompatibel med den nya servern för Mobile Apps SDK. Du bör inte publicera ändringar till en plats som betjänar publicerade klienter för att kunna erbjuda kontinuitet i tjänsten för din app. I stället bör du skapa en ny mobil app som fungerar som en dubblett. Du kan publicera det här programmet på samma App Service-plan för att undvika ytterligare kostnader.

Du måste sedan två versioner av programmet: en som förblir densamma och fungerar publicerade appar i jokertecken och andra som du kan sedan uppgradera och mål med en ny klientversion. Du kan flytta och testa din kod i din takt, men bör du se till att alla felkorrigeringar som du gör gäller för båda. När du anser att ett önskat antal klientdatorer appar i jokertecken har uppdaterat till den senaste versionen, du kan ta bort den ursprungliga migrerade appen om du vill ha. Den innebära inte någon ytterligare monetära kostnader, om finns i samma App Service-plan som din Mobilapp.

Fullständig dispositionen för uppgraderingen är följande:

1. Hämta din befintliga (migrerade) Azure-mobiltjänst.
2. Konvertera projektet till en Azure Mobile App som använder paketet för kompatibilitet.
3. Åtgärda eventuella skillnader (t.ex inställningar för autentisering).
4. Distribuera projektet konverterade Azure Mobile App till en ny App Service.
5. Släpp en ny version av ditt klientprogram som använder den nya Mobilapp.
6. (Valfritt) Ta bort appen för migrerade mobiltjänst.

Borttagningen kan inträffa när du inte ser någon trafik på din ursprungliga migrerade mobiltjänst.

## <a name="install-npm-package"></a> Installera kraven
Du bör installera [Node] på den lokala datorn.  Du bör också installera paketet för kompatibilitet.  När noden har installerats kan köra du följande kommando från en ny cmd eller PowerShell-Kommandotolken:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Hämta Azure Mobile Services-skript
* Logga in på [Azure Portal].
* Med hjälp av **alla resurser** eller **Apptjänster**, hitta Mobile Services-plats.
* Inom platsen klickar du på **verktyg** -> **Kudu** -> **Gå** öppna Kudu-webbplatsen.
* Klicka på **Felsökningskonsolen** -> **PowerShell** att öppna Felsökningskonsolen.
* Navigera till `site/wwwroot/App_Data/config` genom att klicka på varje katalog i sin tur
* Klicka på ikonen download bredvid den `scripts` directory.

Detta hämtas skript i ZIP-format.  Skapa en ny katalog på den lokala datorn och packa upp den `scripts.ZIP` fil i katalogen.  Detta skapar en `scripts` directory.

## <a name="scaffold-app"></a> Autogenerera den nya Azure Mobile Apps-serverdelen
Kör följande kommando från katalogen som innehåller skriptkatalogen:

```scaffold-mobile-app scripts out```

Detta skapar en autogenererade Azure Mobile Apps-serverdel i den `out` directory.  Även om de inte krävs, är det en bra idé att kontrollera den `out` katalogen i en databas med källan kod du väljer.

## <a name="deploy-ama-app"></a> Distribuera Azure Mobile Apps-serverdel
Under distributionen behöver du göra följande:

1. Skapa en ny Mobile App i den [Azure Portal].
2. Kör den `createViews.sql` skript på den anslutna databasen.
3. Länka databasen som är länkad till din Mobiltjänst till din nya App Service.
4. Länka andra resurser (till exempel Meddelandehubbar) till den nya App Service.
5. Distribuera den genererade koden till den nya platsen.

### <a name="create-a-new-mobile-app"></a>Skapa en ny Mobile App
1. Logga in på [Azure Portal].
2. Klicka på **+NY** > **Webb + Mobil** > **Mobilapp** och ange sedan ett namn för din serverdel för mobilappen.
3. För **Resursgrupp** väljer du en befintlig resursgrupp, eller skapar en ny (med samma namn som din app).

    Du kan antingen välja en annan App Service-plan eller skapa en ny. Mer information om Apptjänster planer och hur du skapar en ny plan i en annan prisnivå på datanivå och på din önskade plats finns [Azure App Service-planer djupgående översikt över](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. För **App Service-plan** är standardplanen (i [standardnivån](https://azure.microsoft.com/pricing/details/app-service/)) vald. Du kan också välja en annan plan eller [skapa en ny](../app-service/app-service-plan-manage.md#create-an-app-service-plan). App Service-planens inställningar avgör den [plats, funktioner, kostnad och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som är associerade med din app.

    Efter att du har valt planen klickar du på **Skapa**. Serverdelen för mobilappen skapas.

### <a name="run-createviewssql"></a>Kör CreateViews.SQL
Autogenererade appen innehåller en fil med namnet `createViews.sql`.  Det här skriptet måste köras mot måldatabasen.  Anslutningssträngen för måldatabasen kan hämtas från mobiltjänsten migrerade från den **inställningar** sidan **anslutningssträngar**.  Det heter `MS_TableConnectionString`.

Du kan köra skriptet i SQL Server Management Studio eller Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Koppla databasen till App Service
Länka den befintliga databasen till din Apptjänst:

* I den [Azure Portal], öppna din Apptjänst.
* Välj **alla inställningar** -> **dataanslutningar**.
* Klicka på **+ Lägg till**.
* Välj i listrutan, **SQL-databas**
* Under **SQL-databas**, Välj den befintliga databasen, och klicka sedan på **Välj**.
* Under **anslutningssträngen**, ange användarnamn och lösenord för databasen, och klicka sedan på **OK**.
* I den **lägga till dataanslutningar** klickar du på **OK**.

Användarnamn och lösenord finns genom att visa anslutningssträngen för måldatabasen i din migrerade mobiltjänst.

### <a name="set-up-authentication"></a>Konfigurera autentisering
Azure Mobile Apps kan du konfigurera Azure Active Directory, Facebook, Google, Microsoft och Twitter-autentisering inom tjänsten.  Anpassad autentisering måste ha utvecklats separat.  Referera till den [Autentiseringskoncept] dokumentation och [autentisering Quickstart] dokumentationen för mer information.  

## <a name="updating-clients"></a>Uppdatera mobila klienter
När du har en operativa mobilappsserverdel kan arbeta du med en ny version av ditt klientprogram som använder den. Mobile Apps innehåller också en ny version av klienten SDK: er och liknar serveruppgraderingen ovan, behöver du ta bort alla referenser till SDK: er för Mobile Services innan du installerar Mobile Apps-versioner.

En av de viktigaste ändringarna mellan versionerna är att konstruktorer längre kräver en nyckel för programmet.
Du nu överföra bara URL-adressen till din Mobilapp. Till exempel på .NET-klienter på `MobileServiceClient` konstruktorn är nu:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Du kan läsa om hur du installerar nya SDK: er och använda den nya strukturen via länkarna nedan:

* [Android-versionen 2.2 eller senare](app-service-mobile-android-how-to-use-client-library.md)
* [iOS version 3.0.0 eller senare](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 eller senare](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova version 2.0 eller senare](app-service-mobile-cordova-how-to-use-client-library.md)

Om ditt program gör användning av push-meddelanden, anteckna registreringen instruktioner för varje plattform, har eftersom det ändringar det också.

När du har den nya klientversionen redo prova mot projektet uppgraderade servern. När du verifierar att det fungerar, kan du släppa en ny version av programmet till kunder. Slutligen, när dina kunder har haft möjlighet att få dessa uppdateringar, du kan ta bort Mobile Services-versionen av din app. Nu är har du uppgraderat till en App Service-Mobilapp med hjälp av den senaste Mobile Apps servern SDK.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[vad är Mobilappar?]: app-service-mobile-value-prop.md
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
[priser för Apptjänst]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Autentiseringskoncept]: ../app-service/app-service-authentication-overview.md
[autentisering Quickstart]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

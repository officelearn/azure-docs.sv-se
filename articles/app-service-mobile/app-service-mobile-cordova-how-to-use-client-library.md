---
title: "Hur du använder Apache Cordova-pluginprogrammet för Azure Mobile Apps"
description: "Hur du använder Apache Cordova-pluginprogrammet för Azure Mobile Apps"
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: ebf0e911eeada0e529f908dd3e3430c94edae763
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Hur du använder Apache Cordova-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [Apache Cordova-pluginprogrammet för Azure Mobile Apps]. Om du har använt Azure Mobile Apps först slutföra [Azure Mobile Apps Snabbstart] skapa en tabell för att skapa en serverdel och ladda ned en förskapad Apache Cordova-projekt. I den här guiden fokuserar vi på klientsidan Apache Cordova-pluginprogrammet.

## <a name="supported-platforms"></a>Plattformar som stöds
Detta SDK stöder Apache Cordova v6.0.0 och senare på iOS, Android och Windows enheter.  Plattformsstödet är följande:

* Android API 19-24 (KitKat via Nougat).
* iOS version 8.0 och senare.
* Windows Phone 8.1.
* Universella Windows-plattformen.

## <a name="Setup"></a>Installationen och förutsättningar
Den här handboken förutsätts att du har skapat en serverdel med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier. Den här handboken förutsätter också att du har lagt till Apache Cordova-pluginprogrammet din kod.  Om du inte har gjort det, kan du lägga till Apache Cordova-pluginprogrammet projektet på kommandoraden:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Mer information om hur du skapar [din första Apache Cordova-app], finns i deras dokumentation.

## <a name="ionic"></a>Ställa in en joniska v2-app

Om du vill konfigurera ett joniska v2-projekt, skapa en grundläggande app och lägga till Cordova-pluginprogrammet:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Lägg till följande rader till `app.component.ts` att skapa klienten objektet:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Du kan nu skapa och köra projektet i webbläsaren:

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova-pluginprogrammet stöder både joniska v1 och v2-appar.  Endast joniska v2-appar kräver ytterligare deklarationen för den `WindowsAzure` objekt.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Så här: autentisera användare
Azure Apptjänst har stöd för autentisering och auktorisering av appanvändare som använder olika externa identitetsleverantörer: Facebook, Google, Account och Twitter. Du kan ange behörigheter på tabeller för att begränsa åtkomsten för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i [komma igång med autentisering] kursen.

När du använder autentisering i en Apache Cordova-app måste följande Cordova-plugin-program vara tillgängliga:

* [cordova-plugin-enhet]
* [cordova-plugin-inappbrowser]

Två autentisering flöden stöds: ett flöde för server och ett klient-flöde.  Server-flöde ger enklaste autentiseringsupplevelse, eftersom det är beroende av leverantörens Webbgränssnitt för autentisering. Flödet tillåter djupare integrering med specifika funktioner som enkel inloggning som den förlitar sig på provider-specifik enhetsspecifika SDK.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Så här: konfigurera din mobila App Service för externa omdirigerings-URL.
Flera typer av Apache Cordova-program använda en loopback-funktion för att hantera OAuth UI-flöden.  OAuth UI-flöden på localhost problem Eftersom Autentiseringstjänsten endast användas att använda tjänsten som standard.  Exempel på problematiska OAuth UI-flöden är:

* Flytta efterföljande emulatorn.
* Läs in igen med joniska för Direktmigrering.
* Kör mobila serverdel lokalt
* Mobila serverdel som körs i en annan Azure App Service än en tillhandahåller autentisering.

Följ instruktionerna för att lägga till dina lokala inställningar i konfigurationen:

1. Logga in på [Azure-portalen]
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din Mobilapp.
3. Klicka på **verktyg**
4. Klicka på **resursläsaren** i Följ-menyn och klicka på **Gå**.  Öppnar ett nytt fönster eller flik.
5. Expandera den **config**, **authsettings** noder för din plats i det vänstra navigeringsfönstret.
6. Klicka på **redigera**
7. Sök efter ”allowedExternalRedirectUrls”-elementet.  Det kan ställas in på null eller en matris med värden.  Ändra värdet till följande värde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL: er med URL: er för din tjänst.  Exempel är ”http://localhost: 3000” (för Node.js-exempel service) eller ”http://localhost:4400” (för små service).  Dessa URL: er är dock exempel - sätt, inklusive för de tjänster som anges i exempel kan vara olika.
8. Klicka på den **läsning och skrivning** knappen i det övre högra hörnet på skärmen.
9. Klicka på gröna **PLACERA** knappen.

Inställningarna sparas i det här läget.  Stäng inte webbläsarfönstret förrän inställningarna är klar sparar.
Lägg även till dessa loopback-URL: er CORS-inställningarna för din Apptjänst:

1. Logga in på [Azure-portalen]
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din Mobilapp.
3. Inställningsbladet öppnas automatiskt.  Om den inte, klickar du på **alla inställningar**.
4. Klicka på **CORS** API-menyn.
5. Ange den URL som du vill lägga till i rutan som visas och tryck RETUR.
6. Ange ytterligare URL: er som behövs.
7. Klicka på **spara** spara inställningarna.

Det tar cirka 10 – 15 sekunder för de nya inställningarna ska börja gälla.

## <a name="register-for-push"></a>Så här: registrera för push-meddelanden
Installera den [phonegap-plugin-push] att hantera push-meddelanden.  Det här plugin-programmet kan enkelt läggas till med hjälp av den `cordova plugin add` kommandot på kommandoraden eller via installationsprogrammet för Git-plugin-program i Visual Studio.  Följande kod i din Apache Cordova-app registrerar din enhet för push-meddelanden:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Använda Notification Hubs SDK för att skicka push-meddelanden från servern.  Aldrig skicka push-meddelanden direkt från klienter. Detta kan användas för att utlösa en denial of service-attack mot Meddelandehubbar och pns-systemet.  Pns-systemet kunde förbud trafiken till följd av sådana attacker.

## <a name="more-information"></a>Mer information

Du hittar detaljerad information om API i vår [API-dokumentationen](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com
[Azure Mobile Apps Snabbstart]: app-service-mobile-cordova-get-started.md
[komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-pluginprogrammet för Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[din första Apache Cordova-app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-enhet]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

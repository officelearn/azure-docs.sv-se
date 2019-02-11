---
title: Hur du använder Apache Cordova-pluginprogrammet för Azure Mobile Apps
description: Hur du använder Apache Cordova-pluginprogrammet för Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: cddb3769cfc5a2ba002e19036d986f4165670dc1
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962459"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Hur du använder Apache Cordova-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [Apache Cordova-Plugin för Azure Mobile Apps]. Om du är nybörjare på Azure Mobile Apps slutföra [Azure mobila appar Snabbstart] för att skapa en serverdel, skapa en tabell och ladda ned ett färdiga Apache Cordova-projekt. I den här guiden fokuserar vi på klientsidan Apache Cordova-plugin-programmet.

## <a name="supported-platforms"></a>Plattformar som stöds
Detta SDK har stöd för Apache Cordova v6.0.0 och senare på iOS, Android och Windows enheter.  Plattformsstödet är följande:

* Android API 19 – 24 (KitKat via Nougat).
* iOS version 8.0 och senare.
* Windows Phone 8.1.
* Universal Windows Platform.

## <a name="Setup"></a>Installation och krav
Den här guiden förutsätter att du har skapat en serverdel med en tabell. Den här handboken förutsätts att tabellen har samma schema som tabellerna i dessa självstudier. Den här guiden förutsätter också att du har lagt till Apache Cordova-plugin-programmet i koden.  Om du inte har gjort det, kan du lägga till Apache Cordova-plugin-programmet till ditt projekt på kommandoraden:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Mer information om hur du skapar [din första Apache Cordova-app], finns i deras dokumentation.

## <a name="ionic"></a>Hur du konfigurerar en joniska v2-app

Om du vill konfigurera ett joniska v2-projekt, skapar en grundläggande app och lägga till Cordova-plugin-programmet:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Lägg till följande rader till `app.component.ts` att skapa klientobjektet:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Du kan nu skapa och köra projektet i webbläsaren:

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova-plugin-programmet har stöd för både joniska v1 och v2-appar.  Endast joniska v2-appar kräver ytterligare deklarationen för den `WindowsAzure` objekt.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Hur: Autentisera användare
Azure App Service stöder autentisering och auktorisering av appanvändare som använder olika externa indentitetsprovidrar: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomst för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i den [komma igång med autentisering] självstudien.

När du använder autentisering i en Apache Cordova-app, måste följande Cordova-plugin-program vara tillgängliga:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Två autentiseringsflöden stöds: ett flöde för server och ett klient-flöde.  Server-flödet innehåller enklaste autentiseringsupplevelse som den är beroende av leverantörens Webbgränssnitt för autentisering. Klientflödet gör för djupare integrering med specifika funktioner som enkel inloggning som den är beroende av provider-specifik enhetsspecifika SDK: er.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Hur: Konfigurera Mobile App-tjänsten för externa Omdirigeringswebbadresser.
Flera typer av Apache Cordova-program använda en loopback-funktion för att hantera OAuth UI flöden.  OAuth UI-flöden på localhost orsaka problem Eftersom Autentiseringstjänsten vet bara hur du använder tjänsten som standard.  Exempel på problematiska OAuth UI flöden:

* Ripple-emulatorn.
* Live återinläsning med joniska.
* Kör mobil serverdel lokalt
* Kör mobila serverdelstjänst i en annan Azure App Service än en tillhandahåller autentisering.

Följ dessa instruktioner för att lägga till dina lokala inställningar i konfigurationen:

1. Logga in på [Azure Portal]
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din Mobilapp.
3. Klicka på **verktyg**
4. Klicka på **resursläsaren** i Följ-menyn och klicka på **Gå**.  Ett nytt fönster eller flik öppnas.
5. Expandera den **config**, **authsettings** noder för din plats i det vänstra navigeringsfältet.
6. Klicka på **redigera**
7. Sök efter ”allowedExternalRedirectUrls”-element.  Det kan ställas in på null eller en matris med värden.  Ändra värdet till följande värde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL: er med URL: er för din tjänst.  Exempel är ”http://localhost:3000” (för Node.js-exemplet service), eller ”http://localhost:4400” (för tjänsten Ripple).  Dessa URL: er är dock exempel – din situation, inklusive för tjänster som nämns i exempel kan vara olika.
8. Klicka på den **Läs/Skriv** knappen i det övre högra hörnet på skärmen.
9. Klicka på gröna **PLACERA** knappen.

Inställningarna sparas i det här läget.  Stäng inte webbläsarfönstret förrän du är klar med inställningarna sparas.
Också lägga till dessa loopback-URL: er i CORS-inställningarna för din App Service:

1. Logga in på [Azure Portal]
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din Mobilapp.
3. Inställningar-bladet öppnas automatiskt.  Om det inte, klickar på **alla inställningar**.
4. Klicka på **CORS** under menyn API.
5. Ange den URL som du vill lägga till i rutan som visas och tryck RETUR.
6. Ange ytterligare URL: er som behövs.
7. Spara inställningarna genom att klicka på **Spara**.

Det tar cirka 10 – 15 sekunder för de nya inställningarna ska börja gälla.

## <a name="register-for-push"></a>Hur: Registrera dig för push-meddelanden
Installera den [modul phonegap plugin push] att hantera pushmeddelanden.  Det här plugin-program kan enkelt läggas till med hjälp av den `cordova plugin add` kommandot på kommandoraden eller via installationsprogrammet för Git-plugin-programmet i Visual Studio.  Följande kod i din Apache Cordova-app registrerar din enhet för push-meddelanden:

```javascript
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

Använda Notification Hubs SDK för att skicka push-meddelanden från servern.  Skicka aldrig push-meddelanden direkt från klienter. Då kan användas för att utlösa en DoS-angrepp mot Meddelandehubbar eller pns-systemet.  Pns-systemet kan förbjuda trafiken till följd av sådana attacker.

## <a name="more-information"></a>Mer information

Du hittar detaljerad information om API: et i vår [API-dokumentation](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure mobila appar Snabbstart]: app-service-mobile-cordova-get-started.md
[Komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-Plugin för Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[din första Apache Cordova-app]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[modul phonegap plugin push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

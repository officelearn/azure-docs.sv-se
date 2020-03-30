---
title: Hur man använder Apache Cordova Plugin
description: Så här använder du Apache Cordova-plugin för Azure Mobile Apps
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459369"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Så här använder du Apache Cordova-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med den senaste [Apache Cordova-insticksprogrammet för Azure Mobile Apps]. Om du inte har tidigare azure mobile apps har du först slutfört [snabbstarten] för Azure Mobile Apps för att skapa en backend, skapa en tabell och hämta ett förbyggt Apache Cordova-projekt. I den här guiden fokuserar vi på apache Cordova-plugin på klientsidan.

## <a name="supported-platforms"></a>Plattformar som stöds
Detta SDK stöder Apache Cordova v6.0.0 och senare på iOS-, Android- och Windows-enheter.  Plattformsstödet är följande:

* Android API 19-24 (KitKat genom Nougat).
* iOS-versioner 8.0 och senare.
* Windows Phone 8.1.
* Universell Windows-plattform.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Inställningar och förutsättningar
Den här guiden förutsätter att du har skapat en backend med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier. Denna guide förutsätter också att du har lagt till Apache Cordova Plugin till din kod.  Om du inte har gjort det kan du lägga till Apache Cordova-insticksprogrammet i ditt projekt på kommandoraden:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Mer information om hur du skapar [din första Apache Cordova-app]finns i deras dokumentation.

## <a name="setting-up-an-ionic-v2-app"></a><a name="ionic"></a>Ställa in en Ionic v2-app

För att korrekt konfigurera en Ionic v2-projekt, först skapa en grundläggande app och lägga till Cordova plugin:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Lägg till följande `app.component.ts` rader för att skapa klientobjektet:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Du kan nu bygga och köra projektet i webbläsaren:

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova-insticksprogrammet stöder både Ionic v1- och v2-appar.  Endast Ionic v2-apparna kräver `WindowsAzure` ytterligare deklaration för objektet.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Så här: Autentiserar användare
Azure App Service stöder autentisering och auktorisera appanvändare med hjälp av olika externa identitetsleverantörer: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för specifika åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i serverskript. Mer information finns i självstudien [Kom igång med autentisering.]

När du använder autentisering i en Apache Cordova-app måste följande Cordova-insticksprogram vara tillgängliga:

* [cordova-plugin-enhet]
* [cordova-plugin-inappbrowser]

Två autentiseringsflöden stöds: ett serverflöde och ett klientflöde.  Serverflödet ger den enklaste autentiseringsupplevelsen, eftersom det är beroende av leverantörens webbautentiseringsgränssnitt. Klientflödet möjliggör djupare integrering med enhetsspecifika funktioner som enkel inloggning eftersom det är beroende av leverantörsspecifika enhetsspecifika SDK:er.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Så här konfigurerar du mobilapptjänsten för externa omdirigeringsadresser.
Flera typer av Apache Cordova-program använder en loopback-funktion för att hantera OAuth UI-flöden.  OAuth UI flöden på localhost orsaka problem eftersom autentiseringstjänsten bara vet hur du använder din tjänst som standard.  Exempel på problematiska OAuth UI flöden inkluderar:

* Ripple-emulatorn.
* Live Reload med joniska.
* Köra den mobila backend lokalt
* Köra den mobila serverden i en annan Azure App-tjänst än den som tillhandahåller autentisering.

Följ dessa instruktioner för att lägga till dina lokala inställningar i konfigurationen:

1. Logga in på [Azure-portalen]
2. Välj **Alla resurser** eller App **Services** och klicka sedan på namnet på mobilappen.
3. Klicka på **Verktyg**
4. Klicka på **Resursutforskaren** på OBSERVE-menyn och sedan på **Gå**.  Ett nytt fönster eller en ny flik öppnas.
5. Expandera **config-** **authsettings-noderna** för din webbplats i den vänstra navigeringen.
6. Klicka på **Redigera**
7. Leta efter elementet "allowedExternalRedirectUrls".  Den kan vara inställd på null eller en matris med värden.  Ändra värdet till följande värde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt webbadresserna med webbadresserna till tjänsten.  Exempel `http://localhost:3000` är (för exempeltjänsten Node.js) eller `http://localhost:4400` (för Ripple-tjänsten).  Dessa webbadresser är dock exempel - din situation, även för de tjänster som nämns i exemplen, kan vara annorlunda.
8. Klicka på knappen **Läs/skriv** längst upp till höger på skärmen.
9. Klicka på den gröna **PUT-knappen.**

Inställningarna sparas just nu.  Stäng inte webbläsarfönstret förrän inställningarna har sparats klart.
Lägg också till dessa loopback-URL:er i CORS-inställningarna för apptjänsten:

1. Logga in på [Azure-portalen]
2. Välj **Alla resurser** eller App **Services** och klicka sedan på namnet på mobilappen.
3. Bladet Inställningar öppnas automatiskt.  Om den inte gör det klickar du på **Alla inställningar**.
4. Klicka på **CORS** under API-menyn.
5. Ange webbadressen som du vill lägga till i rutan och tryck på Retur.
6. Ange ytterligare webbadresser efter behov.
7. Spara inställningarna genom att klicka på **Spara**.

Det tar cirka 10-15 sekunder innan de nya inställningarna börjar gälla.

## <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Så här: Registrera dig för push-meddelanden
Installera [phonegap-plugin-push] för att hantera push-meddelanden.  Denna plugin kan enkelt `cordova plugin add` läggas till med kommandot på kommandoraden, eller via Git plugin installationsprogrammet i Visual Studio.  Följande kod i din Apache Cordova-app registrerar din enhet för push-meddelanden:

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

Använd SDK för meddelandehubbar för att skicka push-meddelanden från servern.  Skicka aldrig push-meddelanden direkt från klienter. Detta kan användas för att utlösa en denial of service-attack mot meddelandehubbar eller PNS.  PNS kan förbjuda din trafik till följd av sådana attacker.

## <a name="more-information"></a>Mer information

Du hittar detaljerad API-information i vår [API-dokumentation](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com
[Snabbstart för Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin för Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[din första Apache Cordova-app]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-enhet]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

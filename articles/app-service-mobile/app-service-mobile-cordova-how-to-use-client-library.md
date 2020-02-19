---
title: Använda Apache Cordova-plugin-programmet
description: Så här använder du Apache Cordova-plugin-programmet för Azure Mobile Apps
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459369"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Använda Apache Cordova-klient bibliotek för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med hjälp av det senaste [Apache Cordova-plugin-program för Azure Mobile Apps]. Om du är nybörjare på Azure Mobile Apps måste du först slutföra [Azure Mobile Apps Snabbstart] för att skapa en server del, skapa en tabell och hämta ett fördefinierat Apache Cordova-projekt. I den här hand boken fokuserar vi på klient sidans Apache Cordova-plugin-program.

## <a name="supported-platforms"></a>Plattformar som stöds
SDK: n stöder Apache Cordova v 6.0.0 och senare på iOS-, Android-och Windows-enheter.  Plattforms stödet är följande:

* Android API 19-24 (KitKat till nougat).
* iOS-versioner 8,0 och senare.
* Windows Phone 8,1.
* Universell Windows-plattform.

## <a name="Setup"></a>Konfiguration och krav
Den här guiden förutsätter att du har skapat en server del med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i de här självstudierna. Den här guiden förutsätter också att du har lagt till Apache Cordova-plugin-programmet i din kod.  Om du inte har gjort det kan du lägga till Apache Cordova-plugin-programmet i projektet på kommando raden:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Mer information om hur [din första Apache Cordova-app]finns i dokumentationen.

## <a name="ionic"></a>Konfigurera en Joniska v2-app

Om du vill konfigurera ett Joniska v2-projekt korrekt skapar du först en grundläggande app och lägger till Cordova-plugin-programmet:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Lägg till följande rader i `app.component.ts` för att skapa klient objektet:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Nu kan du skapa och köra projektet i webbläsaren:

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova-plugin-programmet stöder både Joniska v1-och v2-appar.  Endast Joniska v2-appar kräver ytterligare deklaration för `WindowsAzure`-objektet.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Gör så här: autentisera användare
Azure App Service stöder autentisering och auktorisering av App-användare med olika externa identitets leverantörer: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för vissa åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i Server skript. Mer information finns i själv studie kursen [Kom igång med autentisering] .

När du använder autentisering i en Apache Cordova-app måste följande Cordova-plugin-program vara tillgängliga:

* [Cordova – plugin-enhet]
* [Cordova-plugin-inappbrowser]

Två autentiserings flöden stöds: ett Server flöde och ett klient flöde.  Server flödet ger den enklaste autentiseringen, eftersom det förlitar sig på providerns webbautentiserings-gränssnitt. Klient flödet gör det möjligt att integrera djupare med enhetsspecifika funktioner, till exempel enkel inloggning, eftersom det är beroende av providerspecifika leverantörsspecifika SDK: er.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Gör så här: Konfigurera mobil App Service för externa omdirigerings-URL: er.
Flera typer av Apache Cordova-program använder en loopback-funktion för att hantera OAuth UI-flöden.  OAuth UI-flöden på localhost orsakar problem eftersom Autentiseringstjänsten bara vet hur tjänsten används som standard.  Exempel på problematiska OAuth UI-flöden är:

* Krusnings-emulatorn.
* Direktsänd belastning med Joniska.
* Köra mobil Server delen lokalt
* Att köra mobil Server delen i en annan Azure App Service än den som tillhandahåller autentisering.

Följ dessa anvisningar för att lägga till dina lokala inställningar i konfigurationen:

1. Logga in på [Azure Portal]
2. Välj **alla resurser** eller **app Services** och klicka sedan på namnet på din mobilapp.
3. Klicka på **verktyg**
4. Klicka på **resurs läsaren** på menyn Observera och klicka sedan på **gå**.  Ett nytt fönster eller en ny flik öppnas.
5. Expandera **authsettings** -noderna **config**, för din webbplats i det vänstra navigerings fältet.
6. Klicka på **Redigera**
7. Leta efter elementet "allowedExternalRedirectUrls".  Det kan vara inställt på null eller en matris med värden.  Ändra värdet till följande värde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL: erna med URL: er för din tjänst.  Exempel på detta är `http://localhost:3000` (för Node. js-exempelprogrammet) eller `http://localhost:4400` (för Krusnings tjänsten).  Dessa URL: er är dock exempel – din situation, inklusive för de tjänster som nämns i exemplen, kan vara olika.
8. Klicka på knappen **Läs/skriv** i det övre högra hörnet på skärmen.
9. Klicka på knappen grönt **placering** .

Inställningarna sparas i det här läget.  Stäng inte webbläsarfönstret förrän inställningarna har sparats.
Lägg också till dessa loopback-URL: er till CORS-inställningarna för din App Service:

1. Logga in på [Azure Portal]
2. Välj **alla resurser** eller **app Services** och klicka sedan på namnet på din mobilapp.
3. Bladet inställningar öppnas automatiskt.  Om den inte är det klickar du på **alla inställningar**.
4. Klicka på **CORS** under API-menyn.
5. Ange den URL som du vill lägga till i rutan och tryck på RETUR.
6. Ange ytterligare URL: er efter behov.
7. Spara inställningarna genom att klicka på **Spara**.

Det tar ungefär 10-15 sekunder innan de nya inställningarna börjar gälla.

## <a name="register-for-push"></a>Gör så här: registrera dig för push-meddelanden
Installera [PhoneGap – plugin-push] för att hantera push-meddelanden.  Det här plugin-programmet kan enkelt läggas till med kommandot `cordova plugin add` på kommando raden eller via installations programmet för git-plugin i Visual Studio.  Följande kod i din Apache Cordova-App registrerar din enhet för push-meddelanden:

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

Använd Notification Hubs SDK för att skicka push-meddelanden från servern.  Skicka aldrig push-meddelanden direkt från klienter. Detta kan användas för att utlösa en denial of service-attack mot Notification Hubs eller PNS.  PNS kan förbjuda trafiken till följd av sådana angrepp.

## <a name="more-information"></a>Mer information

Detaljerad API-information finns i vår [API-dokumentation](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps Snabbstart]: app-service-mobile-cordova-get-started.md
[Kom igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-plugin-program för Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[din första Apache Cordova-app]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap – plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova – plugin-enhet]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

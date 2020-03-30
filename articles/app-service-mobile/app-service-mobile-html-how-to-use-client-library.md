---
title: Använda JavaScript SDK
description: Så här använder du v för Azure Mobile Apps
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461529"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Använda JavaScript-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med hjälp av de senaste [JavaScript SDK för Azure Mobile Apps]. Om du inte har tidigare i Azure Mobile Apps slutför du först [snabbstarten] för Azure Mobile Apps för att skapa en backend och skapa en tabell. I den här guiden fokuserar vi på att använda den mobila backend i HTML / JavaScript webbapplikationer.

## <a name="supported-platforms"></a>Plattformar som stöds
Vi begränsar webbläsarstöd till de aktuella och sista versionerna av de stora webbläsarna: Google Chrome, Microsoft Edge, Microsoft Internet Explorer och Mozilla Firefox.  Vi förväntar oss att SDK ska fungera med någon relativt modern webbläsare.

Paketet distribueras som en Universell JavaScript-modul, så det stöder globals, AMD och CommonJS-format.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Inställningar och förutsättningar
Den här guiden förutsätter att du har skapat en backend med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier.

Installera Azure Mobile Apps JavaScript SDK `npm` kan göras via kommandot:

```
npm install azure-mobile-apps-client --save
```

Biblioteket kan också användas som en ES2015-modul, inom CommonJS-miljöer som Browserify och Webpack och som ett AMD-bibliotek.  Ett exempel:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Du kan också använda en färdig version av SDK genom att ladda ner direkt från vår CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Så här: Autentiserar användare
Azure App Service stöder autentisering och auktorisera appanvändare med hjälp av olika externa identitetsleverantörer: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för specifika åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i serverskript. Mer information finns i självstudien [Kom igång med autentisering.]

Två autentiseringsflöden stöds: ett serverflöde och ett klientflöde.  Serverflödet ger den enklaste autentiseringsupplevelsen, eftersom det är beroende av leverantörens webbautentiseringsgränssnitt. Klientflödet möjliggör djupare integrering med enhetsspecifika funktioner som enkel inloggning eftersom det är beroende av leverantörsspecifika SDK:er.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Så här konfigurerar du mobilapptjänsten för externa omdirigeringsadresser.
Flera typer av JavaScript-program använder en loopback-funktion för att hantera OAuth UI-flöden.  Dessa funktioner är:

* Köra tjänsten lokalt
* Använda Live Reload med joniska Framework
* Omdirigerar till App Service för autentisering.

Att köra lokalt kan orsaka problem eftersom App Service-autentisering som standard endast är konfigurerad för att tillåta åtkomst från din mobile app-serverdel. Så här ändrar du apptjänstinställningarna för att aktivera autentisering när du kör servern lokalt:

1. Logga in på [Azure-portalen]
2. Navigera till din mobile app-backend.
3. Välj **Resursutforskare** på **UTVECKLINGSVERKTYG-menyn.**
4. Klicka på **Gå** för att öppna resursutforskaren för din mobile app-backend på en ny flik eller ett nytt fönster.
5. Expandera **config** > **authsettings-noden** för din app.
6. Klicka på knappen **Redigera** om du vill aktivera redigering av resursen.
7. Leta reda på elementet **allowedExternalRedirectUrls,** som ska vara null. Lägg till webbadresserna i en matris:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt url:erna i matrisen med url:erna för `http://localhost:3000` tjänsten, som i det här exemplet gäller den lokala nod.js-exempeltjänsten. Du kan `http://localhost:4400` också använda för Ripple-tjänsten eller någon annan WEBBADRESS, beroende på hur appen är konfigurerad.
8. Högst upp på sidan klickar du på **Läs/skriv**och sedan på **PUT** för att spara uppdateringarna.

Du måste också lägga till samma loopback-URL:er i CORS-vitlistningsinställningarna:

1. Navigera tillbaka till [Azure-portalen].
2. Navigera till din mobile app-backend.
3. Klicka på **CORS** på **API-menyn.**
4. Ange varje URL i den tomma **tillåtna ursprungstextrutan.**  En ny textruta skapas.
5. Klicka på **SPARA**

När backend-uppdateringarna har uppdaterats kan du använda de nya loopback-url:erna i appen.

<!-- URLs. -->
[Snabbstart för Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portal]: https://portal.azure.com/
[JavaScript SDK för Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

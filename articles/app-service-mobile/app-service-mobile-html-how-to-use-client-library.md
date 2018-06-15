---
title: Hur du använder JavaScript SDK för Azure Mobile Apps
description: Använda v för Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ba2eb5a7f888e4cffcd798259afa8194b4021025
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151084"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Hur du använder JavaScript-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [JavaScript SDK för Azure Mobile Apps]. Om du har använt Azure Mobile Apps först slutföra [Azure Mobile Apps Snabbstart] att skapa en serverdel och skapa en tabell. I den här guiden fokuserar vi på med mobila serverdel i HTML/JavaScript-webbprogram.

## <a name="supported-platforms"></a>Plattformar som stöds
Vi har begränsat stöd för webbläsare för de aktuella och senaste versionerna av större webbläsare: Google Chrome, Microsoft Edge, Microsoft Internet Explorer och Mozilla Firefox.  Vi räknar med SDK: N ska fungera med relativt moderna webbläsare.

Paketet distribueras som en Universal JavaScript-modul, så att den stöder globals AMD, och CommonJS format.

## <a name="Setup"></a>Installationen och förutsättningar
Den här handboken förutsätts att du har skapat en serverdel med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier.

Installera Azure Mobile Apps JavaScript SDK kan göras den `npm` kommando:

```
npm install azure-mobile-apps-client --save
```

Biblioteket kan också användas som en ES2015-modul i CommonJS miljöer, till exempel Browserify och Webpack och som en AMD-bibliotek.  Exempel:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Du kan också använda en förskapad version av SDK genom att hämta direkt från våra CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Så här: autentisera användare
Azure Apptjänst har stöd för autentisering och auktorisering av appanvändare som använder olika externa identitetsleverantörer: Facebook, Google, Account och Twitter. Du kan ange behörigheter på tabeller för att begränsa åtkomsten för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i [komma igång med autentisering] kursen.

Två autentisering flöden stöds: ett flöde för server och ett klient-flöde.  Server-flöde ger enklaste autentiseringsupplevelse, eftersom det är beroende av leverantörens Webbgränssnitt för autentisering. Flödet tillåter djupare integrering med specifika funktioner som enkel inloggning som den förlitar sig på provider-specifik SDK: er.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Så här: konfigurera din mobila App Service för externa omdirigerings-URL.
Flera typer av JavaScript-program använda en loopback-funktion för att hantera OAuth UI-flöden.  Dessa funktioner är:

* Kör tjänsten lokalt
* Med levande ladda joniska Framework
* Omdirigera till App Service för autentisering.

Kör lokalt kan orsaka problem Eftersom som standard konfigureras endast Apptjänst-autentisering för att tillåta åtkomst från din mobilappsserverdel. Använd följande steg om du vill ändra App Service-inställningar om du vill aktivera autentisering när du kör server lokalt:

1. Logga in på [Azure-portalen]
2. Gå till din mobilappsserverdel.
3. Välj **resursläsaren** i den **UTVECKLINGSVERKTYG** menyn.
4. Klicka på **Gå** öppna resursutforskaren för din mobilappsserverdel i en ny flik eller ett fönster.
5. Expandera den **config** > **authsettings** noden för din app.
6. Klicka på den **redigera** för att aktivera redigering av resursen.
7. Hitta de **allowedExternalRedirectUrls** element som ska vara null. Lägg till URL-adresserna i en matris:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL-adresser i matrisen med URL: er för tjänsten, som i det här exemplet är `http://localhost:3000` för den lokala tjänsten för Node.js-exempel. Du kan också använda `http://localhost:4400` för tjänsten små eller några andra URL, beroende på hur din app har konfigurerats.
8. Överst på sidan, klickar du på **Skrivskyddstyp**, klicka på **PLACERA** att spara uppdateringarna.

Du måste också lägga till samma loopback-URL: er i de godkända-CORS-inställningarna:

1. Gå tillbaka till den [Azure-portalen].
2. Gå till din mobilappsserverdel.
3. Klicka på **CORS** i den **API** menyn.
4. Ange varje URL i tomt **tillåtna ursprung** textruta.  En ny textruta skapas.
5. Klicka på **spara**

När serverdelen uppdaterar kommer du att kunna använda de nya loopback-URL: er i din app.

<!-- URLs. -->
[Azure Mobile Apps Snabbstart]: app-service-mobile-cordova-get-started.md
[komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portalen]: https://portal.azure.com/
[JavaScript SDK för Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

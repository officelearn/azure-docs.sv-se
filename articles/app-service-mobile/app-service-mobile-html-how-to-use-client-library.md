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
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106752"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Hur du använder JavaScript-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [JavaScript SDK för Azure Mobile Apps]. Om du är nybörjare på Azure Mobile Apps slutföra [Azure mobila appar Snabbstart] att skapa en serverdel och skapa en tabell. I den här guiden kommer fokusera vi på att använda mobila serverdelstjänst i HTML/JavaScript-webbprogram.

## <a name="supported-platforms"></a>Plattformar som stöds
Vi har en gräns webbläsarstöd till de aktuella och de senaste versionerna av större webbläsare:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer och Mozilla Firefox.  Vi räknar med SDK: N ska fungera med alla relativt moderna webbläsare.

Paketet distribueras som en Universal JavaScript-modul så att den stöder globals, AMD, och CommonJS format.

## <a name="Setup"></a>Installation och krav
Den här guiden förutsätter att du har skapat en serverdel med en tabell. Den här handboken förutsätts att tabellen har samma schema som tabellerna i dessa självstudier.

Installera JavaScript SDK för Azure Mobile Apps kan ske via den `npm` kommando:

```
npm install azure-mobile-apps-client --save
```

Biblioteket kan också användas som en ES2015-modul i CommonJS miljöer, till exempel Browserify och Webpack och som en AMD-bibliotek.  Exempel:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Du kan också använda en färdiga version av SDK: N genom att ladda ned direkt från vår CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Hur: Autentisera användare
Azure App Service stöder autentisering och auktorisering av appanvändare som använder olika externa indentitetsprovidrar: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomst för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i den [komma igång med autentisering] självstudien.

Två autentiseringsflöden stöds: ett flöde för server och ett klient-flöde.  Server-flödet innehåller enklaste autentiseringsupplevelse som den är beroende av leverantörens Webbgränssnitt för autentisering. Klientflödet gör för djupare integrering med specifika funktioner som enkel inloggning som den är beroende av provider-specifik SDK: er.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Hur: Konfigurera Mobile App-tjänsten för externa Omdirigeringswebbadresser.
Flera typer av JavaScript-program använda en loopback-funktion för att hantera OAuth UI flöden.  Dessa funktioner är:

* Kör din tjänst lokalt
* Med Live återinläsning joniska Framework
* Omdirigera till App Service för autentisering.

Kan orsaka problem om du kör lokalt eftersom som standard konfigureras bara App Service-autentisering för att tillåta åtkomst från din mobilappsserverdel. Använd följande steg om du vill ändra inställningarna för App Service för att aktivera autentisering när du kör server lokalt:

1. Logga in på [Azure Portal]
2. Gå till din mobilappsserverdel.
3. Välj **resursläsaren** i den **UTVECKLINGSVERKTYG** menyn.
4. Klicka på **Gå** att öppna resursutforskaren för din mobilappsserverdel i en ny flik eller fönster.
5. Expandera den **config** > **authsettings** noden för din app.
6. Klicka på den **redigera** knappen Aktivera redigering av resursen.
7. Hitta den **allowedExternalRedirectUrls** element som ska vara null. Lägg till din URL: er i en matris:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL: er i matrisen med URL: er för din tjänst, som i det här exemplet är `http://localhost:3000` för den lokala tjänsten för Node.js-exemplet. Du kan också använda `http://localhost:4400` för tjänsten Ripple eller några andra URL, beroende på hur din app har konfigurerats.
8. Överst på sidan klickar du på **Skrivskyddstyp**, klicka sedan på **PLACERA** att spara dina uppdateringar.

Du måste också lägga till samma loopback-URL: er i listan över godkända CORS-inställningarna:

1. Gå tillbaka till den [Azure Portal].
2. Gå till din mobilappsserverdel.
3. Klicka på **CORS** i den **API** menyn.
4. Ange varje URL i tomt **tillåtna ursprung** textrutan.  En ny textruta skapas.
5. Klicka på **spara**

När serverdelen uppdaterar kommer du att kunna använda de nya loopback-URL: er i din app.

<!-- URLs. -->
[Azure mobila appar Snabbstart]: app-service-mobile-cordova-get-started.md
[Komma igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[JavaScript SDK för Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

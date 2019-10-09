---
title: Så här använder du JavaScript SDK för Azure Mobile Apps
description: Så här använder du v för Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 410571320e5ffae9cf94c5035079e5b202190863
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027362"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Så här använder du JavaScript-klient biblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med hjälp av den senaste [Java Script SDK för Azure Mobile Apps]. Om du är nybörjare på Azure Mobile Apps måste du först slutföra [Azure Mobile Apps Snabbstart] för att skapa en server del och skapa en tabell. I den här hand boken fokuserar vi på att använda mobil Server delen i HTML/Java Script-webbprogram.

## <a name="supported-platforms"></a>Plattformar som stöds
Vi begränsar webbläsarens stöd till aktuella och sista versioner av de större webbläsarna:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer och Mozilla Firefox.  Vi förväntar oss att SDK: n ska fungera med en relativt modern webbläsare.

Paketet distribueras som en universell JavaScript-modul, så att det stöder globala, AMD-och CommonJS-format.

## <a name="Setup"></a>Konfiguration och krav
Den här guiden förutsätter att du har skapat en server del med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i de här självstudierna.

Installation av Azure Mobile Apps JavaScript SDK kan göras via kommandot `npm`:

```
npm install azure-mobile-apps-client --save
```

Biblioteket kan också användas som en ES2015-modul i CommonJS-miljöer som Browserify och WebPack och som ett AMD-bibliotek.  Exempel:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Du kan också använda en fördefinierad version av SDK genom att ladda ned direkt från vårt CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Hur: Autentisera användare
Azure App Service stöder autentisering och auktorisering av App-användare med olika externa identitets leverantörer: Facebook, Google, Microsoft-konto och Twitter. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för vissa åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i Server skript. Mer information finns i själv studie kursen [Kom igång med autentisering] .

Två autentiserings flöden stöds: ett Server flöde och ett klient flöde.  Server flödet ger den enklaste autentiseringen, eftersom det förlitar sig på providerns webbautentiserings-gränssnitt. Klient flödet gör det möjligt att integrera djupare med enhetsspecifika funktioner, till exempel enkel inloggning eftersom det förlitar sig på leverantörsspecifika SDK: er.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Hur: Konfigurera mobil App Service för externa omdirigerings-URL: er.
Flera typer av JavaScript-program använder en loopback-funktion för att hantera OAuth UI-flöden.  Dessa funktioner är:

* Köra din tjänst lokalt
* Använda Live reload med Joniska-ramverket
* Omdirigerar till App Service för autentisering.

Att köra lokalt kan orsaka problem eftersom App Service-autentisering som standard bara konfigureras för att tillåta åtkomst från din server del för mobila appar. Använd följande steg för att ändra App Service inställningarna för att aktivera autentisering när servern körs lokalt:

1. Logga in på [Azure Portal]
2. Navigera till Server delen för mobilappen.
3. Välj **resurs läsaren** på menyn **utvecklingsverktyg** .
4. Klicka på **gå** för att öppna resurs läsaren för Server delen för mobilappen på en ny flik eller i ett nytt fönster.
5. Expandera noden **config** > **authsettings** för din app.
6. Klicka på **Redigera** om du vill aktivera redigering av resursen.
7. Hitta **allowedExternalRedirectUrls** -elementet som ska vara null. Lägg till dina URL: er i en matris:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersätt URL: erna i matrisen med URL: er för din tjänst, som i det här exemplet är `http://localhost:3000` för den lokala Node. js-exempelprogrammet. Du kan också använda `http://localhost:4400` för Krusnings tjänsten eller någon annan URL, beroende på hur appen har kon figurer ATS.
8. Klicka på **Läs/skriv**på sidans överkant och klicka sedan på **Lägg** för att spara dina uppdateringar.

Du måste också lägga till samma loopback-URL: er till CORS vitlista-inställningarna:

1. Gå tillbaka till [Azure Portal].
2. Navigera till Server delen för mobilappen.
3. Klicka på **CORS** i **API** -menyn.
4. Ange varje URL i text rutan tomma **tillåtna ursprung** .  En ny text ruta skapas.
5. Klicka på **Spara**

Efter uppdateringar av Server delen kommer du att kunna använda de nya loopback-URL: erna i din app.

<!-- URLs. -->
[Azure Mobile Apps Snabbstart]: app-service-mobile-cordova-get-started.md
[Kom igång med autentisering]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Java Script SDK för Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

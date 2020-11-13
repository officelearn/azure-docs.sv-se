---
title: 'Snabb start: lägga till användar inloggning till en Node.js-webbapp | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du implementerar autentisering i ett Node.js-webbprogram med hjälp av OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 5cde4b539262c9e20bbcf2e7dd8c8e9de38035c3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594257"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Snabb start: lägga till inloggning med OpenID Anslut till en Node.js webbapp

I den här snabb starten hämtar och kör du ett kod exempel som visar hur du konfigurerar OpenID Connect-autentisering i ett webb program som skapats med hjälp av Node.js med Express. Exemplet är utformat för att köras på vilken plattform som helst.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registrera ditt program
1. Logga in på [Azure Portal](https://portal.azure.com/) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient:
    - Välj din profil på menyn i det övre högra hörnet på sidan och **Växla sedan katalog**.
    - Ändra sessionen till Azure AD-klienten där du vill skapa ditt program.

1. Gå till [Azure Active Directory > Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att registrera din app.

1. Välj **ny registrering.**

1. När sidan **Registrera ett program** visas anger du appens registrerings information:
    - I avsnittet **namn** anger du ett meningsfullt namn som ska visas för användare av appen. Till exempel: ' to WebApp
    - I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)**.

    Om det finns fler än en omdirigerings-URI måste du lägga till dem från fliken **autentisering** senare när appen har skapats.

1. Välj **Registrera** för att skapa appen.

1. På sidan **Översikt** för appen letar du reda på **programmets (klient) ID-** värde och registrerar det för senare. Du behöver det här värdet för att konfigurera programmet senare i det här projektet.

1. I listan över sidor för appen väljer du **Autentisering**.
    - I avsnittet **omdirigerings-URI: er** väljer du **webb** i kombinations rutan och anger följande omdirigerings-URI: `http://localhost:3000/auth/openid/return`
    - I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `https://localhost:3000`.
    - I avsnittet **Avancerade inställningar > implicita bidrag** kontrollerar du **ID-token** som det här exemplet kräver att det [implicita tilldelnings flödet](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) är aktiverat för att logga in användaren.

1. Välj **Spara**.

1. På sidan **certifikat & hemligheter** väljer du **ny klient hemlighet** i avsnittet **klient hemligheter** .
    - Ange en nyckel Beskrivning (för instansens program hemlighet).
    - Välj en nyckel varaktighet på **minst ett år, i två år** eller **upphör aldrig att gälla**.
    - När du klickar på knappen **Lägg till** visas nyckelvärdet. Kopiera nyckelvärdet och spara det på en säker plats.

    Du behöver den här nyckeln senare för att konfigurera programmet. Det här nyckelvärdet visas inte igen eller kan inte hämtas på något annat sätt, så du kan registrera det så snart det visas från Azure Portal.

## <a name="download-the-sample-application-and-modules"></a>Ladda ned exempel programmet och moduler

Sedan ska du klona exemplet lagrings platsen och installera NPM-modulerna.

Från gränssnittet eller kommando raden:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

eller

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Kör kommandot från projektets rot Katalog:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurera programmet

Ange parametrarna i `exports.creds` config.js enligt anvisningarna.

* Uppdatera `<tenant_name>` i `exports.identityMetadata` med Azure AD-klient namnet för formatet \* . onmicrosoft.com.
* Uppdatera `exports.clientID` med det program-ID som du antecknade från App Registration.
* Uppdatera `exports.clientSecret` med program hemligheten noterad från App Registration.
* Uppdatera `exports.redirectUrl` med omdirigerings-URI: n noterad från App Registration.

**Valfri konfiguration för produktions program:**

* Uppdatera `exports.destroySessionUrl` i config.js om du vill använda en annan `post_logout_redirect_uri` .

* Ange `exports.useMongoDBSessionStore` i config.js till true, om du vill använda [mongoDB](https://www.mongodb.com) eller andra [kompatibla sessioner](https://github.com/expressjs/session#compatible-session-stores).
Standardlagringsplatsen i det här exemplet är `express-session` . Standardsessions-lagringsplatsen är inte lämplig för produktion.

* Uppdatera `exports.databaseUri` om du vill använda mongoDB och en annan databas-URI.

* Uppdatera `exports.mongoDBSessionMaxAge` . Här kan du ange hur länge du vill behålla en session i mongoDB. Enheten är sekund (er).

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Starta mongoDB-tjänsten. Om du använder mongoDB-frågearkivet i den här appen måste du [Installera mongoDB](http://www.mongodb.org/) och starta tjänsten först. Om du använder standard arkivet kan du hoppa över det här steget.

Kör appen med följande kommando från kommando raden.

```
$ node app.js
```

**Är servern utmatnings svår att förstå?:** Vi använder `bunyan` för att logga i det här exemplet. Konsolen är inte särskilt begriplig för dig om du inte även installerar Bunyan och kör servern som ovan, men som rör den via Bunyan-binärfilen:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Du är klar!

Du kommer att ha en server igång `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om webb program scenariot som Microsoft Identity Platform stöder:
> [!div class="nextstepaction"]
> [Webbapp som loggar in i användar scenario](scenario-web-app-sign-user-overview.md)

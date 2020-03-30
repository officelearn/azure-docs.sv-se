---
title: Lägg till OIDC-inloggning i en Node.js-webbapp – Microsoft identity platform | Azure
description: Lär dig hur du implementerar autentisering i ett Node.js-webbprogram med OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4aa0cce83f9adc8c648656899ec6dc12d498e26b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77160456"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Snabbstart: Lägga till inloggning med OpenID Connect i en nod.js Web App

I den här snabbstarten får du lära dig hur du konfigurerar OpenID Connect-autentisering i ett webbprogram som skapats med Node.js med Express. Provet är utformat för att köras på alla plattformar.

## <a name="prerequisites"></a>Krav

För att kunna köra det här exemplet behöver du:

* Installera Node.js frånhttp://nodejs.org/

* Antingen ett [Microsoft-konto](https://www.outlook.com) eller [ett Office 365-utvecklarprogram](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registrera ditt program 
1. Logga in på [Azure-portalen](https://portal.azure.com/) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i mer än en Azure AD-klient:
    - Välj din profil på menyn längst upp till höger på sidan och växla sedan **katalog**.
    - Ändra din session till Azure AD-klienten där du vill skapa ditt program.

1. Navigera till [Azure Active Directory > Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att registrera din app.

1. Välj **Ny registrering.**

1. När sidan **Registrera ett program** visas anger du appens registreringsinformation:
    - I avsnittet **Namn** anger du ett meningsfullt namn som ska visas för användare av appen. Till exempel: MyWebApp
    - I avsnittet **Konton som stöds** väljer du Konton i en **organisationskatalog och personliga Microsoft-konton (t.ex Outlook.com.**

    Om det finns fler än en omdirigerings-URI:er måste du lägga till dessa från fliken **Autentisering** senare efter att appen har skapats.

1. Välj **Registrera för** att skapa appen.

1. På appens **översiktssida** letar du reda på värdet **program (klient) och** registrerar det för senare. Du behöver det här värdet för att konfigurera programmet senare i det här projektet.

1. I listan över sidor för appen väljer du **Autentisering**.
    - I avsnittet **Omdirigera URI:er** väljer du **Webben** i kombinationsrutan och anger följande omdirigerings-URI:`http://localhost:3000/auth/openid/return`
    - I avsnittet **Avancerade inställningar** ställer du in **Utloggnings-URL** på `http://localhost:3000`.
    - I avsnittet **Avancerade inställningar > implicita bidrag** kontrollerar du **ID-token** eftersom det här exemplet kräver att [det implicita bidragsflödet](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) ska aktiveras för att logga in användaren.

1. Välj **Spara**.

1. Välj **Ny klienthemlighet**i avsnittet **Klienthemligheter** på sidan **Certifikat & hemligheter.**
    - Ange en nyckelbeskrivning (till exempel apphemlighet).
    - Välj en nyckellängd på antingen **Om 1 år, I 2 år** eller **Aldrig upphör .**
    - När du klickar på knappen **Lägg till** visas nyckelvärdet. Kopiera nyckelvärdet och spara det på en säker plats.

    Du behöver den här nyckeln senare för att konfigurera programmet. Det här nyckelvärdet visas inte igen eller kan hämtas på något annat sätt, så registrera det så snart det är synligt från Azure-portalen.

## <a name="download-the-sample-application-and-modules"></a>Ladda ner exempelprogrammet och modulerna

Klona sedan provrepon och installera NPM-modulerna.

Från skalet eller kommandoraden:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

eller

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Kör kommandot från projektrotkatalogen:

`$ npm install`  

## <a name="configure-the-application"></a>Konfigurera programmet

Ange parametrarna `exports.creds` i config.js enligt instruktionerna.

* Uppdatera `<tenant_name>` `exports.identityMetadata` in med Azure AD-klientnamnet för formatet \*.onmicrosoft.com.
* Uppdatera `exports.clientID` med det program-ID som anges från appregistreringen.
* Uppdatera `exports.clientSecret` med programhemligheten som registrerats från appregistreringen.
* Uppdatera `exports.redirectUrl` med redirect-URI:n som registrerats från appregistreringen.

**Valfri konfiguration för produktionsappar:**

* Uppdatera `exports.destroySessionUrl` i config.js, om du `post_logout_redirect_uri`vill använda en annan .

* Ställ `exports.useMongoDBSessionStore` in config.js till true, om du vill använda [mongoDB](https://www.mongodb.com) eller andra [kompatibla sessionsbutiker](https://github.com/expressjs/session#compatible-session-stores).
Standardsessionsarkivet i det `express-session`här exemplet är . Standardsessionsarkivet är inte lämpligt för produktion.

* Uppdatera `exports.databaseUri`, om du vill använda mongoDB session store och en annan databas URI.

* Uppdatera `exports.mongoDBSessionMaxAge`. Här kan du ange hur länge du vill behålla en session i mongoDB. Enheten är sekund(er).

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Starta mongoDB-tjänsten. Om du använder mongoDB session store i denna app, måste du [installera mongoDB](http://www.mongodb.org/) och starta tjänsten först. Om du använder standardsessionsarkivet kan du hoppa över det här steget.

Kör appen med följande kommando från kommandoraden.

```
$ node app.js
```

**Är servern utgång svårt att förstå?:** Vi `bunyan` använder för att logga in i det här exemplet. Konsolen kommer inte att göra mycket mening för dig om du inte också installera bunyan och köra servern som ovan men rör den genom bunyan binära:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Du är klar!

Du kommer att ha en `http://localhost:3000`server som körs på .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om webbappscenariot som Microsofts identitetsplattform stöder:
> [!div class="nextstepaction"]
> [Webbapp som signerar i användarscenario](scenario-web-app-sign-user-overview.md)

---
title: Använd Microsoft Authentication Library (MSAL) i nationella moln – Microsoft identity-plattformen
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för apputvecklare att hämta token för att kunna anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft-APIS, andra leverantörers webb-API: er eller dina egna webb-API. MSAL har stöd för flera arkitekturer för program och plattformar.'
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075797"
---
# <a name="use-msal-in-national-cloud-environment"></a>Använda MSAL i nationell molnmiljö

[Nationella moln](authentication-national-cloud.md) är fysiskt isolerat instanser av Azure. Dessa områden av Azure är utformade för att se till att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Förutom Microsoft worldwide cloud kan Microsoft Authentication Library (MSAL) också apputvecklare i nationella moln att hämta token för att autentisera och anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph eller andra Microsoft-APIS.

Azure Active Directory (Azure AD) har distribuerats i följande nationella moln, inklusive globala molnet:  

- Azure för amerikanska myndigheter
- Azure Kina 21Vianet
- Azure Tyskland

Den här guiden visar hur du registrerar i arbets-och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API i [Microsoft-molnet för amerikanska myndigheter](https://azure.microsoft.com/global-infrastructure/government/) miljö.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du uppfyller dessa krav innan du börjar.

### <a name="choose-the-appropriate-identities"></a>Välj lämplig identiteter

[Azure Government](https://docs.microsoft.com/azure/azure-government/) program kan använda Azure AD-Government-identiteter, samt offentliga Azure AD-identiteter för att autentisera användare. Eftersom du kan använda någon av dessa identiteter, måste du förstå och bestämma vilka utfärdarslutpunkten som du ska välja för ditt scenario:

- Azure AD Public: Vanligaste om din organisation redan har en offentlig Azure AD-klient till support för Office 365 (offentlig eller GCC) eller ett annat program.
- Azure AD Government: Vanligaste om din organisation redan har en Azure Government för AD-klient till support för Office 365 (GCC hög eller DoD) eller skapar en ny klient i Azure AD-Government.

När du valt, är särskilt beaktande där du genomför appregistreringen. Om du väljer offentlig Azure AD-identiteter för Azure Government-program, måste du registrera programmet i din offentlig Azure AD-klient.

### <a name="get-an-azure-government-subscription"></a>Skaffa en Azure Government-prenumeration

- Om du vill skaffa en Azure Government-prenumeration, se [hantera och ansluta till din prenumeration i Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Om du inte har en Azure Government-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

1. Logga in på [Azure-portalen](https://portal.azure.us/) för att registrera ett program.
    1. Du hittar Azure portal-slutpunkter för andra nationella moln [App registrering-slutpunkter](authentication-national-cloud.md#app-registration-endpoints)

1. Om ditt konto får du tillgång till fler än en klient, Välj ditt konto i det övre högra hörnet och ange din portal-session med önskade Azure AD-klient.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://aka.ms/ra/ff) sidan.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **stöds kontotyper**väljer **konton i alla organisationskatalog**.
1. Under den **omdirigerings-URI** väljer den **Web** plattform och ange värdet till programmets URL baserat på din webbserver. Se avsnitten nedan för instruktioner om hur du anger och hämtar omdirigerings-URL i Visual Studio och nod.
1. När det är klart väljer du **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. Den här självstudien kräver den [flöde beviljat med Implicit](v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra navigeringsfönstret för det registrerade programmet väljer du **Autentisering**.
1. I **Avancerade inställningar** går du till **Implicit beviljande** och aktiverar kryssrutorna **ID-token** och **Åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen behöver logga in användare och anropa ett API.
1. Välj **Spara**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Steg 2:  Konfigurera din webbserver eller projekt

- [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) för en lokal webbserver, till exempel noden.

  eller

- [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Och sedan gå vidare till den [”konfigurera din JavaScript SPA'](#step-4-configure-your-javascript-spa) konfigurera kodexemplet innan det körs.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Steg 3: Använd Microsoft Authentication Library (MSAL) för att logga in användaren

Följ stegen i [Javascript självstudien](tutorial-v2-javascript-spa.md#create-your-project) att skapa projektet och integrera med Microsoft Authentication Library (MSAL) att logga in användaren.

### <a name="step-4-configure-your-javascript-spa"></a>Steg 4: Konfigurera din JavaScript SPA

I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Där:

- `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
- `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
    - Om ditt program stöder **konton i den här organisationskatalog**, Ersätt detta värde med den **klient-ID** eller **klientnamn** (t.ex. Contoso.microsoft.com)
    - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
    -  Autentiseringsslutpunkter för nationella moln finns i [autentiseringsslutpunkter för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Scenarier med personliga Microsoft-konton (MSA) stöds inte i nationella moln.
  
-   `graphEndpoint` -är Microsoft Graph-slutpunkt för Microsoft-molnet för amerikanska myndigheter.
    -  Du hittar Microsoft Graph-slutpunkter för nationella moln [Microsoft Graph-slutpunkter i nationella moln](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

MSAL .NET kan du logga in användare, hämta token och anropa Microsoft Graph API i nationella moln.

Följande självstudie visar hur du skapar en .NET Core 2.2 MVC-webbapp som använder OpenID Connect för att logga in användare med sina ”arbete och skola” konton i organisationen som hör till nationella moln.

- Logga in användare och hämta token genom att följa [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- För att anropa Microsoft Graph API, Följ [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
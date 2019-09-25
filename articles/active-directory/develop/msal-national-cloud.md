---
title: Använd Microsoft Authentication Library (MSAL) i nationella moln – Microsoft Identity Platform
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för programutvecklare att förvärva tokens för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, partner webb-API: er eller ditt eget webb-API. MSAL stöder flera program arkitekturer och plattformar.'
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
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268568"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Använda MSAL i en nationell moln miljö

[Nationella moln](authentication-national-cloud.md) är fysiskt isolerade instanser av Azure. Dessa regioner i Azure bidrar till att se till att kraven på data placering, suveränitet och efterlevnad är uppfyllda inom geografiska gränser.

Förutom Microsofts världs omspännande moln, gör Microsoft Authentication Library (MSAL) det möjligt för programutvecklare i nationella moln att förvärva tokens för att autentisera och anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph eller andra Microsoft API: er.

Med det globala molnet distribueras Azure Active Directory (Azure AD) i följande nationella moln:  

- Azure Government
- Azure Kina 21Vianet
- Azure Tyskland

Den här guiden visar hur du loggar in på arbets-och skol konton, hämtar en åtkomsttoken och anropar Microsoft Graph API i [Azure Government moln](https://azure.microsoft.com/global-infrastructure/government/) miljö.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du uppfyller dessa krav.

### <a name="choose-the-appropriate-identities"></a>Välj lämpliga identiteter

[Azure Government](https://docs.microsoft.com/azure/azure-government/) program kan använda Azure AD myndighets identiteter och offentliga Azure AD-identiteter för att autentisera användare. Eftersom du kan använda någon av dessa identiteter måste du bestämma vilken auktoritets slut punkt du ska välja för ditt scenario:

- Azure AD Public: Används ofta om din organisation redan har en offentlig Azure AD-klient som stöder Office 365 (offentlig eller GCC) eller något annat program.
- Azure AD-myndigheter: Används ofta om din organisation redan har en Azure AD myndighets-klient som stöder Office 365 (GCC High eller DoD) eller skapar en ny klient i Azure AD-myndigheter.

När du har bestämt dig är det viktigt att du utför registreringen av appen. Om du väljer Azure AD-offentliga identiteter för ditt Azure Government-program måste du registrera programmet i din offentliga Azure AD-klient.

### <a name="get-an-azure-government-subscription"></a>Hämta en Azure Government-prenumeration

Information om hur du hämtar en Azure Government-prenumeration finns [i hantera och ansluta till din prenumeration i Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Om du inte har en Azure Government prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

1. Logga in på [Azure Portal](https://portal.azure.us/).
    
   Information om hur du hittar Azure Portal slut punkter för andra nationella moln finns i [slut punkter för registrering av appar](authentication-national-cloud.md#app-registration-endpoints).

1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
1. Gå till sidan [Appregistreringar](https://aka.ms/ra/ff) på Microsoft Identity Platform för utvecklare.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **konto typer som stöds**väljer du **konton i valfri organisations katalog**.
1. I avsnittet **omdirigerings-URI** väljer du **webb** plattform och anger värdet till programmets URL-adress baserat på din webb server. I nästa avsnitt finns anvisningar om hur du ställer in och hämtar omdirigerings-URL: en i Visual Studio och Node.
1. Välj **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. I den här självstudien krävs att du aktiverar det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md). I det vänstra fönstret i det registrerade programmet väljer du **autentisering**.
1. I **Avancerade inställningar**, under **implicit tilldelning**, markerar du kryss rutorna **ID-token** och **åtkomst-token** . ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Steg 2:  Konfigurera din webb server eller ditt projekt

- [Hämta projektfiler](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) för en lokal webb server, till exempel Node.

  eller

- [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Gå sedan vidare till [Konfigurera ditt Java Script-Spa](#step-4-configure-your-javascript-spa) för att konfigurera kod exemplet innan du kör det.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Steg 3: Använd Microsoft Authentication Library för att logga in användaren

Följ stegen i [guiden JavaScript-självstudie](tutorial-v2-javascript-spa.md#create-your-project) för att skapa projektet och integrera med MSAL för att logga in användaren.

### <a name="step-4-configure-your-javascript-spa"></a>Steg 4: Konfigurera ditt Java Script SPA

Lägg till program registrerings informationen i filensomskapadesunderprojektinstallationen.`index.html` Lägg till följande kod högst upp `<script></script>` i taggarna i bröd texten `index.html` i filen:

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

I den koden:

- `Enter_the_Application_Id_here`är **programmets (klient) ID-** värdet för det program som du har registrerat.
- `Enter_the_Tenant_Info_Here`är inställt på något av följande alternativ:
    - Om ditt program har stöd **för konton i den här organisations katalogen**ersätter du värdet med klient-ID eller klient namn (till exempel contoso.Microsoft.com).
    - Om ditt program har stöd **för konton i en organisations katalog**ersätter du värdet `organizations`med.
    
    För att hitta autentiserings slut punkter för alla nationella moln, se [Azure AD-autentiseringens slut punkter](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Personliga Microsoft-konton stöds inte i nationella moln.
  
- `graphEndpoint`är Microsoft Graph slut punkten för Microsoft-molnet för amerikanska myndigheter.

   Information om hur du hittar Microsoft Graph slut punkter för alla nationella moln finns i [Microsoft Graph slut punkter i nationella moln](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Du kan använda MSAL.NET för att logga in användare, Hämta token och anropa Microsoft Graph API i nationella moln.

Följande självstudier visar hur du skapar en .NET Core 2,2 MVC-webbapp. Appen använder OpenID Connect för att logga in användare med ett arbets-och skol konto i en organisation som tillhör ett nationellt moln.

- Följ [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)om du vill logga in användare och hämta tokens.
- Följ [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)för att anropa Microsoft Graph API: et.

## <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

MSAL för iOS och macOS kan användas för att hämta tokens i nationella moln, men det krävs ytterligare konfiguration när du skapar `MSALPublicClientApplication`.

Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

Mål-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Införliva

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)

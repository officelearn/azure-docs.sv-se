---
title: Använd MSAL i en nationell molnapp | Azure
titleSuffix: Microsoft identity platform
description: Med MSAL (Microsoft Authentication Library) kan programutvecklare hämta token för att anropa skyddade webb-API:er. Dessa webb-API:er kan vara Microsoft Graph, andra Microsoft-API:er, partnerwebb-API:er eller ditt eget webb-API. MSAL stöder flera programarkitekturer och plattformar.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533997"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Använda MSAL i en nationell molnmiljö

[Nationella moln](authentication-national-cloud.md), även känd som Suveräna moln, är fysiskt isolerade instanser av Azure. Dessa regioner i Azure hjälper till att se till att datahemvist, suveränitet och efterlevnadskrav uppfylls inom geografiska gränser.

Förutom Microsofts världsomspännande moln gör Microsoft Authentication Library (MSAL) det möjligt för programutvecklare i nationella moln att skaffa token för att autentisera och anropa skyddade webb-API:er. Dessa webb-API:er kan vara Microsoft Graph eller andra Microsoft-API:er.

Inklusive det globala molnet distribueras Azure Active Directory (Azure AD) i följande nationella moln:  

- Azure Government
- Azure Kina 21Vianet
- Azure Tyskland

Den här guiden visar hur du loggar in på arbets- och skolkonton, hämtar en åtkomsttoken och anropar Microsoft Graph API i Molnmiljön för [Azure Government.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du uppfyller dessa förutsättningar.

### <a name="choose-the-appropriate-identities"></a>Välj lämpliga identiteter

[Azure Government-program](https://docs.microsoft.com/azure/azure-government/) kan använda Azure AD Government-identiteter och Azure AD Public-identiteter för att autentisera användare. Eftersom du kan använda någon av dessa identiteter måste du bestämma vilken myndighetsslutpunkt du ska välja för ditt scenario:

- Azure AD Public: Används ofta om din organisation redan har en Azure AD Public-klient för att stödja Office 365 (Public eller GCC) eller ett annat program.
- Azure AD-myndighet: Används ofta om din organisation redan har en Azure AD-klientorganisation för att stödja Office 365 (GCC High eller DoD) eller skapar en ny klientorganisation i Azure AD-myndigheter.

När du har bestämt dig är en särskild faktor var du utför din appregistrering. Om du väljer Azure AD Public-identiteter för ditt Azure Government-program måste du registrera programmet i din Azure AD Public-klientorganisation.

### <a name="get-an-azure-government-subscription"></a>Skaffa en Azure Government-prenumeration

Information om hur du skaffar en Azure Government-prenumeration finns i [Hantera och ansluta till din prenumeration i Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Om du inte har en Azure Government-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

Om du vill ha information om hur du använder ett nationellt moln med ett visst programmeringsspråk väljer du fliken som matchar ditt språk:

## <a name="net"></a>[.NET](#tab/donet)

Du kan använda MSAL.NET för att logga in användare, hämta token och anropa Microsoft Graph API i nationella moln.

Följande självstudier visar hur du skapar en .NET Core 2.2 MVC Web app. Appen använder OpenID Connect för att logga in användare med ett arbets- och skolkonto i en organisation som tillhör ett nationellt moln.

- Om du vill logga in användare och hämta token följer du den här självstudien: [Skapa en ASP.NET Core Web App-inloggade användare i suveräna moln med Microsofts identitetsplattform](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Så här anropar du Microsoft Graph API: [Använda Microsoft identity-plattformen för att anropa Microsoft Graph API från en ASP.NET Core 2.x-webbapp, på uppdrag av en användare som loggar in med hjälp av sitt arbets- och skolkonto i Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Så här aktiverar du din MSAL.js-applikation för suveräna moln:

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

1. Logga in på [Azure-portalen](https://portal.azure.us/).

   Information om hur du hittar Azure-portalslutpunkter för andra nationella moln finns i slutpunkter för [appregistrering](authentication-national-cloud.md#app-registration-endpoints).

1. Om ditt konto ger dig åtkomst till mer än en klient väljer du ditt konto i det övre högra hörnet och anger din portalsession till önskad Azure AD-klientorganisation.
1. Gå till sidan Appregistreringar på Microsofts [identitetsplattform](https://aka.ms/ra/ff) för utvecklare.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds**väljer du Konton i valfri **organisationskatalog**.
1. I avsnittet **Omdirigera URI** väljer du **webbplattformen** och anger värdet till programmets URL baserat på webbservern. Se nästa avsnitt för instruktioner om hur du ställer in och hämtar url:en för omdirigering i Visual Studio och Node.
1. Välj **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. Den här självstudien kräver att du aktiverar det [implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md). Välj **Autentisering**i den vänstra rutan i det registrerade programmet .
1. I **Avancerade inställningar**under Implicit **beviljande**markerar du **kryssrutorna ID-token** och **Access-token.** ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Steg 2: Konfigurera webbservern eller webbprojektet

- [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) för en lokal webbserver, till exempel Nod.

  eller

- [Ladda ner Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Hoppa sedan till [Konfigurera ditt JavaScript SPA](#step-4-configure-your-javascript-spa) för att konfigurera kodexemplet innan du kör det.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Steg 3: Använda Microsoft Authentication Library för att logga in användaren

Följ stegen i [JavaScript-självstudien](tutorial-v2-javascript-spa.md#create-your-project) för att skapa ditt projekt och integrera med MSAL för att logga in användaren.

### <a name="step-4-configure-your-javascript-spa"></a>Steg 4: Konfigurera ditt JavaScript SPA

Lägg `index.html` till programregistreringsinformationen i filen som skapades under projektinställningarna. Lägg till följande kod högst `<script></script>` upp i taggarna i filens `index.html` brödtext:

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

- `Enter_the_Application_Id_here`är **värdet för program -(klient)ID** för det program som du registrerade.
- `Enter_the_Tenant_Info_Here`är inställd på något av följande alternativ:
    - Om ditt program stöder **konton i den här organisationskatalogen**ersätter du det här värdet med klient-ID: n eller klientnamnet (till exempel contoso.microsoft.com).
    - Om programmet stöder **konton i en organisationskatalog** `organizations`ersätter du det här värdet med .

    Information om hur du hittar autentiseringsslutpunkter för alla nationella moln finns i slutpunkter för [Azure AD-autentisering](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Personliga Microsoft-konton stöds inte i nationella moln.

- `graphEndpoint`är Microsoft Graph-slutpunkten för Microsoft-molnet för amerikanska myndigheter.

   Information om hur du hittar Microsoft Graph-slutpunkter för alla nationella moln finns [i Microsoft Graph-slutpunkter i nationella moln](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Så här aktiverar du ditt MSAL Python-program för suveräna moln:

- Registrera ditt program i en viss portal, beroende på molnet. Mer information om hur du väljer portalen finns slutpunkter för [appregistrering](authentication-national-cloud.md#app-registration-endpoints)
- Använd något av [exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) från repo med några ändringar i konfigurationen, beroende på molnet, som nämns härnäst.
- Använd en specifik behörighet, beroende på vilket moln du registrerade programmet i. Mer information om myndigheter för olika moln finns i Slutpunkter för [Azure AD-autentisering](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Här är ett exempel myndighet:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- För att anropa Microsoft graph krävs en specifik graph-url som beror på vilket moln du använder. Information om hur du hittar Microsoft Graph-slutpunkter för alla nationella moln finns i [microsoft graph- och graph explorer-tjänstrotslutpunkter](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Här är ett exempel på en diagramslutpunkt, med omfattning:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Så här aktiverar du din MSAL för Java-program för suveräna moln:

- Registrera ditt program i en viss portal, beroende på molnet. Mer information om hur du väljer portalen finns slutpunkter för [appregistrering](authentication-national-cloud.md#app-registration-endpoints)
- Använd något av [exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) från repo med några ändringar i konfigurationen, beroende på molnet, som nämns härnäst.
- Använd en specifik behörighet, beroende på vilket moln du registrerade programmet i. Mer information om myndigheter för olika moln finns i Slutpunkter för [Azure AD-autentisering](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Här är ett exempel myndighet:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- För att anropa Microsoft graph krävs en specifik graph-url som beror på vilket moln du använder. Information om hur du hittar Microsoft Graph-slutpunkter för alla nationella moln finns i [microsoft graph- och graph explorer-tjänstrotslutpunkter](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Här är ett exempel på en diagramslutpunkt, med omfattning:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL för iOS och macOS kan användas för att hämta token i `MSALPublicClientApplication`nationella moln, men det kräver ytterligare konfiguration när du skapar .

Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här us government) kan du skriva:

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

## <a name="swift"></a>[Swift](#tab/swift)

MSAL för iOS och macOS kan användas för att hämta token i `MSALPublicClientApplication`nationella moln, men det kräver ytterligare konfiguration när du skapar .

Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här us government) kan du skriva:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Autentisering i nationella moln](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Tyskland](https://docs.microsoft.com/azure/germany/)

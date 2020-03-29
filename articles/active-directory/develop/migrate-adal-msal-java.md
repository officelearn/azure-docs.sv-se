---
title: ADAL till MSAL migration guide (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar java-appen för Azure Active Directory Authentication Library (ADAL) till Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2d85a3a5d876d731655bb30f7d37a6f42fa5c220
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696732"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL till MSAL-migreringsguide för Java

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder Azure Active Directory Authentication Library (ADAL) för att använda Microsoft Authentication Library (MSAL).

Både Microsoft Authentication Library for Java (MSAL4J) och Azure AD Authentication Library for Java (ADAL4J) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Hittills har de flesta utvecklare arbetat med Azure AD för utvecklarplattform (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton) genom att begära token med Azure AD Authentication Library (ADAL).

MSAL erbjuder följande fördelar:

- Eftersom den använder den nyare slutpunkten för Microsoft Identity Platform kan du autentisera en bredare uppsättning Microsoft-identiteter som Azure AD-identiteter, Microsoft-konton och sociala och lokala konton via Azure AD Business to Consumer (B2C).
- Användarna får den bästa upplevelsen för enkel inloggning.
- Ditt program kan aktivera inkrementellt medgivande och det är enklare att stödja villkorlig åtkomst.

MSAL för Java är det auth-bibliotek som vi rekommenderar att du använder med Microsofts identitetsplattform. Inga nya funktioner kommer att implementeras på ADAL4J. Alla ansträngningar framöver är inriktade på att förbättra MSAL.

## <a name="differences"></a>Skillnader

Om du har arbetat med Slutpunkten för Azure AD för utvecklare (v1.0) och ADAL4J) kanske du vill läsa [Vad är annorlunda med slutpunkten för Microsoft Identity Platform (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Scope inte resurser

ADAL4J hämtar token för resurser medan MSAL för Java förvärvar token för scope. Ett antal MSAL för Java-klasser kräver en scopeparameter. Den här parametern är en lista över strängar som deklarerar önskade behörigheter och resurser som begärs. Se [Microsoft Graphs scope](https://docs.microsoft.com/graph/permissions-reference) för att se exempelomfattningar.

## <a name="core-classes"></a>Kärnklasser

I ADAL4J `AuthenticationContext` representerar klassen din anslutning till Security Token Service (STS) eller auktoriseringsservern via en myndighet. MSAL för Java är dock utformat kring klientprogram. Det ger två `PublicClientApplication` separata `ConfidentialClientApplication` klasser: och för att representera klientprogram.  Det senare, `ConfidentialClientApplication`representerar ett program som är utformat för att på ett säkert sätt underhålla en hemlighet, till exempel en programidentifierare för en daemonapp.

Följande tabell visar hur ADAL4J fungerar mappa till de nya MSAL för Java-funktioner:

| ADAL4J-metoden| MSAL4J-metod|
|------|-------|
|acquireToken(Strängresurs, ClientCredential-autentiseringsuppgifter, Adringning av AuthenticationCallback) | acquireToken(ClientCredentialParameters)|
|acquireToken(Strängresurs, ClientAssertion-påstående, Adropback-motringning av AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(Strängresurs, AsymmetricKeyCredential-autentiseringsuppgifter, Callback-motringning av AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String username, String password, AuthenticationCallback callback)| acquireToken (UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String username, String password=null, AuthenticationCallback callback)|acquireToken (IntegratedWindowsAuthenticationParameters)|
|acquireToken(Strängresurs, UserAssertion userAssertion, ClientCredential-autentiseringsuppgifter, AuthenticationCallback callback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() och förvärvaTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount i stället för IUser

ADAL4J manipulerade användare. Även om en användare representerar en enda mänsklig agent eller programvaruagent kan den ha ett eller flera konton i Microsofts identitetssystem. En användare kan till exempel ha flera personliga Azure AD-, Azure AD B2C- eller Microsoft-konton.

MSAL för Java definierar begreppet `IAccount` konto via gränssnittet. Detta är en brytande förändring från ADAL4J, men det är en bra en eftersom det fångar det faktum att samma användare kan ha flera konton, och kanske till och med i olika Azure AD-kataloger. MSAL för Java ger bättre information i gästscenarier eftersom hemkontoinformation tillhandahålls.

## <a name="cache-persistence"></a>Cache persistens

ADAL4J hade inte stöd för tokencache.
MSAL för Java lägger till en [tokencache](msal-acquire-cache-tokens.md) för att förenkla hanteringen av tokenlivstider genom att automatiskt uppdatera utgångna token när det är möjligt och förhindra onödiga uppmaningar för användaren att tillhandahålla autentiseringsuppgifter när det är möjligt.

## <a name="common-authority"></a>Gemensam myndighet

I v1.0, om `https://login.microsoftonline.com/common` du använder myndigheten, kan användare logga in med alla Azure Active Directory (AAD) konto (för alla organisationer).

Om du `https://login.microsoftonline.com/common` använder myndigheten i v2.0 kan användare logga in med valfri AAD-organisation eller till och med ett personligt Microsoft-konto (MSA). I MSAL för Java, om du vill begränsa inloggning till ett `https://login.microsoftonline.com/organizations` AAD-konto, måste du använda myndigheten (vilket är samma beteende som med ADAL4J). Om du vill ange `authority` en behörighet anger du parametern i metoden `PublicClientApplication` [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) när du skapar klassen.

## <a name="v10-and-v20-tokens"></a>v1.0- och v2.0-tokens

V1.0-slutpunkten (används av ADAL) avger endast v1.0-token.

V2.0-slutpunkten (används av MSAL) kan avge v1.0- och v2.0-token. En egenskap för webb-API:ets programmanifest gör det möjligt för utvecklare att välja vilken version av token som ska accepteras. Se `accessTokenAcceptedVersion` i referensdokumentationen för [programmanifestet.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Mer information om v1.0- och v2.0-token finns i [Azure Active Directory-åtkomsttoken .](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)

## <a name="adal-to-msal-migration"></a>ADAL till MSAL-migrering

I ADAL4J exponerades uppdateringstokens – vilket gjorde det möjligt för utvecklare att cachelagra dem. De skulle `AcquireTokenByRefreshToken()` sedan använda för att aktivera lösningar som att implementera tidskrävande tjänster som uppdaterar instrumentpaneler för användarens räkning när användaren inte längre är ansluten.

MSAL för Java exponerar inte uppdateringstoken av säkerhetsskäl. I stället hanterar MSAL uppfriskande tokens åt dig.

MSAL för Java har ett API som gör att du kan migrera uppdateringstoken som du har förvärvat med ADAL4j till ClientApplication: [acquireToken(RefreshTokenParameters).](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) Med den här metoden kan du ange den tidigare använda uppdateringstoken tillsammans med eventuella scope (resurser) som du önskar. Uppdateringstoken kommer att bytas ut mot en ny och cachelagras för användning av ditt program.

Följande kodavsnitt visar en del migreringskod i ett konfidentiellt klientprogram:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Returnerar `IAuthenticationResult` en åtkomsttoken och ID-token, medan den nya uppdateringstoken lagras i cacheminnet. Ansökan kommer nu också att innehålla ett IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Om du vill använda de token som nu finns i cacheminnet anropar du:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```

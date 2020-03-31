---
title: Konfiguration av klientprogram (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om konfigurationsalternativ för offentliga klientprogram och konfidentiella klientprogram med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0262d22ae00456ce06cb8efbf995f1a093b20043
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262898"
---
# <a name="application-configuration-options"></a>Alternativ för programkonfiguration

I koden initierar du ett nytt offentligt eller konfidentiellt klientprogram (eller användaragent för MSAL.js) för att autentisera och hämta token. Du kan ange ett antal konfigurationsalternativ när du initierar klientappen i Microsoft Authentication Library (MSAL). Dessa alternativ kan delas in i två grupper:

- Registreringsalternativ, inklusive:
    - [Behörighet](#authority) (består av identitetsproviderinstansen och [inloggningsmålgruppen](#application-audience) för appen, och eventuellt klient-ID). [instance](#cloud-instance)
    - [Klient-ID](#client-id).
    - [Omdirigera URI](#redirect-uri).
    - [Klienthemlighet](#client-secret) (för konfidentiella klientprogram).
- [Loggningsalternativ](#logging), inklusive loggnivå, kontroll av personuppgifter och namnet på komponenten med hjälp av biblioteket.

## <a name="authority"></a>Myndighet

Behörigheten är en URL som anger en katalog som MSAL kan begära token från. De gemensamma myndigheterna är:

- https\://login.microsoftonline.com/\<klientorganisation\>/, där &lt;klienten&gt; är klient-ID för Azure Active Directory (Azure AD) eller en domän som är associerad med den här Azure AD-klienten. Används endast för att logga in användare av en viss organisation.
- https\://login.microsoftonline.com/common/. Används för att logga in användare med arbets- och skolkonton eller personliga Microsoft-konton.
- https\://login.microsoftonline.com/organizations/. Används för att logga in användare med arbets- och skolkonton.
- https\://login.microsoftonline.com/consumers/. Används för att logga in användare med endast personliga Microsoft-konton (tidigare kallade Windows Live ID-konton).

Behörighetsinställningen måste vara förenligt med vad som deklareras i programregistreringsportalen.

Auktoritets-URL:en består av instansen och målgruppen.

Myndigheten kan vara:
- En Azure AD-molnmyndighet.
- En Azure AD B2C-behörighet. Se [B2C-detaljer](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- En AD FS-myndighet (Active Directory Federation Services). Se [AD FS-stöd](https://aka.ms/msal-net-adfs-support).

Azure AD-molnmyndigheterna har två delar:
- Instansen *instance* för identitetsprovider
- *Inloggningspubliken* för appen

Instansen och målgruppen kan sammanfogas och anges som auktoritets-URL. I versioner av MSAL.NET tidigare än MSAL 3. *x*var du tvungen att komponera myndigheten själv, baserat på molnet du ville rikta in dig på och inloggningsmålet.  Det här diagrammet visar hur auktoritets-URL:en består:

![Hur auktoritetsadressen är sammansatt](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Molninstans

*Instansen* används för att ange om din app signerar användare från Det offentliga Azure-molnet eller från nationella moln. Med MSAL i koden kan du ange Azure-molninstansen med hjälp av en uppräkning `Instance` eller genom att skicka URL:en till den [nationella molninstansen](authentication-national-cloud.md#azure-ad-authentication-endpoints) som medlem (om du känner till den).

MSAL.NET kommer att utlösa ett uttryckligt undantag om båda `Instance` och `AzureCloudInstance` anges.

Om du inte anger en instans kommer din app att inrikta sig `https://login.onmicrosoftonline.com`på Azures offentliga molninstans (instansen av URL).

## <a name="application-audience"></a>Programmålgrupp

Inloggningspubliken beror på appens affärsbehov:
- Om du är en LOB-utvecklare (line of business) skapar du förmodligen ett program med en enda klientorganisation som bara används i organisationen. I så fall måste du ange organisationen, antingen av dess klient-ID (ID för din Azure AD-instans) eller av ett domännamn som är associerat med Azure AD-instansen.
- Om du är en ISV kanske du vill logga in användare med deras arbets- och skolkonton i någon organisation eller i vissa organisationer (app för flera signatureer). Men du kanske också vill att användarna ska logga in med sina personliga Microsoft-konton.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Så här anger du målgruppen i koden/konfigurationen

Med MSAL i koden anger du målgruppen med något av följande värden:
- Azure AD-myndighetspublikuppräkning
- Klient-ID, som kan vara:
  - Ett GUID (ID för din Azure AD-instans) för program med en klientorganisation
  - Ett domännamn som är associerat med din Azure AD-instans (även för program med en klientorganisation)
- En av dessa platshållare som klient-ID i stället för Azure AD-myndighetspublikens uppräkning:
    - `organizations`för en ansökan med flera trogna
    - `consumers`för att logga in användare endast med sina personliga konton
    - `common`för att logga in användare med sina arbets- och skolkonton eller sina personliga Microsoft-konton

MSAL will throw a meaningful exception if you specify both the Azure AD authority audience and the tenant ID.

Om du inte anger en målgrupp riktar din app in sig på Azure AD och personliga Microsoft-konton som målgrupp. (Det vill än, det `common` kommer att bete sig som om det angavs.)

### <a name="effective-audience"></a>Effektiv publik

Den effektiva målgruppen för ditt program är den minsta (om det finns en skärningspunkt) för målgruppen som du anger i appen och målgruppen som anges i appregistreringen. I själva verket kan du med [appregistreringsupplevelsen](https://aka.ms/appregistrations) ange målgruppen (de kontotyper som stöds) för appen. Mer information finns i [Snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md).

För närvarande är det enda sättet att få en app att logga in användare med endast personliga Microsoft-konton att konfigurera båda dessa inställningar:
- Ställ in appregistreringspubliken på `Work and school accounts and personal accounts`.
- Ange målgruppen i koden/konfigurationen till `AadAuthorityAudience.PersonalMicrosoftAccount` (eller `TenantID` ="konsumenter").

## <a name="client-id"></a>Klientorganisations-ID

Klient-ID:t är det unika program-ID som azure AD tilldelat din app när appen registrerades.

## <a name="redirect-uri"></a>Omdirigerings-URI

Omdirigerings-URI är den URI som identitetsprovidern skickar tillbaka säkerhetstoken till.

### <a name="redirect-uri-for-public-client-apps"></a>Omdirigera URI för offentliga klientappar

Om du är en offentlig klientapputvecklare som använder MSAL:
- Du vill använda `.WithDefaultRedirectUri()` i stationära eller UWP-program (MSAL.NET 4,1 +). Den här metoden anger den offentliga klientprogrammets omdirigeringsuri-egenskap till standardredokomrederingen uri för offentliga klientprogram. 

  Plattform  | Omdirigerings-URI  
  ---------  | --------------
  Skrivbordsapp (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | värdet `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`på . Detta gör det möjligt för SSO med webbläsaren genom att ställa in värdet på resultatet av WebAuthenticationBroker.GetCurrentApplicationCallbackUri() som du måste registrera
  .NET Core | `https://localhost`. Detta gör det möjligt för användaren att använda systembläddraren för interaktiv autentisering eftersom .NET Core inte har ett användargränssnitt för den inbäddade webbvyn för tillfället.

- Du behöver inte lägga till en omdirigera URI om du bygger en Xamarin Android och iOS-program som inte `msal{ClientId}://auth` stöder mäklare (omdirigera URI ställs automatiskt in för Xamarin Android och iOS

- Du måste konfigurera omdirigerings-URI:en i [appregistreringar:](https://aka.ms/appregistrations)

   ![Omdirigera URI i appregistreringar](media/msal-client-application-configuration/redirect-uri.png)

Du kan åsidosätta omdirigerings-URI:n med hjälp av egenskapen `RedirectUri` (till exempel om du använder mäklare). Här är några exempel på omdirigera URI:er för det scenariot:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

Mer information om iOS finns i [Migrera iOS-program som använder Microsoft Authenticator från ADAL.NET till MSAL.NET](msal-net-migration-ios-broker.md) och Utnyttja [mäklaren på iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Mer Information om Android finns [i Förmedlad autentisering i Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Omdirigera URI för konfidentiella klientappar

För webbappar är omdirigerings-URI (eller svar URI) den URI som Azure AD använder för att skicka tillbaka token till programmet. Den här URI:n kan vara webbadressen till webbappen/webb-API:et om den konfidentiella appen är en av dessa. Omdirigerings-URI:n måste registreras i appregistreringen. Den här registreringen är särskilt viktig när du distribuerar en app som du först har testat lokalt. Du måste sedan lägga till svars-URL:en för den distribuerade appen i programregistreringsportalen.

För daemon-appar behöver du inte ange en uri för omdirigering.

## <a name="client-secret"></a>Klienthemlighet

Det här alternativet anger klienthemligheten för den konfidentiella klientappen. Den här hemligheten (applösenord) tillhandahålls av programregistreringsportalen eller tillhandahålls till Azure AD under appregistrering med PowerShell AzureAD, PowerShell AzureRM eller Azure CLI.

## <a name="logging"></a>Loggning

De andra konfigurationsalternativen möjliggör loggning och felsökning. Mer information om hur du använder dem finns i artikeln [Loggning.](msal-logging.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md).
Lär dig mer om [att instansiera klientprogram med hjälp av MSAL.js](msal-js-initializing-client-applications.md).

---
title: Ny registrering för Azure-portalappar
titleSuffix: Microsoft identity platform
description: En introduktion till den nya appregistreringsupplevelsen i Azure-portalen
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154601"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Den nya registreringsupplevelsen för Azure Portal-appar

Det finns många förbättringar i den nya [appregistreringarna](https://go.microsoft.com/fwlink/?linkid=2083908) i Azure-portalen. Om du är mer bekant med programregistreringsportalen (apps.dev.microsoft.com) erfarenhet för att registrera eller hantera konvergerade program, så kallade den gamla upplevelsen, kommer den här utbildningsguiden att komma igång med den nya upplevelsen.

## <a name="whats-not-changing"></a>Vad förändras inte?

- Dina program och relaterade konfigurationer kan hittas som de är i den nya upplevelsen. Du behöver inte registrera programmen igen och användare av dina program behöver inte logga in igen.

    > [!NOTE]
    > Du måste logga in med kontot som du använde för att registrera program för att hitta dem i Azure-portalen. Vi rekommenderar att du kontrollerar den inloggade användaren i Azure-portalen matchar användaren som har loggat in på programregistreringsportalen genom att jämföra e-postadressen från din profil.
    >
    > I vissa fall, särskilt när du loggar in med personliga Microsoft-konton (t.ex. e-post i din Azure AD-klientorganisation. Om du fortfarande tror att dina program saknas loggar du ut och loggar in med rätt konto.

- Live SDK-appar som skapats med personliga Microsoft-konton stöds ännu inte i Azure-portalen och kommer att fortsätta att finnas kvar i den gamla upplevelsen inom en snar framtid.

## <a name="key-changes"></a>Viktiga ändringar

-   I den gamla upplevelsen registrerades appar som standard som konvergerade appar som stöder alla organisationskonton (multitenant) samt personliga Microsoft-konton. Detta kunde inte ändras via den gamla upplevelsen, vilket gör det svårt att skapa appar som bara stöds av organisationskonton (antingen multitenant eller en enda klient).
    Med den nya upplevelsen kan du registrera appar som stöder alla dessa alternativ. [Läs mer om apptyper](active-directory-v2-registration-portal.md).

-   I den nya upplevelsen, om ditt personliga Microsoft-konto också finns i en Azure AD-klientorganisation, ser du tre flikar – alla program i klienten, ägda program i klienten samt program från ditt personliga konto. Så om du tror att appar som är registrerade med ditt personliga Microsoft-konto saknas kontrollerar du fliken **Program från ditt personliga konto.**

-   I den nya upplevelsen kan du enkelt växla mellan klienter genom att navigera till din profil och välja växelkatalog.

## <a name="list-of-applications"></a>Lista över ansökningar

-   Den nya applistan visar program som har registrerats via den äldre appregistreringsupplevelsen i Azure-portalen (endast appar som loggar in Azure AD-konton) samt appar som är registrerade via [programregistreringsportalen](https://apps.dev.microsoft.com/) (appar som loggar in både Azure AD och personliga Microsoft-konton).

-   Den nya applistan har ytterligare två kolumner: **Skapad i** kolumn och **kolumnen Certifikat & hemligheter** som visar statusen (aktuell, snart förfallotid eller utgånget) för autentiseringsuppgifter som har registrerats i appen.

## <a name="new-app-registration"></a>Ny appregistrering

För att registrera en konvergerad app var du bara tvungen att ange ett namn för att registrera en konvergerad app. Apparna som skapades registrerades som konvergerade appar som stöder alla organisationskataloger (multitenant) samt personliga Microsoft-konton.  Detta kunde inte ändras via den gamla upplevelsen, vilket gör det svårt att skapa appar som bara stöds av organisationskonton (antingen multitenant eller en enda klient). [Läs mer om kontotyper som stöds](v2-supported-account-types.md)

I den nya upplevelsen måste du ange ett namn för appen och välja vilka kontotyper som stöds. Du kan också ange en omdirigera URI.
Om du tillhandahåller en omdirigerings-URI måste du ange om den är webb/offentlig (inbyggt/mobilt och stationärt). Mer information om hur du registrerar en app med hjälp av den nya appregistreringsupplevelsen finns i [den här snabbstarten](quickstart-register-app.md).

## <a name="app-management-page"></a>Sidan Apphantering

Den gamla upplevelsen hade en enda apphanteringssida för konvergerade appar med följande avsnitt: Egenskaper, Programhemligheter, Plattformar, Ägare, Microsoft Graph-behörigheter, Profil och Avancerade alternativ.

Den nya upplevelsen i Azure-portalen representerar dessa funktioner på separata sidor. Här hittar du motsvarande funktionalitet:

-   Egenskaper - Namn och program-ID finns på sidan Översikt.

-   Application Secrets finns på sidan Certifikat & hemligheter

-   Plattformskonfigurationen finns på sidan Autentisering

-   Microsoft Graph-behörigheter finns på sidan API-behörigheter tillsammans med andra behörigheter

-   Profilen finns på varumärkessidan

-   Avancerat alternativ - Stöd för Live SDK finns på sidan Autentisering.

## <a name="application-secretscertificates--secrets"></a>Programhemligheter/intyg & hemligheter

I den nya upplevelsen har **programhemligheter** bytt namn till **Certifikat & hemligheter**. Dessutom kallas **offentliga nycklar** för **certifikat** och lösenord som **klienthemligheter**. **Passwords** Vi valde att inte ta med den här funktionen i den nya upplevelsen av säkerhetsskäl, därför kan du inte längre generera ett nytt nyckelpar.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plattformar/autentisering: Svara på URL:er/omdirigera URI:er
I den gamla upplevelsen hade en app avsnittet Plattformar för webb-, inbyggt och webb-API för att konfigurera url:er för omdirigering, url för utloggning och implicit flöde.

I den nya upplevelsen finns svarsadresser\'i avsnittet Autentisering i appen. Dessutom kallas de omdirigera URI:er och formatet för omdirigerings-URI:er har ändrats. De måste associeras med en apptyp (webb eller offentlig klient - mobil och stationär). [Läs mer](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Webb-API:er konfigureras på Exponera en API-sida.

> [!NOTE]
> Prova den nya autentiseringsinställningsupplevelsen där du kan konfigurera inställningar för ditt program baserat på den plattform eller enhet som du vill rikta in dig på. [Läs mer](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Behörigheter/API-behörigheter för Microsoft Graph

-   När du väljer ett API i den gamla upplevelsen kan du välja mellan Microsoft Graph API:er. I den nya upplevelsen kan du välja mellan många Microsoft-API:er, inklusive Microsoft Graph, API:er från din organisation och dina API:er, detta presenteras på tre flikar: Microsoft API:er, API:er som min organisation använder eller Mina API:er. Sökfältet på API:er som min organisation använder fliksökningar via tjänstens huvudnamn i klienten.

    > [!NOTE]
    > Den här fliken visas inte om programmet inte är associerat med en klient. Mer information om hur du begär behörigheter med den nya upplevelsen finns i [den här snabbstarten](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   Den gamla upplevelsen hade inte en **grant-behörighetsknapp.** I den nya upplevelsen finns det ett bidragsmedgivandeavsnitt med en **bidragsadministratörsmedgivandeknapp** i avsnittet API-behörigheter för en app. Endast en administratör kan bevilja samtycke och den här knappen är endast aktiverad för administratörer. När en administratör väljer knappen **Bevilja administratörsgodkännande** beviljas administratörsgodkännande för alla begärda behörigheter.

## <a name="profile"></a>Profil
I den gamla upplevelsen hade Profile Logo, Hemsida URL, Användarvillkor URL och sekretesspolicy URL konfiguration. I den nya upplevelsen finns dessa på Branding-sidan.

## <a name="application-manifest"></a>Programmanifest
I den nya upplevelsen kan du med manifestsidan redigera och uppdatera appens attribut. Mer information finns i [Programmanifest](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användargränssnitt
Det finns ett nytt användargränssnitt för egenskaper som tidigare bara kunde ställas in med manifestredigeraren eller API:et, eller som inte fanns.

-   Implicit bidragsflöde (oauth2AllowImplicitFlow) finns på sidan Autentisering. Till skillnad från den gamla upplevelsen kan du aktivera åtkomsttoken eller ID-token, eller båda.

-   Scope som definieras av det här API:et (oauth2Permissions) och auktoriserade klientprogram (preAuthorizedApplications) kan konfigureras via sidan Exponera ett API. Mer information om hur du konfigurerar en app så att den är ett webb-API och exponerar behörigheter/scope finns i [den här snabbstarten](quickstart-configure-app-expose-web-apis.md).

-   Publisher-domän (som visas för användare i [programmets\'samtyckesfråga)](application-consent-experience.md)finns på sidan Branding blade. Mer information om hur du konfigurerar en utgivardomän finns i [den här informationsinformationen](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya erfarenheten har följande begränsningar:

-   Den nya upplevelsen stöder ännu inte appregistreringar för Azure AD B2C-klienter.

-   Den nya upplevelsen stöder ännu inte Live SDK-appar som skapats med personliga Microsoft-konton.

-   Det går inte att ändra värdet för konton som stöds i användargränssnittet. Du måste använda appmanifestet\'om du inte växlar mellan Azure AD-enklient och flera innehavare.

   > [!NOTE]
   > Om du är en personlig Microsoft-kontoanvändare i Azure AD-klienten och klientadministratören har begränsad åtkomst till Azure-portalen kan du få en åtkomst nekad. Men om du kommer igenom genvägen genom att skriva appregistreringar i sökfältet eller fästa den, kommer du att kunna komma åt den nya upplevelsen.

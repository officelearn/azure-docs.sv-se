---
title: Ny registrering för Azure-portalappar
titleSuffix: Microsoft identity platform
description: En introduktion till den nya appens registrerings upplevelse i Azure Portal
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
ms.openlocfilehash: 3a954167dc0698389680a92511621fb6acf4b12b
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889983"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Den nya Azure Portal appens registrerings upplevelse

Det finns många förbättringar i den nya [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) miljön i Azure Portal. Om du är mer bekant med program registrerings portalen (apps.dev.microsoft.com) för att registrera eller hantera program, som kallas "gammal upplevelse", kommer den här guiden att hjälpa dig att komma igång med den nya upplevelsen.

## <a name="whats-not-changing"></a>Vad ändras inte?

- Dina program och relaterade konfigurationer kan hittas i den nya upplevelsen. Du behöver inte registrera programmen igen och användarna av dina program behöver inte logga in igen.

    > [!NOTE]
    > Du måste logga in med det konto du använde för att registrera program för att hitta dem i Azure Portal. Vi rekommenderar att du kontrollerar den inloggade användaren i Azure Portal matchar användaren som var inloggad på program registrerings portalen genom att jämföra e-postadressen från din profil.
    >
    > I vissa fall, särskilt när du loggar in med personliga Microsoft-konton (t. ex. Outlook, Live, Xbox osv.) med en e-postadress för Azure AD, upptäckte vi Azure Portal att du får ett annat konto med samma e-postadress i Azure AD-klienten. Om du fortfarande tror att dina program saknas kan du logga ut och logga in med rätt konto.

- Live SDK-appar som skapats med personliga Microsoft-konton stöds ännu inte i Azure Portal och fortsätter att vara kvar i den gamla miljön i nära framtid.

## <a name="key-changes"></a>Nyckel ändringar

-   I den tidigare versionen registrerades appar som standard som *konvergerade* appar – appar som stöder alla organisations konton (flera innehavare) samt personliga Microsoft-konton. Detta kunde inte ändras genom den gamla upplevelsen, vilket gör det svårt att skapa appar som endast stöder organisations konton (antingen flera innehavare eller en enskild klient).
    Med den nya upplevelsen kan du registrera appar som stöder alla dessa alternativ. [Läs mer om app-typer](active-directory-v2-registration-portal.md).

-   I den nya upplevelsen, om din personliga Microsoft-konto också finns i en Azure AD-klient, ser du tre flikar – alla program i klienten, ägda program i klienten och program från ditt personliga konto. Om du tror att appar som registrerats med din personliga Microsoft-konto saknas, kontrollerar du **programmen från fliken personligt konto** .

-   I den nya upplevelsen kan du enkelt växla mellan klienter genom att gå till din profil och välja Växla katalog.

## <a name="list-of-applications"></a>Lista över program

-   I listan med nya appar visas program som har registrerats via den äldre appens registrering i Azure Portal (appar som bara loggar in på Azure AD-konton) samt appar som registrerats, även om [program registrerings portalen](https://apps.dev.microsoft.com/) (appar som loggar in både Azure AD och personliga Microsoft-konton).

-   Listan över nya appar har två ytterligare kolumner: **skapad i** kolumnen kolumn och **certifikat & hemligheter** som visar status (aktuell, förfaller snart eller upphört) autentiseringsuppgifter som har registrerats i appen.

## <a name="new-app-registration"></a>Ny appregistrering

För att registrera en app var du bara tvungen att ange ett namn i den tidigare versionen. De appar som har skapats registrerades som *konvergerade* appar – appar som stöder alla organisations kataloger (flera innehavare) samt personliga Microsoft-konton.  Detta kunde inte ändras genom den gamla upplevelsen, vilket gör det svårt att skapa appar som endast stöder organisations konton (antingen en eller flera innehavare). [Läs mer om konto typer som stöds](v2-supported-account-types.md)

I den nya upplevelsen måste du ange ett namn för appen och välja de konto typer som stöds. Du kan också ange en omdirigerings-URI.
Om du anger en omdirigerings-URI måste du ange om den är webb/offentlig (inbyggd/mobil och stationär). Mer information om hur du registrerar en app med hjälp av den nya program registrerings upplevelsen finns i [den här snabb](quickstart-register-app.md)starten.

## <a name="app-management-page"></a>Sidan hantering av appar

Den gamla upplevelsen hade en enda program hanterings sida för appar med följande avsnitt: egenskaper, program hemligheter, plattformar, ägare, Microsoft Graph behörigheter, profil och avancerade alternativ.

Den nya upplevelsen i Azure Portal presenterar dessa funktioner på separata sidor. Här kan du hitta motsvarande funktioner:

- Egenskaper – namn och program-ID finns på översikts sidan.
- Program hemligheter finns på sidan certifikat & hemligheter
- Plattforms konfiguration finns på sidan autentisering
- Microsoft Graph behörigheter finns på sidan API-behörigheter tillsammans med andra behörigheter
- Profilen är på varumärkes Sidan
- Avancerat alternativ – Live SDK-stöd finns på sidan autentisering.

## <a name="application-secretscertificates--secrets"></a>Program hemligheter/certifikat & hemligheter

I den nya upplevelsen har **program hemligheter** bytt namn till **certifikat & hemligheter**. Dessutom kallas **offentliga nycklar** för **certifikat** och **lösen ord** som **klient hemligheter**. Vi valde att inte ta med den här funktionen i den nya upplevelsen av säkerhets skäl, så du kan därför inte längre generera ett nytt nyckel par.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plattformar/autentisering: svars webb adresser/omdirigerings-URI: er
I den tidigare versionen har en app haft plattformar för webb-, intern-och webb-API för att konfigurera omdirigerings-URL: er, utloggnings-URL och implicit flöde.

I den nya upplevelsen hittar du svars-URL: er\'i avsnittet om app s-autentisering. Dessutom kallas de omdirigerings-URI: er och formatet för omdirigering av URI: er har ändrats. De måste vara kopplade till en app-typ (webb eller offentlig klient-mobil och stationär dator). [Läs mer](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Webb-API: er har kon figurer ATS i exponera en API-sida.

> [!NOTE]
> Testa de nya inställningarna för autentisering där du kan konfigurera inställningar för ditt program baserat på den plattform eller enhet som du vill använda som mål. [Läs mer](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph behörigheter/API-behörigheter

-   När du väljer ett API i den gamla upplevelsen kan du välja endast Microsoft Graph API: er. I den nya upplevelsen kan du välja bland många Microsoft-API: er, inklusive Microsoft Graph, API: er från din organisation och dina API: er, presenteras i tre flikar: Microsoft API: er som min organisation använder eller mina API: er. Sök fältet i API: er i min organisation använder TABB-sökningar via tjänstens huvud namn i klient organisationen.

    > [!NOTE]
    > Den här fliken visas inte om programmet inte är associerat med en klient. Mer information om hur du begär behörigheter med hjälp av den nya upplevelsen finns i [den här snabb](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)starten.

-   Den gamla versionen har inte någon **behörighets knappen bevilja** . I den nya upplevelsen finns avsnittet beviljande medgivande med knappen **bevilja administratörs medgivande** i avsnittet API-behörigheter för appen. Endast en administratör kan bevilja medgivande och den här knappen är endast aktive rad för administratörer. När en administratör väljer knappen **bevilja administratörs medgivande** beviljas administratörs medgivande till alla begärda behörigheter.

## <a name="profile"></a>Profil
I den gamla upplevelsen hade profilen en logo typ, start sidans URL, URL: en för URL: en och sekretess policyn för URL-konfiguration. I den nya upplevelsen hittar du dessa på sidan varumärkes anpassning.

## <a name="application-manifest"></a>Programmanifest
På sidan ny upplevelse kan du redigera och uppdatera appens attribut på manifest sidan. Mer information finns i [program manifestet](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användar gränssnitt
Det finns ett nytt gränssnitt för egenskaper som tidigare endast kunde anges med hjälp av manifest redigeraren eller API: t eller som inte finns.

-   Du hittar oauth2AllowImplicitFlow (implicita tilldelnings flöde) på sidan autentisering. Till skillnad från den gamla upplevelsen kan du aktivera åtkomsttoken eller ID-token, eller båda.

-   Omfattningar som definieras av denna API (oauth2Permissions) och auktoriserade klient program (preAuthorizedApplications) kan konfigureras via sidan exponera en API. Mer information om hur du konfigurerar en app så att den är ett webb-API och visar behörigheter/omfattningar finns i [den här snabb](quickstart-configure-app-expose-web-apis.md)starten.

-   Publisher-domän (som visas för användare på [begäran om\'program s medgivande](application-consent-experience.md)) hittar du på sidan varumärkes dialog. Mer information om hur du konfigurerar en Publisher-domän finns i [den här instruktionen](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya upplevelsen har följande begränsningar:

-   Den nya upplevelsen har ännu inte stöd för Appregistreringar för Azure AD B2C klienter.

-   Den nya upplevelsen stöder ännu inte Live SDK-appar som skapats med personliga Microsoft-konton.

-   Det finns inte stöd för att ändra värdet för konton som stöds i användar gränssnittet. Du måste använda app-manifestet om du\'inte byter mellan Azure AD-klienten och flera klienter.

   > [!NOTE]
   > Om du är en personlig Microsoft-konto användare i Azure AD-klienten och klient administratören har begränsat åtkomsten till Azure Portal kan du få åtkomst nekad. Men om du kommer genom genvägen genom att skriva Appregistreringar i Sök fältet eller fästa det, kommer du att kunna komma åt den nya upplevelsen.

## <a name="next-steps"></a>Nästa steg

För att komma igång med den nya appens registrerings upplevelse, se [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md).

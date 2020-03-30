---
title: Ny utbildningsguide för Azure-portalappsregistreringar
description: Introducerar den nya registreringsupplevelsen för Azure Portal-appar
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154584"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Ny utbildningsguide för Azure-portalappsregistreringar

Du kan hitta många förbättringar i den nya [appregistreringsupplevelsen](https://go.microsoft.com/fwlink/?linkid=2083908) i Azure-portalen. Om du är bekant med appregistreringarna (äldre) i Azure-portalen kan du använda den här utbildningsguiden för att komma igång med den nya upplevelsen.

I Azure Active Directory är den nya programregistreringsupplevelsen som beskrivs här allmänt tillgänglig (GA). I Azure Active Directory B2C (Azure AD B2C) är den här upplevelsen i förhandsversion.

## <a name="key-changes"></a>Viktiga ändringar

- Appregistreringar är inte begränsade till att vara antingen en *webbapp/ett API* eller en *inbyggd* app. Du kan använda samma appregistrering för alla dessa appar genom att registrera respektive omdirigerings-URI:er.

- Den äldre upplevelsen stöds appar som loggar in med endast organisationens (Azure AD)-konton. Appar registrerades som en enda klient. Appar stödde endast organisationskonton från katalogen som appen registrerades i. Appar kan ändras för att vara flera innehavare och stödja alla organisationskonton. Med den nya upplevelsen kan du registrera appar som kan stödja både dessa alternativ och ett tredje alternativ: alla organisationskonton samt personliga Microsoft-konton.

- Den äldre upplevelsen var endast tillgänglig när den loggades in på Azure-portalen med ett organisationskonto. Med den nya upplevelsen kan du använda personliga Microsoft-konton som inte är associerade med en katalog.

## <a name="list-of-applications"></a>Lista över ansökningar

Den nya applistan visar program som har registrerats via den äldre appregistreringen i Azure-portalen. Dessa appar loggar in med hjälp av Azure AD-konton. Den nya applistan visar också appar som är registrerade via Application Registration Portal. Dessa appar loggar in med Hjälp av Azure AD och personliga Microsoft-konton.

>[!NOTE]
>Programregistreringsportalen har tagits bort.

Den nya applistan har ingen **kolumn för programtyp** eftersom en enskild appregistrering kan vara flera typer. Listan innehåller ytterligare två kolumner: **Skapad på** och **Certifikat & hemligheter**. **Certifikat & hemligheter** visar status för autentiseringsuppgifter som har registrerats i appen. Statusar inkluderar **Aktuell**, **Förfalla snart**och **Utgånget**.

## <a name="new-app-registration"></a>Ny appregistrering

I den äldre upplevelsen måste du registrera en app som du var skyldig att tillhandahålla: **Namn,** **Programtyp**och **Inloggnings-URL/Omdirigera URI**. Apparna som skapades var Azure AD endast program med en enda klient. De stödde bara organisationskonton från katalogen som appen registrerades i.

I den nya upplevelsen måste du ange ett **namn** för appen och välja **vilka kontotyper som stöds**. Du kan också ange en **Redirect URI**. Om du tillhandahåller en omdirigerings-URI måste du ange om den är webb/offentlig (mobil och stationär). Mer information finns i [Snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md). För Azure AD B2C finns [i Registrera ett program i Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Skillnader mellan sidan Application Registration Portal och App registrations

### <a name="the-legacy-properties-page"></a>Äldre egenskapssida

Äldreupplevelsen hade en **egenskapssida.** **Egenskaper** hade följande fält:

- **Namn**
- **Objekt-ID**
- **Program-ID:t**
- **URI för app-id**
- **Logotyp**
- **Url till startsidan**
- **Utloggnings-URL**
- **Url för användarvillkor**
- **URL för sekretesspolicy**
- **Typ av program**
- **Flera innehavare**

Den nya upplevelsen har inte den sidan. Här hittar du motsvarande funktionalitet:

- **Namn,** **logotyp,** **webbadress,** **användarvillkor url**och **webbadressen till sekretesspolicyn** finns nu på appens **varumärkessida.**
- **Objekt-ID** och **klient-ID** finns på sidan **Översikt.**
- De funktioner som styrs av **växlingsknappen för flera innehavare** i den äldre upplevelsen har ersatts av **kontotyper som stöds** på **sidan Autentisering.** Mer information finns i [Snabbstart: Ändra konton som stöds av ett program](quickstart-modify-supported-accounts.md).
- **Utloggnings-URL finns** nu på sidan **Autentisering.**
- **Programtypen** är inte längre ett giltigt fält. Omdirigerings-URI:er, som du hittar på sidan **Autentisering,** avgör i stället vilka apptyper som stöds.
- **App-ID URI** kallas nu **Application ID URI** och du kan hitta den på **Exponera ett API**. I den äldre egenskapen registrerades automatiskt med `https://{tenantdomain}/{appID}`hjälp av `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`följande format: , till exempel . I den nya upplevelsen är det automatiskt `api://{appID}`uppargent som , men det måste uttryckligen sparas. I Azure AD B2C-klienter används `https://{tenantdomain}/{appID}` formatet fortfarande.

### <a name="reply-urlsredirect-urls"></a>Svara på URL:er/omdirigera URls

I äldre upplevelse, en app hade en **svar webbadresser** sida. I den nya upplevelsen finns svarsadresser på appens **autentiseringssida.** De kallas nu Omdirigera **URI:er**.

Formatet för omdirigerings-URI:er har ändrats. De måste vara kopplade till en apptyp, antingen webb eller offentlig. Av säkerhetsskäl stöds inte `http://` jokertecken och scheman, förutom *http://localhost*.

### <a name="keyscertificates--secrets"></a>Nycklar/certifikat & hemligheter

I den äldre upplevelsen hade en app **sidan Nycklar.** I den nya upplevelsen har den bytt namn till **Certifikat & hemligheter**.

**Offentliga nycklar** kallas nu **certifikat**. **Lösenord** kallas nu **klienthemligheter**.

### <a name="required-permissionsapi-permissions"></a>Nödvändiga behörigheter/API-behörigheter

I den äldre upplevelsen hade en app en **sida med obligatoriska behörigheter.** I den nya upplevelsen har den bytt namn till **API-behörigheter**.

När du har valt ett API i den äldre upplevelsen kan du välja från en liten lista med Microsoft-API:er. Du kan också söka igenom tjänstens huvudnamn i klienten. I den nya upplevelsen kan du välja mellan flera flikar: **Microsoft API:er,** **API:er som min organisation använder**eller Mina **API:er**. Sökfältet på **API:er som min organisation** använder fliksökningar via tjänstens huvudnamn i klienten.

> [!NOTE]
> Den här fliken visas inte om programmet inte är associerat med en klient. Mer information om hur du begär behörigheter finns i [Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md).

Den äldre upplevelsen hade knappen **Bevilja behörighet högst** upp på sidan **Begärd behörighet.** I den nya upplevelsen har sidan **Bevilja medgivande** knappen en **bidragsadministratör medgivande** i en apps **API-behörigheter.** Det finns också vissa skillnader i hur knapparna fungerar.

I den äldre upplevelsen varierade logiken beroende på den inloggade användaren och de behörigheter som begärs. Logiken var:

- Om endast behörigheter som kan kunna med användaren har begärts och den inloggade användaren inte var administratör, kan användaren ge användaren samtycke till de begärda behörigheterna.
- Om minst en behörighet som kräver administratörsmedgivande begärdes och den inloggade användaren inte var administratör, fick användaren ett felmeddelande när han försökte bevilja medgivande.
- Om den inloggade användaren var administratör beviljades administratörsgodkännande för alla begärda behörigheter.

I den nya upplevelsen kan endast en administratör bevilja medgivande. När en administratör väljer **Bevilja administratörsmedgivande**beviljas administratörsmedgivande för alla begärda behörigheter.

## <a name="deleting-an-app-registration"></a>Ta bort en appregistrering

I äldre upplevelse kan du bara ta bort appar med en enda klientorganisation. Borttagningsknappen inaktiverades för appar med flera innehavare. I den nya upplevelsen kan du ta bort appar i vilket tillstånd som helst, men du måste bekräfta åtgärden. Mer information finns i [Snabbstart: Ta bort ett program som är registrerat på Microsofts identitetsplattform](quickstart-remove-app.md).

## <a name="application-manifest"></a>Programmanifest

Äldre och nya upplevelser använder olika versioner för formatet på JSON i manifestredigeraren. Mer information finns i [Azure Active Directory-appmanifest](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användargränssnitt

Den nya upplevelsen lägger till gränssnittskontroller för följande egenskaper:

- **Sidan Autentisering** har implicit`oauth2AllowImplicitFlow` **bidragsflöde** ( ). Till skillnad från i den äldre upplevelsen kan du aktivera **Access-token** eller **ID-token**eller båda.
- Sidan **Exponera ett API** innehåller Scope som definieras av det här **API:et** (`oauth2Permissions`) och **auktoriserade klientprogram** (`preAuthorizedApplications`). Mer information om hur du konfigurerar en app så att den är ett webb-API och exponerar behörigheter/scope finns i [Snabbstart: Konfigurera ett program för att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md).
- **Sidan Branding** innehåller **Publisher-domänen**. Utgivardomänen visas för användare i [programmets samtyckesfråga](application-consent-experience.md). Mer information finns i Så här konfigurerar du [ett programs utgivardomän](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya erfarenheten har följande begränsningar:

- Formatet på klienthemligheter (applösenord) skiljer sig från det äldres upplevelse och kan bryta CLI.
- Det går inte att ändra värdet för konton som stöds i användargränssnittet. Du måste använda appmanifestet om du inte växlar mellan Azure AD-enklient och flera innehavare.

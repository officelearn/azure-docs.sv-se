---
title: App-registreringar i Azure portal-utbildning för - Azure
description: Skapa inbäddad och ge webbläsaren utan autentiseringsflöden med hjälp av kod för enheten.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870124"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Utbildning-guide: App-registreringar i Azure portal  

Du kan hitta ett antal förbättringar i den nya [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelse i Azure-portalen. Om du är mer bekant med den äldre upplevelsen kan du använda den här utbildning-guiden för att komma igång med den nya upplevelsen.

## <a name="key-changes"></a>Ändringar i entitetsnyckeln

- App-registreringar inte är begränsade till att antingen en **web app/API** eller en **interna** app. Du kan använda samma appregistreringen för alla dessa genom att registrera respektive omdirigerade URI: er.
- Äldre upplevelsen stöds-App som loggar in organisationens (Azure AD)-konton endast. Appar som registrerats som en enda klient (stöder bara organisationskonton från appen har registrerats i katalogen) och kan ändras om du vill att flera innehavare (stöder alla organisationskonton). Den nya upplevelsen kan du registrera appar som kan stödja båda alternativen samt ett tredje alternativ: alla organisationskonton samt personliga Microsoft-konton.
- Den äldre upplevelsen var bara tillgängliga när du loggat in på Azure-portalen med ett organisationskonto. Du kan använda personliga Microsoft-konton som inte är associerade med en katalog med den nya upplevelsen.

## <a name="list-of-applications"></a>Lista över program

- Den nya applistan innehåller program som har registrerats via appen äldre registreringar upplevelse i Azure-portalen (appar som loggar in i Azure AD-konton) samt appar registrerad men den [Programregistreringsportalen](https://apps.dev.microsoft.com/) (appar som loggar in i Azure AD och personliga Microsoft-konton).
- Den nya applistan inte har en **programtyp** kolumnen (eftersom en enda app-registrering kan vara flera typer) och har två ytterligare kolumner: en **skapas på** kolumn och en **certifikat & hemligheter** kolumn som visar status (aktuella, upphör snart att gälla eller upphört att gälla) av autentiseringsuppgifter som har registrerats i appen.

## <a name="new-app-registration"></a>Ny appregistrering

I den äldre upplevelsen för att registrera en app tvungen du att tillhandahålla: **Namnet**, **programtyp**, och **inloggnings-URL/Redirect URI**. Appar som har skapats har vilket innebär att de endast stöd för organisationskonton från den katalog som appen har registrerats i Azure AD endast enda klient-program.

I den nya upplevelsen, måste du ange en **namn** för appen och välj den **stöds kontotyper**. Du kan också bifoga en **omdirigerings-URI**. Om du anger en omdirigerings-URI måste du ange om är det web/offentlig (mobila och stationära). Mer information om hur du registrerar en app med de nya app-registreringarna Upplev, se [snabbstarten](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>Äldre egenskapssidan

Den äldre upplevelsen hade en **egenskaper** sida som inte har den nya upplevelsen. Den **egenskaper** bladet hade följande fält: **Namnet**, **objekt-ID**, **program-ID**, **Appidentitets-URI**, **logotyp**, **URL-Adressen** , **URL för utloggning**, **URL för Tjänstvillkor**, **URL till sekretesspolicy**, **programtyp**, och  **Flera innehavare.**

Här är där du hittar motsvarande funktioner i den nya miljön:

- **Namnet**, **logotyp**, **URL-Adressen**, **URL för Tjänstvillkor**, och **URL till sekretesspolicy** är nu på appens  **Anpassning av** sidan.
- **Objekt-ID** och **(klient)-ID: T** finns på den **översikt** sidan.
- Funktionen styrs av den **flera innehavare** växling i den äldre upplevelsen har ersatts av **stöds kontotyper** på den **autentisering** sidan. Läs mer om hur flera innehavare mappar till stöds kontotyperna [snabbstarten](quickstart-modify-supported-accounts.md).
- **URL för utloggning** är nu på den **autentisering** sidan.
- **Programtyp** är inte längre giltigt fält. I stället omdirigerings-URI: er (som finns på den **autentisering** sidan) avgör vilka typer av appar som stöds.
- **App-ID-URI** kallas nu **program-ID-URI** och du hittar den på den **exponerar ett API** bladet. Den äldre upplevelsen kan den här egenskapen automatiskt-registrerades med hjälp av följande format: `https://{tenantdomain}/{appID}` (till exempel `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). I det nya formatet är det automatiskt genererade som `api://{appID}`, men du måste uttryckligen att spara.

## <a name="reply-urlsredirect-urls"></a>Svars-URL: er/omdirigerings-URL

I den äldre upplevelsen kan en app hade en **Svarswebbadresser** sidan. I den nya upplevelsen svars-URL finns på en app **autentisering** avsnittet. Dessutom kan de kallas **omdirigerings-URI: er**. Dessutom format för omdirigeras URI: er har ändrats. De är obligatoriska som ska associeras med en typ av app (webb- eller offentlig). Dessutom av säkerhetsskäl jokertecken och http:// scheman stöds inte (med undantag för http://localhost).

## <a name="keyscertificates--secrets"></a>Nycklar/certifikat och hemligheter

I den äldre upplevelsen kan en app hade **nycklar** sidan. I den nya upplevelsen, det har bytt namn till **certifikat och hemligheter**. Dessutom **offentliga nycklar** kallas **certifikat** och **lösenord** kallas **klienten hemligheter**.

## <a name="required-permissionsapi-permissions"></a>Nödvändiga behörigheter/API-behörigheter

- I den äldre upplevelsen kan en app hade en **nödvändiga behörigheter** sidan. I den nya upplevelsen, det har bytt namn till **API-behörigheter**.
- När du väljer ett API i den äldre upplevelsen kan du välja från en lista över Microsoft APIs eller Sök igenom tjänstens huvudnamn i klientorganisationen. I den nya upplevelsen kan du välja bland flera flikar: **Microsoft APIs**, **API: er som min organisation använder**, eller **Mina API: er**. Sökfältet på **API: er min organisation** använder fliken söker igenom tjänstens huvudnamn i klientorganisationen. 

   > [!NOTE]
   > Den här fliken visas inte om ditt program är inte kopplad till en klient. Mer information om hur du begär behörigheter med hjälp av den nya upplevelsen finns i [snabbstarten](quickstart-configure-app-access-web-apis.md).

- Den äldre upplevelsen hade en **bevilja** längst upp på den **begärda behörigheter** sidan. I den nya upplevelse, finns det en **ge medgivande** med en **bevilja administratörsmedgivande** knappen i en app **API-behörigheter** avsnittet. Dessutom finns vissa skillnader i hur funktionen knappar:
   - I den äldre upplevelsen logiken variera beroende på den inloggade användaren och de behörigheter som begärts. Logiken som var:
      - Om bara samtycke kan användarbehörigheter begärdes och den inloggade användaren har inte en administratör, kunde användaren att bevilja tillstånd för behörigheterna som krävs.
      - Om minst en behörighet som kräver administratörens godkännande begärdes och den inloggade användaren har inte en administratör kan får användaren ett felmeddelande när du försöker ge ditt medgivande.
      - Om den inloggade användaren är administratör kan har administratörens godkännande beviljats för behörigheterna som krävs.
   - I den nya upplevelsen kan bara en administratör ge ditt medgivande. När en administratör väljer den **bevilja administratörens godkännande** knappen administratörens godkännande har beviljats behörigheterna som krävs.

## <a name="deleting-an-app-registration"></a>Tar bort en appregistrering

I den äldre upplevelsen hade en app ska vara en enda klient som ska tas bort. Knappen Ta bort har inaktiverats för appar för flera klienter. Appar kan tas bort i varje delstat i den nya upplevelsen, men du måste bekräfta åtgärden. Läs mer om att ta bort appregistreringar [snabbstarten](quickstart-remove-app.md).

## <a name="application-manifest"></a>Programmanifest

Gamla och nya upplevelser använder olika versioner för formatet för JSON i redigeringsprogrammet för applikationsmanifestet. Mer information finns i [programmanifestet](reference-app-manifest.md).

## <a name="new-ui"></a>Nytt användargränssnitt

Det finns nya Användargränssnittet för egenskaper som kunde tidigare bara anges med hjälp av redigeringsprogrammet för applikationsmanifestet eller API: et eller inte fanns.

- **Implicit flöde beviljat med** (oauth2AllowImplicitFlow) finns på den **autentisering** sidan. Till skillnad från i den äldre upplevelsen kan du aktivera **åtkomsttoken** eller **id-token**, eller båda.
- **Omfattningar som definieras av den här API: et** (oauth2Permissions) och **behörighet klientprogram** (preAuthorizedApplications) kan konfigureras via den **exponerar ett API** sidan. Mer information om hur du konfigurerar en app för att vara ett webb-API och exponera behörigheter/scope finns i [snabbstarten](quickstart-configure-app-expose-web-apis.md).
- **Apputgivarens domän** (som visas för användarna på den [medgivandetext för programmets](application-consent-experience.md)) finns på den **varumärkesriktlinjer bladet** sidan. Mer information om hur du konfigurerar en utgivardomän finns i [den här anvisningen](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Begränsningar

Den nya miljön har följande begränsningar:

- Den nya upplevelsen är för närvarande inte tillgänglig i Azure AD B2C-klienter.
- Formatet på klienten hemligheter (lösenord) skiljer sig från som den äldre upplevelsen och delar upp CLI.
- Ändra värdet för stöds konton stöds inte i Användargränssnittet. Du måste använda appmanifestet såvida inte du växlar mellan Azure AD enda klient och flera innehavare.

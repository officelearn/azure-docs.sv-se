---
title: Skyddade webb-API - appregistrering | Azure
description: Lär dig hur du skapar ett skyddat webb-API och den information du behöver registrera appen.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111118"
---
# <a name="protected-web-api---app-registration"></a>Skyddade webb-API - appregistrering

Den här artikeln förklarar app registrering dig ett skyddat webb-API.

Gå till [Snabbstart: Registrera ett program med Microsoft identity-plattformen](quickstart-register-app.md) vanliga anvisningar för hur du registrera programmet.

## <a name="accepted-token-version"></a>Godkänd token version

Microsoft identity-plattformen endpoint kan utfärda två typer av token: token v1.0 och v2.0-token. Du kan läsa mer om dessa token i [åtkomsttoken](access-tokens.md). Godkänd token version beror på den **stöds kontotyper** du angav när du skapade ditt program:

- Om värdet för **stöds kontotyper** är **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)** , godkänd token version kommer att v2.0.
- Annars blir godkänd token version v1.0.

När du har skapat programmet kan ändra du godkänd token version genom att följa dessa steg:

1. Välj din app i Azure-portalen och välj sedan den **Manifest** för din app.
2. I manifestet, söker du efter **”accessTokenAcceptedVersion”** , och se dess värde är **2**. Den här egenskapen kan Azure AD som vet att webb-API accepterar v2.0-token. Om det är **null**, godkänd token version kommer att v1.0.
3. Välj **Spara**.

> [!NOTE]
> Det är upp till webb-API för att bestämma vilka token version (v1.0 eller v2.0) som accepteras. När klienter begär en token för ditt webb-API med hjälp av Microsoft identity-plattformen v2.0-slutpunkten kan får de en token som anger vilken version accepteras av webb-API.

## <a name="no-redirect-uri"></a>Inga omdirigerings-URI

Webb-API: er behöver inte registrera en omdirigerings-URI eftersom ingen användare är inloggad interaktivt.

## <a name="expose-an-api"></a>Exponera ett API

En annan inställning som är specifika för web API: er är exponerad API och exponerade omfång.

### <a name="resource-uri-and-scopes"></a>Resurs-URI och scope

Scope är vanligtvis formulärets `resourceURI/scopeName`. För Microsoft Graph scope har genvägar som `User.Read`, men den här strängen är bara en genväg för `https://graph.microsoft.com/user.read`.

Under registreringen måste du definiera följande parametrar:

- Resurs-URI - som standard på programregistreringsportalen rekommenderar att du kan använda `api://{clientId}`. Det här resurs-URI är unikt, men det är inte mänskliga läsbar. Du kan ändra den, men se till att det blir unikt.
- En eller flera **scope** (för klientprogram, de visas som **delegerade behörigheter** för ditt webb-API)
- En eller flera **approller** (för klientprogram, de visas som **programbehörigheter** för ditt webb-API)

Scope visas också på godkännandeskärmen som presenteras för slutanvändare som använder programmet. Därför måste du ange motsvarande strängar som beskriver vilka:

- Som visas av slutanvändaren
- Enligt klientadministratör som kan bevilja administratörens godkännande

### <a name="how-to-expose-delegated-permissions-scopes"></a>Hur du kan exponera delegerade behörigheterna (omfång)

1. Välj den **exponerar ett API** avsnittet i program-registreringen och:
   1. Välj **Lägg till omfång**.
   1. Om så krävs, accepterar du den föreslagna program-ID-URI (api :// {clientId}) genom att välja **spara och fortsätt**.
   1. Ange följande parametrar:
      - För **scopenamn**, använda `access_as_user`.
      - För **vem som kan godkänna**, se till att den **administratörer och användare** alternativet är markerat.
      - I **visningsnamn för administratörsmedgivande**, typen `Access TodoListService as a user`.
      - I **beskrivning av administratörsmedgivande**, typen `Accesses the TodoListService Web API as a user`.
      - I **visningsnamn för användarmedgivande**, typen `Access TodoListService as a user`.
      - I **beskrivning av användarmedgivande**, typen `Accesses the TodoListService Web API as a user`.
      - Behåll **tillstånd** inställd **aktiverad**.
      - Välj **Lägg till omfattning**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Fall där ditt webb-API anropas av daemon-program

I detta stycke lär du dig att registrera din skyddade webb-API så att den kan anropas på ett säkert sätt av daemon-program:

- Du måste exponera **programbehörigheter**. Du kan endast deklarera programbehörigheter när daemon program samverkar inte med användare och därför delegerade behörigheter vore inte särskilt meningsfullt.
- Innehavaradministratörer kan kräva Azure AD för att problemet token för din Webbapp till endast de program som har registrerat att de vill ha åtkomst till någon av behörigheter för webb-API-appar.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Hur du kan exponera programbehörigheter (roller)

Om du vill exponera behörigheter för programmet, måste du redigera manifestet.

1. I programregistrering för ditt program, klickar du på **Manifest**.
1. Redigera manifestet genom att leta upp den `appRoles` inställning och lägga till en eller flera roller för programmet. Rolldefinitionen tillhandahålls i exemplet JSON blocket nedan.  Lämna den `allowedMemberTypes` till ”program” endast. Kontrollera att den **id** är ett unikt guid och **displayName** och **värdet** inte innehålla blanksteg.
1. Spara manifestet.

Innehållet i `appRoles` bör vara följande (den `id` kan vara valfri unikt GUID)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Kontrollera att Azure AD utfärdar tokens för ditt webb-API endast tillåtna klienter

Webb-API söker efter den app-roll (som är utvecklare sätt att göra det). Men du kan även konfigurera Azure Active Directory för att utfärda en token för ditt webb-API till program som har godkänts av administratör för att få åtkomst till ditt API. Lägga till den här ytterligare säkerhet:

1. I appen **översikt** för din appregistrering, väljer du hyperlänken med namnet på ditt program i **hanterat program i den lokala katalogen**. Rubrik för det här fältet kan trunkeras. Du kan till exempel läsa: `Managed application in ...`

   > [!NOTE]
   >
   > När du väljer den här länken som du navigerar till den **Enterprise Programöversikt** sidan som är associerad med tjänstens huvudnamn för ditt program i den klient där du skapade den. Du kan gå tillbaka till registreringssidan för appen genom att använda Bakåt-knappen i webbläsaren.

1. Välj den **egenskaper** sidan i den **hantera** delen av Enterprise programsidor
1. Om du vill att AAD för att tillämpa åtkomst till ditt webb-API från endast vissa klienter anger **Användartilldelning krävs?** till **Ja**.

   > [!IMPORTANT]
   >
   > Genom att ange **Användartilldelning krävs?** till **Ja**, AAD kontrollerar approlltilldelningarna klienter när de begär en åtkomsttoken för webb-API. Om klienten inte har tilldelats till några AppRoles, returnerar AAD bara följande fel: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Du kan spara **Användartilldelning krävs?** till **nr**, <span style='background-color:yellow; display:inline'>Azure AD inte kontrollera approlltilldelningarna när en klient begär en åtkomsttoken för ditt webb-API</span>. Alla daemon-klienter (det vill säga alla klienter som använder flödet) skulle därför fortfarande att kunna hämta en åtkomsttoken för API: et genom att bara ange sin publik. Alla program skulle kunna få åtkomst till API utan att behöva begära behörighet för den. Nu kan detta är inte sedan slutet av det, eftersom ditt webb-API kan alltid, enligt beskrivningen i nästa avsnitt, kontrollera att programmet har rätt roll (som var godkänd av administratör), genom att verifiera att den åtkomst-token har en `roles` anspråk och rätt värde för t hans anspråk (i vårt fall `access_as_application`).

1. Välj **spara**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-protected-web-api-app-configuration.md)

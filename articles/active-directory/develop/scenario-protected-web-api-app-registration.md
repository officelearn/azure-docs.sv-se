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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536868"
---
# <a name="protected-web-api-app-registration"></a>Skyddade webb-API: Appregistrering

Den här artikeln förklarar ärendets natur appregistrering för ett skyddat webb-API.

Gå till [Snabbstart: Registrera ett program med Microsoft identity-plattformen](quickstart-register-app.md) för de vanliga stegen för att registrera en app.

## <a name="accepted-token-version"></a>Godkänd token version

Microsoft identity-plattformen endpoint kan utfärda två typer av token: token v1.0 och v2.0-token. Läs mer om dessa token [åtkomsttoken](access-tokens.md). Godkänd token version beror på den **stöds kontotyper** du angav när du skapade ditt program:

- Om värdet för **stöds kontotyper** är **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)** , godkänd token version kommer att v2.0.
- Annars blir godkänd token version v1.0.

När du har skapat programmet kan du fastställa eller ändra godkänd token version genom att följa dessa steg:

1. Välj din app i Azure-portalen och välj sedan den **Manifest** för din app.
2. I manifestet, söker du efter **”accessTokenAcceptedVersion”** . Observera att dess värde är **2**. Den här egenskapen anger till Azure Active Directory (AD Azure) att webb-API accepterar v2.0-token. Om värdet är **null**, godkänd token version är v1.0.
3. Om du har ändrat den token versionen väljer **spara**.

> [!NOTE]
> Webb-API: Anger vilken token version (v1.0 eller v2.0) som accepteras. När klienter begär en token för ditt webb-API från Microsoft identity-plattformen v2.0-slutpunkten kan får de en token som anger vilken version accepteras av webb-API.

## <a name="no-redirect-uri"></a>Inga omdirigerings-URI

Webb-API: er behöver inte registrera en omdirigerings-URI eftersom ingen användare är inloggad i interaktivt.

## <a name="expose-an-api"></a>Exponera ett API

En annan inställning som är specifika för web API: er är exponerad API och exponerade omfång.

### <a name="resource-uri-and-scopes"></a>Resurs-URI och scope

Scope har vanligtvis formen `resourceURI/scopeName`. För Microsoft Graph scope har genvägar som `User.Read`. Strängen är en genväg till `https://graph.microsoft.com/user.read`.

Under registreringen måste dessa parametrar definieras:

- Resurs-URI. Som standard på programregistreringsportalen rekommenderar att du kan använda `api://{clientId}`. Det här resurs-URI är unikt, men det är inte mänskliga läsbar. Du kan ändra den, men se till att det nya värdet är unikt.
- En eller flera *scope*. (För klientprogram, som de ska visas som *delegerade behörigheter* för ditt webb-API.)
- En eller flera *approller*. (För klientprogram, som de ska visas som *programbehörigheter* för ditt webb-API.)

Scope visas också på godkännandeskärmen som presenteras för användarna av din app. Så måste du ange de motsvarande strängar som beskriver omfång:

- Som visas av slutanvändaren.
- Enligt klientadministratör som kan bevilja administratörens godkännande.

### <a name="exposing-delegated-permissions-scopes"></a>Exponera delegerade behörigheterna (omfång)

1. Välj den **exponerar ett API** avsnittet i program-registreringen.
1. Välj **Lägg till omfång**.
1. Acceptera den föreslagna program-ID-URI (`api://{clientId}`) genom att välja **spara och fortsätt**.
1. Ange dessa parametrar:
      - För **scopenamn**, använda **access_as_user**.
      - För **vem som kan godkänna**, se till att **administratörer och användare** har valts.
      - I **visningsnamn för administratörsmedgivande**, ange **åtkomst TodoListService som en användare**.
      - I **beskrivning av administratörsmedgivande**, ange **har åtkomst till TodoListService webb-API som en användare**.
      - I **visningsnamn för användarmedgivande**, ange **åtkomst TodoListService som en användare**.
      - I **beskrivning av användarmedgivande**, ange **har åtkomst till TodoListService webb-API som en användare**.
      - Behåll **tillstånd** inställd **aktiverad**.
      - Välj **Lägg till omfattning**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Om ditt webb-API anropas av en daemon-app

I det här avsnittet lär du dig att registrera din skyddade webb-API så att på ett säkert sätt kan anropas av daemon-appar.

- Du måste exponera *programbehörigheter*. Du måste deklarera endast programbehörigheter eftersom daemon-appar inte interagera med användare, så delegerade behörigheter inte skulle kunna fungera.
- Innehavaradministratörer kan kräva Azure Active Directory (AD Azure) att problemet token för ditt webb-API till program som har registrerat för att komma åt en webb-API: er programbehörigheter.

#### <a name="exposing-application-permissions-app-roles"></a>Exponera programbehörigheter (roller)

Om du vill exponera behörigheter för programmet, måste du redigera manifestet.

1. Programregistrering för ditt program, Välj **Manifest**.
1. Redigera manifestet genom att leta upp den `appRoles` inställning och lägga till en eller flera roller för programmet. Rolldefinitionen finns i följande exempel JSON block. Lämna den `allowedMemberTypes` inställd `"Application"` endast. Kontrollera att den `id` är ett unikt GUID och att `displayName` och `value` inte innehåller blanksteg.
1. Spara manifestet.

I följande exempel visas innehållet i `appRoles`. (Den `id` kan vara valfri unikt GUID.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Se till att Azure AD utfärdar en token för ditt webb-API för att endast tillåtna klienter

Webb-API: söker efter den app-rollen. (Som är utvecklare sätt att exponera behörigheter för programmet.) Men du kan också konfigurera Azure AD för att utfärda en token för ditt webb-API enbart för program som har godkänts av administratör för att få åtkomst till ditt API. Lägga till den här ökad säkerhet:

1. I appen **översikt** för din appregistrering, klicka på länken med namnet på din app under **hanterat program i den lokala katalogen**. Rubrik för det här fältet kan trunkeras. Du kan exempelvis se **hanterat program i...**

   > [!NOTE]
   >
   > När du väljer den här länken går du till den **Enterprise Programöversikt** sidan som är associerad med tjänstens huvudnamn för ditt program i den klient där du skapade den. Du kan gå tillbaka till registreringssidan för appen genom att använda Bakåt-knappen i webbläsaren.

1. Välj den **egenskaper** sidan i den **hantera** delen av Enterprise programsidor.
1. Om du vill att Azure AD för att tillåta åtkomst till ditt webb-API från endast vissa klienter anger **Användartilldelning krävs?** till **Ja**.

   > [!IMPORTANT]
   >
   > Om du ställer in **Användartilldelning krävs?** till **Ja**, Azure AD kontrollerar approlltilldelningarna för klienter när de begär en åtkomsttoken för webb-API. Om klienten inte tilldelats några roller, Azure AD returnerar felet `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Du kan spara **Användartilldelning krävs?** inställd **nr**, *Azure AD inte kontrollera approlltilldelningarna när en klient begär en åtkomsttoken för ditt webb-API*. Alla daemon-klienter (det vill säga alla klienter som använder flödet) kommer att kunna hämta en åtkomsttoken för API: et genom att bara ange sin publik. Alla program kommer att kunna få åtkomst till API utan att behöva begära behörighet för den. Men ditt webb-API kan alltid, enligt beskrivningen i föregående avsnitt, kontrollera att programmet har rätt roll (som är godkänd av administratör). API: et utför verifieringen genom att verifiera att den åtkomst-token har en roller anspråk och att värdet för det här kravet är korrekt. (I vårt fall värdet är `access_as_application`.)

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-protected-web-api-app-configuration.md)

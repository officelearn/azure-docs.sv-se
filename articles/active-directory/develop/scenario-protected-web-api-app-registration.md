---
title: Registrering av skyddad webb-API-app | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du skapar ett skyddat webb-API och den information du behöver för att registrera appen.
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
ms.openlocfilehash: 2d9e5d051f101288b8528b47fa88b4783a040950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262508"
---
# <a name="protected-web-api-app-registration"></a>Skyddat webb-API: Appregistrering

I den här artikeln beskrivs detaljerna i appregistrering för ett skyddat webb-API.

De vanligaste stegen för att registrera en app finns i [Snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Accepterad tokenversion

Slutpunkten för Microsoft-identitetsplattformen kan utfärda v1.0-token och v2.0-token. Mer information om dessa token finns i [Access-token](access-tokens.md).

Den godkända tokenversionen beror på värdet **för kontotyper som stöds** som du väljer när du skapar ditt program.

- Om värdet **för kontotyper som stöds** är **Konton i en organisationskatalog och personliga Microsoft-konton (t.ex Outlook.com.**
- Annars är den accepterade tokenversionen v1.0.

När du har skapat programmet kan du bestämma eller ändra den godkända tokenversionen genom att följa dessa steg:

1. I Azure-portalen väljer du din app och **väljer**manifest .
1. Hitta **egenskapsåtkomsttokenAcceptedVersion** i manifestet. Egenskapens standardvärde är 2.
1. Värdet anger azure Active Directory (Azure AD) vilken tokenversion webb-API:et accepterar.
    - Om värdet är 2 accepterar webb-API:et v2.0-token.
    - Om värdet är **null**accepterar webb-API:et v1.0-token.
1. Om du har ändrat tokenversionen väljer du **Spara**.

> [!NOTE]
> Webb-API:et anger vilken tokenversion som accepteras. När en klient begär en token för ditt webb-API från slutpunkten för Microsoft identity platform (v2.0) får klienten en token som anger vilken tokenversion webb-API:et accepterar.

## <a name="no-redirect-uri"></a>Ingen omdirigera URI

Webb-API:er behöver inte registrera en omdirigerad URI eftersom ingen användare är interaktivt inloggad.

## <a name="exposed-api"></a>Exponerat API

Andra inställningar som är specifika för webb-API:er är det exponerade API:et och de exponerade scopea.

### <a name="application-id-uri-and-scopes"></a>Program-ID-URI och scope

Scope har vanligtvis `resourceURI/scopeName`formuläret . För Microsoft Graph har scopemen genvägar. Är till `User.Read` exempel en `https://graph.microsoft.com/user.read`genväg för .

Under appregistreringen måste du definiera dessa parametrar:

- Resursen URI
- Ett eller flera scope
- En eller flera approller

Som standard rekommenderar programregistreringsportalen att du `api://{clientId}`använder resursen URI . Denna URI är unik men inte läsbar. Om du ändrar URI:n kontrollerar du att det nya värdet är unikt.

Till klientprogram visas scope som *delegerade behörigheter* och approller visas som *programbehörigheter* för webb-API:et.

Scope visas också i samtyckesfönstret som visas för användare av din app. Så du måste ange motsvarande strängar som beskriver omfattningen:

- Som ses av en användare.
- Som framgår av en klientadministratör, som kan bevilja administratörsmedgivande.

### <a name="exposing-delegated-permissions-scopes"></a>Exponera delegerade behörigheter (scope)

1. Välj **Exponera ett API** i programregistreringen.
1. Välj **Lägg till omfång**.
1. Om du uppmanas till det godkänner`api://{clientId}`du det föreslagna tillämpnings-ID:t URI ( ) genom att välja **Spara och fortsätt**.
1. Ange dessa värden:
    - Välj **Scope-namn** och ange **access_as_user**.
    - Välj **Vem som kan medgivande** och se till att administratörer och **användare** är valda.
    - Välj **Visa namn för administratörsmedgivande** och ange Access **TodoListService som användare**.
    - Välj **Administratörsmedgivande beskrivning** och ange **Åtkomst till TodoListService Web API som användare**.
    - Välj **Visningsnamn för användarmedgivande** och ange **Access TodoListService som användare**.
    - Välj **Beskrivning av användarmedgivande** och ange **Åtkomst till TodoListService Web API som användare**.
    - Behåll **tillståndsvärdet** inställt **på Aktiverat**.
 1. Välj **Lägg till omfattning**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Om ditt webb-API anropas av en daemonapp

I det här avsnittet får du lära dig hur du registrerar ditt skyddade webb-API så att daemonappar kan anropa det på ett säkert sätt.

- Du deklarerar och exponerar endast *programbehörigheter* eftersom daemonappar inte interagerar med användare. Delegerade behörigheter skulle inte vara meningsfulla.
- Klientadministratörer kan kräva att Azure AD endast utfärdar webb-API-token till program som har registrerats för att komma åt en av API:ets programbehörigheter.

#### <a name="exposing-application-permissions-app-roles"></a>Exponera programbehörigheter (approller)

Om du vill visa programbehörigheter måste du redigera manifestet.

1. I ansökningsregistreringen för din ansökan väljer du **Manifest**.
1. Om du vill redigera `appRoles` manifestet hittar du inställningen och lägger till programroller. Rolldefinitionerna finns i följande JSON-block.
1. Lämna `allowedMemberTypes` inställd `"Application"` på bara.
1. Se `id` till att det är ett unikt GUID.
1. Kontrollera `displayName` och `value` inte innehålla blanksteg.
1. Spara manifestet.

Följande exempel visar innehållet `appRoles`i , `id` där värdet på kan vara en unik GUID.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Se till att Azure AD utfärdar token för ditt webb-API till endast tillåtna klienter

Webb-API:et söker efter approllen. Den här rollen är en programvaruutvecklares sätt att exponera programbehörigheter. Du kan också konfigurera Azure AD för att utfärda API-token endast till appar som klientadministratören godkänner för API-åtkomst.

Så här lägger du till den ökade säkerheten:

1. Gå till sidan Översikt **för** appen för din appregistrering.
1. Under **Hanterat program i lokal katalog**väljer du länken med namnet på din app. Etiketten för det här valet kan trunkeras. Du kan till exempel se **Hanterat program i ...**

   > [!NOTE]
   >
   > När du väljer den här länken går du till sidan **Översikt över företagsprogram.** Den här sidan är associerad med tjänstens huvudnamn för ditt program i klienten där du skapade den. Du kan gå till sidan för appregistrering genom att använda bakåtknappen i din webbläsare.

1. Välj sidan **Egenskaper** i avsnittet **Hantera** på programsidorna för företag.
1. Om du vill att Azure AD ska tillåta åtkomst till webb-API:et från endast vissa klienter anger du **Yes** **användartilldelning som krävs?**

   > [!IMPORTANT]
   >
   > Om du anger **användartilldelning krävs?** till **Ja,** Azure AD kontrollerar approlltilldelningar av en klient när den begär en webb-API-åtkomsttoken. Om klienten inte tilldelas några approller returnerar Azure AD felmeddelandet "invalid_client: AADSTS501051: \<Programnamnet\> har inte tilldelats\>en roll för webb-API:et \<".
   >
   > Om du behåller **användartilldelning krävs?** **No** Varje demonklient, vilket innebär att alla klienter som använder klientautentiseringsflödet, kan få en åtkomsttoken för API:et bara genom att ange sin målgrupp. Alla program kan komma åt API:et utan att behöva begära behörigheter för det.
   >
   > Men som förklaras i föregående avsnitt kan webb-API:et alltid verifiera att programmet har rätt roll, som är auktoriserad av klientadministratören. API:et utför den här verifieringen genom att verifiera att åtkomsttoken har ett rollanspråk och att värdet för det här anspråket är korrekt. I det föregående JSON-exemplet `access_as_application`är värdet .

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfiguration av appkod](scenario-protected-web-api-app-configuration.md)

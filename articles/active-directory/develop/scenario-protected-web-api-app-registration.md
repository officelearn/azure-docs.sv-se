---
title: Skyddad webb-API-app-registrering | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API och den information som du behöver för att registrera appen.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775153"
---
# <a name="protected-web-api-app-registration"></a>Skyddat webb-API: registrera appar

I den här artikeln förklaras information om app-registrering för ett skyddat webb-API.

Vanliga steg för att registrera en app finns i [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Godkänd token-version

Slut punkten för Microsoft Identity Platform kan utfärda v 1.0-token och v 2.0-token. Mer information om dessa token [finns i åtkomsttoken](access-tokens.md).

Den godkända token-versionen beror på de **konto typer som stöds** och du väljer när du skapar programmet.

- Om värdet för **konto typer som stöds** är **konton i en organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** är den godkända token-versionen v 2.0.
- Annars är den godkända token-versionen v 1.0.

När du har skapat programmet kan du fastställa eller ändra den godkända token-versionen genom att följa dessa steg:

1. I Azure Portal väljer du din app och väljer sedan **manifest**.
1. Hitta egenskapen **accessTokenAcceptedVersion** i manifestet. Egenskapens standardvärde är 2.
1. Värdet anger att Azure Active Directory (Azure AD) vilken token-version som webb-API: n accepterar.
    - Om värdet är 2 accepterar webb-API v 2.0-token.
    - Om värdet är **Null**accepterar webb-API: et v 1.0-token.
1. Om du har ändrat version för token väljer du **Spara**.

> [!NOTE]
> Webb-API: et anger vilken token-version den accepterar. När en klient begär en token för ditt webb-API från Microsoft Identity Platform (v 2.0) slut punkten, hämtar klienten en token som anger vilken token-version som webb-API: n accepterar.

## <a name="no-redirect-uri"></a>Ingen omdirigerings-URI

Webb-API: er behöver inte registrera en omdirigerings-URI eftersom ingen användare interaktivt loggas in.

## <a name="exposed-api"></a>Exponerat API

Andra inställningar som är speciella för webb-API: er är exponerade API och exponerade omfång.

### <a name="application-id-uri-and-scopes"></a>Program-ID-URI och omfång

Omfattningarna har vanligt vis formuläret `resourceURI/scopeName`. För Microsoft Graph har omfången genvägar. `User.Read` är till exempel en genväg till `https://graph.microsoft.com/user.read`.

När du registrerar appar måste du definiera följande parametrar:

- Resurs-URI
- Ett eller flera omfång
- En eller flera app-roller

Som standard rekommenderar program registrerings portalen att du använder resurs-URI `api://{clientId}`. Denna URI är unik men inte läslig. Om du ändrar URI måste du kontrol lera att det nya värdet är unikt.

För klient program visas omfattningar som *delegerade behörigheter* och app-roller visas som *program behörigheter* för ditt webb-API.

Omfattningar visas också i godkännande fönstret som presenteras för användare av din app. Du måste ange motsvarande strängar som beskriver omfånget:

- Som det setts av en användare.
- Som det visas av en innehavaradministratör, som kan ge administrativt medgivande.

### <a name="exposing-delegated-permissions-scopes"></a>Exponera delegerade behörigheter (omfattningar)

1. Välj **exponera ett API** i program registreringen.
1. Välj **Lägg till omfång**.
1. Om du uppmanas att godkänna den föreslagna program-ID-URI: n (`api://{clientId}`) genom att välja **Spara och fortsätt**.
1. Ange följande värden:
    - Välj **omfångs namn** och ange **access_as_user**.
    - Välj **vem som kan** godkänna och se till att **Administratörer och användare** är markerade.
    - Välj **visnings namn för administratörs medgivande** och ange **Access-TodoListService som en användare**.
    - Välj **Beskrivning av administratörs medgivande** och ange **åtkomst till TODOLISTSERVICE webb-API som en användare**.
    - Välj **användar medgivande visnings namn** och ange **Access-TodoListService som en användare**.
    - Välj **Beskrivning av användar medgivande** och ange **åtkomst till TODOLISTSERVICE webb-API som en användare**.
    - Behåll värdet för **tillstånd** inställt på **aktive rad**.
 1. Välj **Lägg till omfattning**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Om ditt webb-API anropas av en daemon-app

I det här avsnittet får du lära dig hur du registrerar ditt skyddade webb-API så att daemon-appar kan anropa det på ett säkert sätt.

- Du deklarerar och exponerar endast *program behörigheter* eftersom daemon-appar inte interagerar med användare. Delegerade behörigheter skulle inte vara begripliga.
- Klient organisations administratörer kan kräva att Azure AD bara utfärdar webb-API-token till program som har registrerat sig för att få åtkomst till ett av API: ns program behörigheter.

#### <a name="exposing-application-permissions-app-roles"></a>Exponera program behörigheter (app-roller)

Om du vill visa program behörigheter måste du redigera manifestet.

1. Välj **manifest**i program registreringen för ditt program.
1. Om du vill redigera manifestet letar du upp `appRoles`-inställningen och lägger till program roller. Roll definitionerna finns i följande exempel-JSON-block.
1. Lämna `allowedMemberTypes` inställt på endast `"Application"`.
1. Kontrol lera att `id` är ett unikt GUID.
1. Kontrol lera att `displayName` och `value` inte innehåller blank steg.
1. Spara manifestet.

I följande exempel visas innehållet i `appRoles`där värdet för `id` kan vara ett unikt GUID.

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

Webb-API: et söker efter app-rollen. Den här rollen är en program varu utvecklares sätt att exponera program behörigheter. Du kan också konfigurera Azure AD att bara utfärda API-token till appar som klient administratören godkänner för API-åtkomst.

För att lägga till denna ökade säkerhet:

1. Gå till **översikts** sidan för appen för att registrera din app.
1. Under **hanterat program i lokal katalog**väljer du länken med namnet på din app. Etiketten för det här urvalet kan vara trunkerad. Du kan till exempel se **hanterat program i...**

   > [!NOTE]
   >
   > När du väljer den här länken går du till **översikts sidan för företags program** . Den här sidan är kopplad till tjänstens huvud namn för ditt program i den klient där du skapade det. Du kan gå till registrerings sidan för appen genom att använda knappen tillbaka i webbläsaren.

1. Välj sidan **Egenskaper** i avsnittet **Hantera** på sidan företags program.
1. Om du bara vill att Azure AD ska tillåta åtkomst till ditt webb-API från vissa klienter måste du ange **användar tilldelning krävs?** till **Ja**.

   > [!IMPORTANT]
   >
   > Om du anger **användar tilldelning krävs?** till **Ja**kontrollerar Azure AD program roll tilldelningarna för en klient när den begär en webb-API-åtkomsttoken. Om klienten inte är tilldelad någon app-roll returnerar Azure AD fel meddelandet "invalid_client: AADSTS501051: program \<program namn\> tilldelas inte en roll för \<Web API\>".
   >
   > Om du behåller **användar tilldelningen krävs?** ange till **Nej**, det går inte att kontrol lera roll tilldelningarna för rollerna när en klient begär en åtkomsttoken för ditt webb-API. Alla daemon-klienter, vilket innebär att alla klienter som använder flödet för klientautentiseringsuppgifter, kan få en åtkomsttoken för API: et genom att ange dess mål grupp. Alla program kan komma åt API: et utan att begära behörigheter för det.
   >
   > Men enligt beskrivningen i föregående avsnitt, kan ditt webb-API alltid verifiera att programmet har rätt roll, vilket är auktoriserat av klient organisationens administratör. API: et utför den här kontrollen genom att verifiera att åtkomsttoken har ett roll anspråk och att värdet för det här anspråket är korrekt. I det föregående JSON-exemplet är värdet `access_as_application`.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [App Code-konfiguration](scenario-protected-web-api-app-configuration.md)

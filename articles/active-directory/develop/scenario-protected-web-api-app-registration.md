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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86c36661dd8b5d43417ca1d845eef5cdb146f1e9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962533"
---
# <a name="protected-web-api-app-registration"></a>Skyddat webb-API: registrera appar

I den här artikeln förklaras information om app-registrering för ett skyddat webb-API.

Se [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md) för vanliga steg för att registrera en app.

## <a name="accepted-token-version"></a>Godkänd token-version

Slut punkten för Microsoft Identity Platform kan utfärda två typer av tokens: v 1.0-token och v 2.0-token. Mer information om dessa token [finns i åtkomsttoken](access-tokens.md). Den godkända token-versionen beror på vilka **konto typer som stöds** och som du valde när du skapade ditt program:

- Om värdet för **konto typer som stöds** är **konton i en organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** , är den godkända token-versionen v 2.0.
- Annars är den godkända token-versionen v 1.0.

När du har skapat programmet kan du fastställa eller ändra den godkända token-versionen genom att följa dessa steg:

1. I Azure Portal väljer du din app och väljer sedan **manifestet** för din app.
2. Sök efter **"accessTokenAcceptedVersion"** i manifestet. Observera att värdet är **2**. Den här egenskapen anger Azure Active Directory (Azure AD) som webb-API: et accepterar v 2.0-token. Om värdet är **Null**är den godkända token-versionen v 1.0.
3. Om du har ändrat version för token väljer du **Spara**.

> [!NOTE]
> Webb-API: et anger vilken token-version (v 1.0 eller v 2.0) den accepterar. När klienter begär en token för webb-API: n från slut punkten för Microsoft Identity Platform (v 2.0) får de en token som visar vilken version som accepteras av webb-API: et.

## <a name="no-redirect-uri"></a>Ingen omdirigerings-URI

Webb-API: er behöver inte registrera en omdirigerings-URI eftersom ingen användare har loggat in interaktivt.

## <a name="expose-an-api"></a>Gör ett API tillgängligt

En annan inställning som är speciell för webb-API: er är det exponerade API: et och exponerade omfång.

### <a name="resource-uri-and-scopes"></a>Resurs-URI och omfång

Omfattningar är vanligt vis i form `resourceURI/scopeName`. För Microsoft Graph har Sök vägarna genvägar som `User.Read`. Den här strängen är en genväg för `https://graph.microsoft.com/user.read`.

Under registreringen av appen måste du definiera följande parametrar:

- Resurs-URI. Som standard rekommenderar program registrerings portalen att du använder `api://{clientId}`. Den här resurs-URI: n är unik, men den är inte läslig. Du kan ändra det, men se till att det nya värdet är unikt.
- Ett eller flera *omfång*. (Till klient program visas de som *delegerade behörigheter* för ditt webb-API.)
- En eller flera *app-roller*. (Till klient program visas de som *program behörigheter* för ditt webb-API.)

Omfattningarna visas också på skärmen för medgivande som visas för slutanvändare av appen. Därför måste du ange motsvarande strängar som beskriver omfånget:

- Som det visas av slutanvändaren.
- Som det visas av klient organisationens administratör, som kan ge administrativt medgivande.

### <a name="exposing-delegated-permissions-scopes"></a>Exponera delegerade behörigheter (omfattningar)

1. Välj avsnittet **exponera ett API** i program registreringen.
1. Välj **Lägg till omfång**.
1. Om du uppmanas att godkänna den föreslagna program-ID-URI: n (`api://{clientId}`) genom att välja **Spara och fortsätt**.
1. Ange följande parametrar:
      - Använd **access_as_user**för **omfattnings namn**.
      - För **vem som kan**godkänna, se till att **Administratörer och användare** är markerade.
      - I **visnings namn för administratörs medgivande**anger du **åtkomst TodoListService som en användare**.
      - I **Administratörs medgivande Beskrivning**anger **du åtkomst till TODOLISTSERVICE webb-API som en användare**.
      - I **användar medgivande visnings namn**anger du **Access-TodoListService som en användare**.
      - I **användar medgivande beskrivningen**, anger **du åtkomst till TODOLISTSERVICE webb-API som en användare**.
      - Behåll **tillstånds** inställningen **aktive rad**.
      - Välj **Lägg till omfattning**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Om ditt webb-API anropas av en daemon-app

I det här avsnittet får du lära dig hur du registrerar ditt skyddade webb-API så att det kan anropas säkert av daemon-appar.

- Du måste exponera *program behörigheter*. Du ska endast deklarera program behörigheter eftersom daemon-appar inte interagerar med användare, så delegerade behörigheter skulle inte vara begripliga.
- Klient organisations administratörer kan kräva Azure Active Directory (Azure AD) för att utfärda token för ditt webb-API endast till program som har registrerat sig för att få åtkomst till ett webb-API: s program behörigheter.

#### <a name="exposing-application-permissions-app-roles"></a>Exponera program behörigheter (app-roller)

Om du vill visa program behörigheter måste du redigera manifestet.

1. Välj **manifest**i program registreringen för ditt program.
1. Redigera manifestet genom att leta upp `appRoles`s inställningen och lägga till en eller flera program roller. Roll definitionen finns i följande exempel på JSON-block. Lämna `allowedMemberTypes` inställt på endast `"Application"`. Kontrol lera att `id` är ett unikt GUID och att `displayName` och `value` inte innehåller blank steg.
1. Spara manifestet.

I följande exempel visas innehållet i `appRoles`. (`id` kan vara valfritt unikt GUID.)

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

Webb-API: et söker efter app-rollen. (Det är det som utvecklare kan använda för att exponera program behörigheter.) Men du kan också konfigurera Azure AD för att utfärda en token för ditt webb-API till appar som har godkänts av klient administratören för att få åtkomst till ditt API. För att lägga till denna ökade säkerhet:

1. På sidan **Översikt över** appen för din app-registrering väljer du länken med namnet på din app under **hanterat program i lokal katalog**. Rubriken för det här fältet kan vara trunkerad. Du kan till exempel se **hanterat program i...**

   > [!NOTE]
   >
   > När du väljer den här länken går du till **översikts sidan för företags program** som är kopplad till tjänstens huvud namn för ditt program i klient organisationen där du skapade den. Du kan gå tillbaka till registrerings sidan för appen genom att använda knappen tillbaka i webbläsaren.

1. Välj sidan **Egenskaper** i avsnittet **Hantera** på sidan företags program.
1. Om du bara vill att Azure AD ska tillåta åtkomst till ditt webb-API från vissa klienter måste du ange **användar tilldelning krävs?** till **Ja**.

   > [!IMPORTANT]
   >
   > Om du anger **användar tilldelning krävs?** till **Ja**kontrollerar Azure AD program roll tilldelningarna för klienterna när de begär en åtkomsttoken för webb-API: et. Om klienten inte är tilldelad till några app-roller kommer Azure AD returnera fel `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Om du behåller **användar tilldelningen krävs?** ange till **Nej**, *det går inte att kontrol lera roll tilldelningarna för rollerna när en klient begär en ÅTKOMSTTOKEN för ditt webb-API*. Alla daemon-klienter (det vill säga alla klienter som använder klientens autentiseringsuppgifter Flow) kommer att kunna hämta en åtkomsttoken för API: et genom att ange dess mål grupp. Alla program kommer att kunna få åtkomst till API: et utan att begära behörigheter för det. Men ditt webb-API kan alltid, enligt beskrivningen i föregående avsnitt, kontrol lera att programmet har rätt roll (vilket tillåts av klient administratören). API: et utför den här kontrollen genom att verifiera att åtkomsttoken har ett roll anspråk och att värdet för det här anspråket är korrekt. (I vårt fall är värdet `access_as_application`.)

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-protected-web-api-app-configuration.md)

---
title: Begär åtkomsttoken i Azure Active Directory B2C | Microsoft Docs
description: Den här artikeln visar hur du konfigurera ett klientprogram och hämta en åtkomsttoken.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2043e0fc9fa63903073311856e7e8d31fb34c506
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015357"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: Begär åtkomsttoken

En åtkomsttoken (betecknas som **åtkomst\_token** i svar från Azure AD B2C) är en typ av säkerhetstoken som en klient kan använda för att få åtkomst till resurser som skyddas av en [auktoriseringsservern](active-directory-b2c-reference-protocols.md), till exempel ett webb-API. Åtkomsttoken visas i form av [JWTs](active-directory-b2c-reference-tokens.md) och innehåller information om den avsedda resursservern och beviljade behörigheter till servern. När du anropar resursservern måste åtkomsttoken finnas i HTTP-begäran.

Den här artikeln beskriver hur du konfigurerar ett klientprogram och ett webb-API för att få ett **åtkomst\_token**.

> [!NOTE]
> **Webb-API:ets kedjor (On-Behalf-Of) stöds inte av Azure AD B2C.**
>
> Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, båda skyddade med Azure AD B2C. Det här scenariot är vanligt i interna klienter som har en webb-API-serverdel, som i sin tur anropar en Microsoft-onlinetjänst som Azure AD Graph API.
>
> Det här scenariot med länkade webb-API:er kan användas med hjälp av "OAuth 2.0 JWT Bearer Credential grant", även kallat On-Behalf-Of-flöde. Dock är On-Behalf-Of-flödet inte implementerat i Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrera ett webb-API och publicera behörigheter

Innan du begär ett åtkomsttoken, måste du först registrera ett webb-API och publicera behörigheterna (hädanefter kallat scopes) som kan beviljas till klientprogrammet.

### <a name="register-a-web-api"></a>Registrera ett webb-API

1. På menyn för Azure AD B2C-funktioner på Azure-portalen klickar du på **Applications**.
2. Klicka på **+ Lägg till** högst upp på menyn.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso-API”.
4. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**.
5. Ange ett godtyckligt värde för den **Svarswebbadresser**. Ange till exempel `https://localhost:44316/`. Värdet har ingen betydelse eftersom ett API inte bör ta emot token direkt från Azure AD B2C.
6. Ange en **App-ID-URI**. Det här är identifieraren som används för ditt webb-API. Ange till exempel ”information” i rutan. Den **Appidentitets-URI** blir `https://{tenantName}.onmicrosoft.com/notes`.
7. Klicka på **Skapa** för att registrera ditt program.
8. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.

### <a name="publishing-permissions"></a>Publicera behörigheter

Scope, som är detsamma som behörighet, behövs när appen anropar ett API. Några exempel på scope är ”läsa” eller ”skriva”. Anta att du vill att din webb- eller native app ska kunna ”läsa” från ett API. Din app skulle anropa Azure AD B2C och begära en åtkomsttoken som ger åtkomst till scopet ”läsa”. För att Azure AD B2C ska kunna skapa en sådan åtkomsttoken behöver appen ha behörighet att läsa från det specifika API:et. För att göra detta behöver ditt API först publicera scopet "läsa".

1. I Azure AD B2C menyn **Applications** (program) öppnar du webb-API-programmet (”Contoso-API”).
2. Klicka på alternativet för **publicerade omfång**. Här definierar du behörigheterna (omfång) som kan beviljas till andra program.
3. Lägg till **omfångsvärden** vid behov (till exempel ”läsa”). Som standard definieras omfånget ”user_impersonation”. Du kan ignorera detta om du vill. Ange en beskrivning av detta scope i den **scopenamn** kolumn.
4. Klicka på **Spara**.

> [!IMPORTANT]
> **Scope Name** är beskrivningen av **Scope Value**. När du använder området, se till att använda **Scope Value**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Ge en nativ- eller webbapp behörigheter till ett webb-API

När ett API har konfigurerats för att publicera scopes, måste klientprogrammet beviljas dessa scopes via Azure-portalen.

1. Navigera till menyn **Applications** (program) i sektionen för Azure AD B2C-funktioner.
2. Registrera ett klientprogram ([webbapp](active-directory-b2c-app-registration.md) eller [inbyggd klient](active-directory-b2c-app-registration.md)) om du inte redan har en. Om du följer den här guiden som startpunkt när behöver du registrera ett klientprogram.
3. Klicka på **API-åtkomst**.
4. Klicka på **Lägg till**.
5. Välj ditt webb-API och scope (behörigheter) som du vill bevilja.
6. Klicka på **OK**.

> [!NOTE]
> Azure AD B2C kommer inte att be din klientapplikations användare om samtycke. I stället tillhandahålls alla medgivande av administratören, baserat på de behörigheter som konfigurerats mellan de program som beskrivs ovan. Om beviljandet av behörigheter för en applikation återkallas, kommer alla användare som tidigare hade möjlighet att få denna behörighet inte längre kunna göra detta.

## <a name="requesting-a-token"></a>Begära ett token

När du begär ett åtkomsttoken måste klientprogrammet ange de önskade behörigheterna i begärans **scope** parameter. För att exempelvis ange **Scope value** ”läsa” för ett API som har en **App ID URI** som är `https://contoso.onmicrosoft.com/notes`, skulle scopet vara `https://contoso.onmicrosoft.com/notes/read`. Nedan visas ett exempel på en begäran om en auktorisationskod till endpointen "/authorize".

> [!NOTE]
> Anpassade domäner stöds för närvarande inte tillsammans med åtkomsttoken. Du måste använda domänen tenantName.onmicrosoft.com i fråge-URL:et.

```
https://<tenantName>.b2clogin.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

För att få tillgång till flera behörigheter i samma begäran, kan du lägga till flera poster i en enda **scope** parameter, genom att avgränsa med blanksteg. Exempel:

Avkodad URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL-kodad:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Du kan begära fler områden (behörigheter) för en resurs än vad som har beviljats för klientprogrammet. När så är fallet lyckas anropet om minst en behörighet har beviljats. Detta resulterande **åtkomst\_token** får dess ”scp”-anspråk ifyllt enbart med de behörigheter som har beviljats.

> [!NOTE] 
> Att begära åtkomster för två olika webbresurser i samma begäran, stöds inte. Den här typen av begäran misslyckas.

### <a name="special-cases"></a>Specialfall

OpenID Connect-standarden specificerar flera särskilda "scope"-värden. Följande särskilda scopes representerar behörigheten ”åtkomst till användarens profil”:

* **openid**: detta begär en ID-token
* **offline\_åtkomst**: detta kräver ett uppdateringstoken (med hjälp av [Auth kod flöden](active-directory-b2c-reference-oauth-code.md)).

Om den `response_type` parameter i en `/authorize` begäran innehåller `token`, `scope` parameter måste innehålla minst en resurs omfång (annat än `openid` och `offline_access`) som kommer att beviljas. I annat fall den `/authorize` förfrågan avslutas med ett fel.

## <a name="the-returned-token"></a>Den returnerade token

Följande begäran kommer att finnas tillgängliga vid ett lyckat nyskapande av **åtkomst\_token** (antingen från "/bevilja` eller "/token" enpointen):

| Namn | Begäran | Beskrivning |
| --- | --- | --- |
|Målgrupp |`aud` |Det **application ID** för den enda resurs som detta token ger åtkomst till. |
|Scope |`scp` |Behörigheterna för resursen. Flera beviljade behörigheter kommer att avgränsas med blanksteg. |
|Behörig part |`azp` |Det **program-ID** hos klientprogrammet som initierade begäran. |

När ditt API tar emot ett **åtkomst\_token**, måste det [Validera detta token](active-directory-b2c-reference-tokens.md) för att bevisa att detta token är autentiskt och har rätt att göra anspråk (claims).

Vi är alltid öppna för återkoppling och förslag! Om du har problem med det här avsnittet kan du publicera en fråga på Stack Overflow med taggen [”azure-ad-b2c”](https://stackoverflow.com/questions/tagged/azure-ad-b2c). För förfrågningar om ny funktionalitet, vänligen lägg till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Nästa steg

* Skapa ett webb-API med hjälp av [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Skapa ett webb-API med hjälp av [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

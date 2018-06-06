---
title: Begär åtkomst-token i Azure Active Directory B2C | Microsoft Docs
description: Den här artikeln visar hur du konfigurera ett klientprogram och få en åtkomst-token.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 92c4544a0dee4c875b1c802a8c4d77d48bfb94ef
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711075"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: Begär åtkomst-token

En åtkomst-token (betecknas som **åtkomst\_token** i svar från Azure AD B2C) är en typ av säkerhetstoken som en klient kan använda för att komma åt resurser som skyddas av en [auktorisering server](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), till exempel ett webb-API. Åtkomsttoken representeras som [JWTs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) och innehåller information om den avsedda resursservern och beviljats behörighet till servern. När du anropar resursservern måste åtkomsttoken finnas i HTTP-begäran.

Den här artikeln beskrivs hur du konfigurerar ett klientprogram och webb-API för att få en **åtkomst\_token**.

> [!NOTE]
> **Webb-API kedjor (On-Behalf-Of) stöds inte av Azure AD B2C.**
>
> Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, både skyddas av Azure AD B2C. Det här scenariot är vanligt i interna klienter som har en webb-API-serverdel, som anropar en Microsoft online service som Azure AD Graph API.
>
> Det här scenariot med länkade webb-API kan användas med hjälp av OAuth 2.0 JWT ägar autentiseringsuppgifter bevilja, även kallat On-Behalf-Of-flöde. Dock implementeras On-Behalf-Of-flöde för närvarande inte i Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrera ett webb-API och publicera behörigheter

Innan du begär en åtkomst-token, måste du först registrera ett webb-API och publicera behörigheter (scope) som kan beviljas för klientprogrammet.

### <a name="register-a-web-api"></a>Registrera ett webb-API

1. Klicka på menyn för Azure AD B2C-funktioner på Azure portal **program**.
1. Klicka på **+ Lägg till** längst upp på menyn.
1. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel ange ”Contoso-API”.
1. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**.
1. Ange ett godtyckligt värde för den **Reply URL: er**. Ange till exempel `https://localhost:44316/`. Värdet har ingen betydelse eftersom en API inte bör emot token direkt från Azure AD B2C.
1. Ange en **App-ID-URI**. Det här är identifieraren som används för ditt webb-API. Ange till exempel ”information” i rutan. Den **App-ID URI** blir `https://{tenantName}.onmicrosoft.com/notes`.
1. Klicka på **Skapa** för att registrera ditt program.
1. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.

### <a name="publishing-permissions"></a>Publishing behörigheter

Scope, vilket är analoga med behörigheter krävs när din app anropar en API. ”Läsa” eller ”write” är några exempel på scope. Anta att du vill att webbplatsen eller inbyggda appen ”läsa” från en API. Appen skulle anropa Azure AD B2C och begär en åtkomst-token som ger tillgång till området ”läsa”. Appen måste ha behörigheten ”läsa” från det specifika API: et för Azure AD B2C att generera en åtkomsttoken. Om du vill göra detta behöver din API först publicera området ”läsa”.

1. I Azure AD B2C **program** menyn Öppna webb-API-program (”Contoso-API”).
1. Klicka på alternativet för **publicerade omfång**. Här definierar du behörigheterna (omfång) som kan beviljas till andra program.
1. Lägg till **Scope-värden** vid behov (till exempel ”läsa”). Som standard definieras omfånget ”user_impersonation”. Du kan ignorera detta om du vill. Ange en beskrivning för detta scope i den **scopenamn** kolumn.
1. Klicka på **Spara**.

> [!IMPORTANT]
> Den **scopenamn** är beskrivningen av den **Scope-värde**. När du använder omfång, se till att använda den **Scope-värde**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Ge ett inbyggt eller web app-behörigheter till ett webb-API

När en API är konfigurerad för att publicera scope, måste klientprogrammet beviljas sådana omfattningar via Azure portal.

1. Navigera till den **program** funktioner Azure AD B2C-menyn.
1. Registrera ett klientprogram ([webbapp](active-directory-b2c-app-registration.md#register-a-web-app) eller [native client](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) om du inte redan har en. Om du följer den här guiden som startpunkt när behöver du registrera ett klientprogram.
1. Klicka på **API-åtkomst**.
1. Klicka på **Lägg till**.
1. Välj ditt webb-API och scope (behörigheter) som du vill bevilja.
1. Klicka på **OK**.

> [!NOTE]
> Azure AD B2C ombeds du inte klienten programanvändare sitt samtycke. I stället som alla medgivande admin, baserat på de behörigheter som har konfigurerats mellan de program som beskrivs ovan. En bevilja behörighet för ett program har återkallats, kommer alla användare som tidigare har kunnat få behörigheten inte längre att göra detta.

## <a name="requesting-a-token"></a>Begära ett token

När du begär en åtkomst-token klientprogrammet måste ange de önskade behörigheterna i den **omfång** parameter för begäran. Till exempel för att ange den **Scope-värde** ”läsa” för API: N som har den **App-ID URI** av `https://contoso.onmicrosoft.com/notes`, omfattningen skulle vara `https://contoso.onmicrosoft.com/notes/read`. Nedan visas ett exempel på en begäran om tillstånd att den `/authorize` slutpunkt.

> [!NOTE]
> Anpassade domäner kan inte användas tillsammans med åtkomsttoken. Du måste använda tenantName.onmicrosoft.com domänen i begärande-URL.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Du får tillgång till flera behörigheter i samma begäran, du kan lägga till flera poster i enda **omfång** parameter, avgränsade med blanksteg. Exempel:

Avkoda URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL-kodade:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Du kan begära flera scope (behörigheter) för en resurs än vad beviljas för klientprogrammet. När så är fallet lyckas anropet om minst en behörighet. Det resulterande **åtkomst\_token** har dess ”scp”-anspråk med de behörigheter som har beviljats.

> [!NOTE] 
> Vi stöder inte begär behörighet mot två olika webbresurser i samma begäran. Den här typen av förfrågan misslyckas.

### <a name="special-cases"></a>Särskilda fall

Standard OpenID Connect anger flera särskilda ”omfång”-värden. Följande särskilda scope representerar behörigheten ”åtkomst till användarens profil”:

* **openid**: detta begär en ID-token
* **offline\_åtkomst**: detta begär en uppdateringstoken (med hjälp av [Auth kod flödar](active-directory-b2c-reference-oauth-code.md)).

Om den `response_type` parameter i en `/authorize` begäran innehåller `token`, `scope` parameter måste innehålla minst en resurs omfång (utom `openid` och `offline_access`) som kan beviljas. I annat fall den `/authorize` begäran avslutas med ett fel.

## <a name="the-returned-token"></a>Returnerade token

I har minted **åtkomst\_token** (från antingen den `/authorize` eller `/token` endpoint), följande anspråk kommer att finnas:

| Namn | Begär | Beskrivning |
| --- | --- | --- |
|Målgrupp |`aud` |Den **program-ID** av enskild resurs som token beviljar åtkomst till. |
|Omfång |`scp` |Behörigheterna för resursen. Flera beviljade behörigheter ska avgränsas med blanksteg. |
|Auktoriserad part |`azp` |Den **program-ID** av klientprogram som initierade begäran. |

När din API tar emot den **åtkomst\_token**, den måste [Validera token](active-directory-b2c-reference-tokens.md) att bevisa att token är giltigt och har rätt anspråk.

Vi är alltid öppna för feedback och förslag! Om du har problem med det här avsnittet inlägg på Stack Overflow med taggen ['azure-ad b2c-](https://stackoverflow.com/questions/tagged/azure-ad-b2c). För funktionsbegäranden, lägga till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Nästa steg

* Skapa ett web API med [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Skapa ett web API med [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

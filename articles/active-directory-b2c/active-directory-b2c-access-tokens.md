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

Den här artikeln beskrivs hur du konfigurerar ett klientprogram och webb-API för att få en **åtkomst\_token**.

> [!NOTE]
> **Webb-API: et kedjor (On-Behalf-Of) stöds inte av Azure AD B2C.**
>
> Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, båda skyddade med Azure AD B2C. Det här scenariot är vanligt i interna klienter som har ett webb-API-serverdel, som i sin tur anropar en Microsoft-onlinetjänst som Azure AD Graph API.
>
> Det här scenariot med länkade webb-API kan användas med hjälp av OAuth 2.0 JWT-Ägarautentisering Autentiseringsuppgiften bevilja, även kallat On-Behalf-Of-flöde. Dock är On-Behalf-Of-flöde inte implementerat i Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrera ett webb-API och publicera behörigheter

Innan du begär en åtkomsttoken, måste du först registrera ett webb-API och publicera behörigheterna (omfång) som kan beviljas till klientprogrammet.

### <a name="register-a-web-api"></a>Registrera ett webb-API

1. På menyn för Azure AD B2C-funktioner på Azure portal klickar du på **program**.
2. Klicka på **+ Lägg till** högst upp på menyn.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso-API”.
4. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**.
5. Ange ett godtyckligt värde för den **Svarswebbadresser**. Ange till exempel `https://localhost:44316/`. Värdet har ingen betydelse eftersom ett API inte bör ta emot token direkt från Azure AD B2C.
6. Ange en **App-ID-URI**. Det här är identifieraren som används för ditt webb-API. Ange till exempel ”information” i rutan. Den **Appidentitets-URI** blir `https://{tenantName}.onmicrosoft.com/notes`.
7. Klicka på **Skapa** för att registrera ditt program.
8. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.

### <a name="publishing-permissions"></a>Publicera behörigheter

Scope, som är detsamma som behörighet, behövs när appen anropar ett API. Några exempel på scope är ”läsa” eller ”skriva”. Anta att du vill att dina webb- eller inbyggd app att ”läsa” från ett API. Din app skulle anropa Azure AD B2C och begär en åtkomsttoken som ger åtkomst till området ”läsa”. Appen måste ha behörighet att ”läsa” från den specifika API: et för Azure AD B2C att skapa en åtkomsttoken. Detta gör måste ditt API först publicera området ”Läs”.

1. I Azure AD B2C **program** menyn Öppna webb-API-program (”Contoso-API”).
2. Klicka på alternativet för **publicerade omfång**. Här definierar du behörigheterna (omfång) som kan beviljas till andra program.
3. Lägg till **omfångsvärden** vid behov (till exempel ”läsa”). Som standard definieras omfånget ”user_impersonation”. Du kan ignorera detta om du vill. Ange en beskrivning av detta scope i den **scopenamn** kolumn.
4. Klicka på **Spara**.

> [!IMPORTANT]
> Den **scopenamn** är beskrivningen av den **Omfattningsvärde**. När du använder området, se till att använda den **Omfattningsvärde**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Ge ett inbyggt eller web app-behörigheter till ett webb-API

När ett API har konfigurerats för att publicera scope, måste klientprogrammet beviljas sådana omfattningar via Azure portal.

1. Navigera till den **program** menyn för Azure AD B2C-funktioner.
2. Registrera ett klientprogram ([webbapp](active-directory-b2c-app-registration.md) eller [inbyggd klient](active-directory-b2c-app-registration.md)) om du inte redan har en. Om du följer den här guiden som startpunkt när behöver du registrera ett klientprogram.
3. Klicka på **API-åtkomst**.
4. Klicka på **Lägg till**.
5. Välj ditt webb-API och scope (behörigheter) som du vill bevilja.
6. Klicka på **OK**.

> [!NOTE]
> Azure AD B2C ombeds du inte klienten programanvändare sitt samtycke. I stället tillhandahålls alla medgivande av administratören, baserat på de behörigheter som konfigurerats mellan de program som beskrivs ovan. En behörighetsbeviljande för ett program har återkallats, längre alla användare som har tidigare kan få behörighet inte kunna göra detta.

## <a name="requesting-a-token"></a>Begära ett token

När du begär en åtkomsttoken klientprogrammet måste ange de önskade behörigheterna i den **omfång** parameter för begäran. Till exempel vill ange den **Omfattningsvärde** ”läsa” för API som har den **Appidentitets-URI** av `https://contoso.onmicrosoft.com/notes`, omfattningen skulle vara `https://contoso.onmicrosoft.com/notes/read`. Nedan visas ett exempel på en begäran om godkännande för kod till den `/authorize` slutpunkt.

> [!NOTE]
> Anpassade domäner stöds för närvarande inte tillsammans med åtkomsttoken. Du måste använda din tenantName.onmicrosoft.com domän i fråge-URL.

```
https://<tenantName>.b2clogin.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Du får tillgång till flera behörigheter i samma begäran, kan du lägga till flera poster i en enda **omfång** parameter, avgränsade med blanksteg. Exempel:

Avkoda-URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL-kodad:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Du kan begära fler områden (behörigheter) för en resurs än vad som har beviljats för klientprogrammet. När så är fallet lyckas anropet om minst en behörighet har beviljats. Den resulterande **åtkomst\_token** har dess ”scp”-anspråk som fylls i med de behörigheter som har beviljats.

> [!NOTE] 
> Vi stöder inte begär åtkomst mot två olika webbresurser i samma begäran. Den här typen av begäran misslyckas.

### <a name="special-cases"></a>Specialfall

OpenID Connect-standarden specificerar flera särskilda ””-omfångsvärden. Följande särskilda omfattningar representerar behörigheten ”åtkomst till användarens profil”:

* **openid**: detta begär en ID-token
* **offline\_åtkomst**: detta begär en uppdateringstoken (med hjälp av [Auth kod flödar](active-directory-b2c-reference-oauth-code.md)).

Om den `response_type` parameter i en `/authorize` begäran innehåller `token`, `scope` parameter måste innehålla minst en resurs omfång (annat än `openid` och `offline_access`) som kommer att beviljas. I annat fall den `/authorize` förfrågan avslutas med ett fel.

## <a name="the-returned-token"></a>Den returnerade token

I en har minted **åtkomst\_token** (antingen från den `/authorize` eller `/token` slutpunkt), följande anspråken kommer att finnas:

| Namn | Begär | Beskrivning |
| --- | --- | --- |
|Målgrupp |`aud` |Den **program-ID** av den enda som token ger åtkomst till. |
|Omfång |`scp` |Behörigheterna för resursen. Flera beviljade behörigheter ska avgränsas med blanksteg. |
|Auktoriserade part |`azp` |Den **program-ID** på det klientprogram som initierade begäran. |

När ditt API tar emot den **åtkomst\_token**, den måste [Validera token](active-directory-b2c-reference-tokens.md) att bevisa att token är autentisk och har rätt anspråken.

Vi är alltid öppen för feedback och förslag! Om du har problem med det här avsnittet kan du publicera på Stack Overflow med taggen [”azure-ad-b2c”](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Lägga till dem i för funktionsförfrågningar om [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Nästa steg

* Skapa en webb-API med hjälp av [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Skapa en webb-API med hjälp av [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

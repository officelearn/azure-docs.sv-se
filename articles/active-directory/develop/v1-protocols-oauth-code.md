---
title: Förstå OAuth 2.0-auktoriseringskodflödet i Azure AD
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att bevilja åtkomst till webbprogram och webb-API: er i din klient med Azure Active Directory och OAuth 2.0.'
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/5/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2598bb4deef0c7dae9f5df558ec1054ad02fb2f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297061"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Bevilja åtkomst till Azure Active Directory-webbprogram med hjälp av kod grant-flöde för OAuth 2.0

Azure Active Directory (Azure AD) används OAuth 2.0 att bevilja åtkomst till webbprogram och webb-API: er i Azure AD-klienten. Den här handboken är språkoberoende och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av våra [bibliotek med öppen källkod](active-directory-authentication-libraries.md).

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.1). Den används för att utföra autentisering och auktorisering i de flesta programtyper, bland annat web apps och internt installerade appar.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Flöde för OAuth 2.0-auktorisering

På en hög nivå ut hela auktorisering flödet för ett program lite så här:

![Kodflöde för OAuth-autentisering](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod

Auktoriseringskodsflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. I den här begäran anger klienten de behörigheter som krävs för att läsa från användaren. Du kan hämta slutpunkten för OAuth 2.0-auktorisering för din klient genom att välja **appregistreringar > slutpunkter** i Azure-portalen.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| tenant |obligatorisk |Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |obligatorisk |Program-ID som tilldelats din app när du registrerade med Azure AD. Du hittar du i Azure Portal. Klicka på **Azure Active Directory** i sidopanelen tjänster klickar du på **appregistreringar**, och välj programmet. |
| response_type |obligatorisk |Måste innehålla `code` för auktoriseringskodsflödet. |
| redirect_uri |Rekommenderas |Redirect_uri för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av redirect_uris som du registrerade i portalen, men det måste vara url-kodas. För interna & mobila appar, bör du använda standardvärdet för `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Kan vara `query`, `fragment`, eller `form_post`. `query` innehåller koden som en frågesträngsparameter på din omdirigerings-URI. Om du ska få en ID-token med hjälp av det implicita flödet, du kan inte använda `query` som angetts på den [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du ska få enbart koden, kan du använda `query`, `fragment`, eller `form_post`. `form_post` Kör ett INLÄGG som innehåller koden omdirigerings-URI. Standardvärdet är `query` för ett kodflöde.  |
| state |Rekommenderas |Ett värde i begäran som returneras också i token-svaret. Ett slumpmässigt genererat unikt värde som normalt används för [att förhindra attacker med förfalskning av begäran](https://tools.ietf.org/html/rfc6749#section-10.12). Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| resurs | Rekommenderas |App-ID URI för mål-webb-API (säker resurs). För att hitta URI: N för App-ID i Azure Portal, klickar du på **Azure Active Directory**, klickar du på **programregistreringar**, Öppna programmets **inställningar** sidan och klicka sedan på  **Egenskaper för**. Det kan också vara en extern resurs som `https://graph.microsoft.com`. Detta är nödvändigt i en av auktorisering eller tokenbegäranden. Om du vill kontrollera att färre authentication placeras anvisningarna den begäran om godkännande för att kontrollera medgivande tas emot från användaren. |
| omfång | **ignoreras** | För v1 Azure AD-appar, scope statiskt konfigureras i Azure Portal under programmen **inställningar**, **nödvändiga behörigheter**. |
| fråga |valfri |Ange vilken typ av interaktion från användaren som krävs.<p> Giltiga värden är: <p> *login*: Användaren ska uppmanas att autentiseras på nytt. <p> *select_account*: Användaren uppmanas att välja ett konto att avbryta enkel inloggning på. Användaren kan välja ett befintligt inloggade konto, ange sina autentiseringsuppgifter för ett konto som är sparade eller välja att använda ett annat konto helt och hållet. <p> *godkänna*: Användargodkännande har beviljats, men behöver uppdateras. Användaren ska uppmanas att godkänna. <p> *admin_consent*: En administratör ska uppmanas att ge samtycke åt alla användare i organisationen |
| login_hint |valfri |Kan användas för att fylla förväg adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sina användarnamn i tid. Appar som ofta använda den här parametern under omautentisering som redan har extraherats användarnamnet från en tidigare logga in med den `preferred_username` anspråk. |
| domain_hint |valfri |Anger ett tips om klient eller domän som användaren ska använda för att logga in. Värdet för domain_hint är en registrerad domän för klienten. Om klienten är federerat till en lokal katalog, omdirigerar AAD till den angivna innehavare federationsservern. |
| code_challenge_method | Rekommenderas    | Den metod som används för att koda den `code_verifier` för den `code_challenge` parametern. Kan vara något av `plain` eller `S256`. Om undantas identifieras `code_challenge` antas vara klartext om `code_challenge` ingår. Azure AAD v1.0 stöder både `plain` och `S256`. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Rekommenderas    | Används för att skydda auktoriseringsbeviljanden kod via Proof-nyckel för kod Exchange (PKCE) från en intern eller offentlig klient. Krävs om `code_challenge_method` ingår. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Om användaren är en del av en organisation, kan en administratör för organisationen godkänna eller neka för användarens räkning eller Tillåt användaren att godkänna. Användaren har möjlighet att godkänna endast när administratören tillåter den.
>
>

Nu uppmanas användaren att ange sina autentiseringsuppgifter och godkänna de behörigheter som begärdes av appen i Azure Portal. När användaren autentiserar och ger ditt medgivande, Azure AD skickar ett svar till din app på den `redirect_uri` adressen i din begäran med kod.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar kan se ut så här:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beskrivning |
| --- | --- |
| admin_consent |Värdet är SANT om en administratör har godkänt att medgivandetext för begäran. |
| Kod |Auktoriseringskod som programmet har begärt. Programmet kan använda Auktoriseringskoden för att begära en åtkomsttoken för målresursen. |
| session_state |Ett unikt värde som identifierar den aktuella användarsessionen. Det här värdet är ett GUID, men ska behandlas som ett täckande värde som har skickats utan undersökning. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Det är bra för programmet för att kontrollera att värdena i begäran och svar är identiska innan du använder svaret. Detta hjälper dig för att identifiera [skriptkörning begär förfalskning (CSRF)-attacker](https://tools.ietf.org/html/rfc6749#section-10.12) mot klienten. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att programmet kan hantera dem på lämpligt sätt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felvärde kod som definierats i avsnittet 5.2 av den [OAuth 2.0 auktorisering Framework](https://tools.ietf.org/html/rfc6749). I nästa tabell beskrivs felkoder som returnerar Azure AD. |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsedd att vara slutanvändarens eget. |
| state |Statusvärdet är ett slumpmässigt genererat, icke återanvändas värde som skickas i begäran och returnerades i svaret att förhindra attacker med förfalskning (CSRF) av begäran. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slutpunkt-auktoriseringsfel
I följande tabell beskrivs olika felkoder som kan returneras i de `error` -parametern för felsvaret.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis påträffades under första testningen. |
| unauthorized_client |Klientprogrammet har inte behörighet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| access_denied |Resursägaren nekas godkännande |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om inte användaren godkänner. |
| unsupported_response_type |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis påträffades under första testningen. |
| server_error |Ett oväntat fel inträffade på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. |Detta anger resursen om den finns, inte har konfigurerats på klienten. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Använda Auktoriseringskoden för att begära en åtkomsttoken
Nu när du har skaffat en auktoriseringskod och ha beviljats behörighet av användaren, kan du lösa in koden för en åtkomsttoken till önskad resurs genom att skicka en POST-begäran till den `/token` slutpunkt:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| tenant |obligatorisk |Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |obligatorisk |Program-Id som tilldelats din app när du registrerade med Azure AD. Du hittar du i Azure-portalen. Program-Id visas i inställningarna för registreringen. |
| _typ av beviljande |obligatorisk |Måste vara `authorization_code` för auktoriseringskodsflödet. |
| Kod |obligatorisk |Den `authorization_code` som du hämtade i föregående avsnitt |
| redirect_uri |obligatorisk | En `redirect_uri`registrerad i klientprogrammet. |
| client_secret |krävs för webbappar är inte tillåtet för offentliga klienter |Programhemlighet som du skapade i Azure Portal för din app under **nycklar**. Det kan inte användas i en inbyggd app (offentlig klient), eftersom client_secrets inte lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er (alla konfidentiella klienter) som har möjlighet att lagra den `client_secret` på ett säkert sätt på serversidan. Client_secret ska URL-kodat innan de skickas. |
| resurs | Rekommenderas |App-ID URI för mål-webb-API (säker resurs). För att hitta URI: N för App-ID i Azure Portal, klickar du på **Azure Active Directory**, klickar du på **programregistreringar**, Öppna programmets **inställningar** sidan och klicka sedan på  **Egenskaper för**. Det kan också vara en extern resurs som `https://graph.microsoft.com`. Detta är nödvändigt i en av auktorisering eller tokenbegäranden. Om du vill kontrollera att färre authentication placeras anvisningarna den begäran om godkännande för att kontrollera medgivande tas emot från användaren. Om de finns i både auktoriseringsbegäran och tokenbegäran resursen ' parametrar måste matcha. | 
| code_verifier | valfri | Den samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE har använts i auktoriseringsbegäran kod bevilja. Mer information finns i den [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

För att hitta URI: N för App-ID i Azure Portal, klickar du på **Azure Active Directory**, klickar du på **programregistreringar**, Öppna programmets **inställningar** sidan och klicka sedan på  **Egenskaper för**.

### <a name="successful-response"></a>Lyckat svar
Azure AD-returnerar en [åtkomsttoken](access-tokens.md) vid ett lyckat svar. Om du vill minimera nätverksanrop från klientprogrammet och deras associerade svarstiden bör klientprogrammet cacheåtkomsttokens för livslängd för token som anges i OAuth 2.0-svar. För att fastställa livslängd för token, kan du använda antingen den `expires_in` eller `expires_on` parametervärden.

Om ett webb-API-resursen returnerar en `invalid_token` felkoden detta kan tyda på att resursen har fastställt att token har upphört att gälla. Om klienten och resurs klockan tiderna är olika (kallas en ”tidssnedställning”), resursen kan överväga att token har upphört att gälla innan token har raderats från klientens cacheminne. Om detta inträffar kan du avmarkera token från cacheminnet, även om det är fortfarande inom livslängden beräknade.

Ett lyckat svar kan se ut så här:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda [åtkomsttoken](access-tokens.md) som en signerad JSON Web Token (JWT). Appen kan använda den här token för att autentisera till den säkra resursen, till exempel ett webb-API. |
| token_type |Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är ägar. Läs mer om ägar-token, [OAuth2.0 auktorisering Framework: Ägar-Token användning (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| expires_on |Den tid då den åtkomst-token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. Det här värdet används för att fastställa livslängd för cachelagrade token. |
| resurs |App-ID URI för webb-API (säker resurs). |
| omfång |Personifiering behörigheter till klientprogrammet. Standardbehörigheten är `user_impersonation`. Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. |
| refresh_token |OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare åtkomst-token när den aktuella åtkomst-token upphör att gälla. Uppdatera token är långlivade och kan användas för att behålla åtkomst till resurser i längre tid. |
| id_token |En osignerad JSON Web Token (JWT) som representerar en [ID-token](id-tokens.md). Appen kan base64Url avkoda segmenten i den här token för att begäraninformation om den användare som loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. |

Mer information om JSON web token finns i den [JWT IETF utkast till en specifikation](https://go.microsoft.com/fwlink/?LinkId=392344).   Mer information om `id_tokens`, finns i den [v1.0 OpenID Connect-flöde](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Felsvar
Utfärdande endpoint felen är HTTP-felkoder, eftersom klienten anropar utfärdande slutpunkten direkt. Utöver HTTP-statuskod returnerar Azure AD-slutpunkten för utfärdande också ett JSON-dokument med objekt som beskriver felet.

En exempel-felsvar kan se ut så här:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa i diagnostik. |
| tidsstämpel |Den tid då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan hjälpa i diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa i diagnostik för komponenter. |

#### <a name="http-status-codes"></a>HTTP-statuskoder
I följande tabell visas de HTTP-statuskoder som utfärdande-slutpunkten returnerar. Felkoden är tillräckliga för att beskriva svaret i vissa fall, men om det finns fel kan du behöva parsa tillhörande JSON-dokumentet och undersök dess felkoden.

| HTTP-kod | Beskrivning |
| --- | --- |
| 400 |Standard HTTP-kod. Används i de flesta fall och är vanligtvis på grund av en felaktig begäran. Åtgärda och skicka begäran igen. |
| 401 |Autentiseringen misslyckades. Begäran saknar till exempel client_secret-parametern. |
| 403 |Auktoriseringen misslyckades. Exempelvis kan saknar användaren behörighet att komma åt resursen. |
| 500 |Ett internt fel har uppstått på tjänsten. Gör om begäran. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för tokenslutpunkt fel
| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran igen |
| invalid_grant |Auktoriseringskoden är ogiltigt eller har upphört att gälla. |Testa en ny begäran till den `/authorize` slutpunkt |
| unauthorized_client |Den autentiserade klienten har inte behörighet att använda den här beviljandetypen för auktorisering. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| invalid_client |Klientautentiseringen misslyckades. |Med klientens autentiseringsuppgifter är inte giltiga. Du kan åtgärda genom uppdaterar programadministratören autentiseringsuppgifterna. |
| unsupported_grant_type |Auktoriseringsservern stöder inte beviljandetypen för auktorisering. |Ändra beviljandetyp i begäran. Den här typen av fel ska ske under utveckling och identifieras under första testningen. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. |Detta anger resursen om den finns, inte har konfigurerats på klienten. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| interaction_required |Begäran kräver interaktion från användaren. Till exempel krävs ett steg för ytterligare autentisering. | Försök igen med en interaktiv auktoriseringsbegäran för samma resurs i stället för en icke-interaktiv begäran. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |

## <a name="use-the-access-token-to-access-the-resource"></a>Använd åtkomsttoken för åtkomst till resursen
Nu när du har skaffat har en `access_token`, du kan använda token i begäranden till webb-API: er, genom att inkludera den i den `Authorization` rubrik. Den [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specifikationen förklarar hur du använder ägar-token i HTTP-begäranden att komma åt skyddade resurser.

### <a name="sample-request"></a>Exempelbegäran
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Felsvar
Skyddade resurser som implementerar RFC 6750 problemet HTTP-statuskoder. Om begäran innehåller inte autentiseringsuppgifter eller saknar token, svaret innehåller en `WWW-Authenticate` rubrik. När en begäran inte svarar resursservern med HTTP-statuskoden och en felkod.

Följande är ett exempel på ett misslyckat svar när klientbegäran inte omfattar ägartoken:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Fel parametrar
| Parameter | Beskrivning |
| --- | --- |
| authorization_uri |URI: N (fysiska slutpunkt) av auktoriseringsservern. Det här värdet används också som en lookup-nyckel för att få mer information om servern från en slutpunkt för identifiering. <p><p> Klienten måste verifiera att auktoriseringsservern är betrodd. När resursen skyddas av Azure AD, det är tillräckligt för att verifiera att URL: en börjar med https://login.microsoftonline.com eller ett annat värdnamn som har stöd för Azure AD. En klientspecifik resurs ska alltid returnera en klientspecifik auktorisering URI. |
| fel |Ett felvärde kod som definierats i avsnittet 5.2 av den [OAuth 2.0 auktorisering Framework](https://tools.ietf.org/html/rfc6749). |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsedd att vara slutanvändarens eget. |
| resource_id |Returnerar den unika identifieraren för resursen. Klientprogrammet kan använda den här identifieraren som värde för den `resource` parametern när det begär en token för resursen. <p><p> Det är viktigt för klientprogram att verifiera det här värdet, annars en skadlig tjänsten kanske kan orsaka en **höjning av privilegier** attack <p><p> Den rekommenderade strategin för att förhindra ett angrepp är att kontrollera att den `resource_id` matchar basen för web API-URL som används. Till exempel om https://service.contoso.com/data används, den `resource_id` kan vara htttps://service.contoso.com/. Klientprogrammet måste avvisa en `resource_id` som börjar inte med den grundläggande Webbadressen om det inte finns en tillförlitligt alternativa sätt att kontrollera id. |

#### <a name="bearer-scheme-error-codes"></a>Felkoder för ägar-schema
Specifikationen RFC 6750 definierar följande fel för resurser som använder WWW-autentisera rubrik och ägar-schema i svaret.

| HTTP-statuskod | Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- | --- |
| 400 |invalid_request |Begäran är inte giltig. Det kan exempelvis en parameter saknas eller använda samma parameter två gånger. |Åtgärda felet och försök begäran. Den här typen av fel ska ske under utveckling och upptäcks i första testningen. |
| 401 |invalid_token |Åtkomsttoken är saknas, är ogiltig eller har återkallats. Värdet för parametern error_description innehåller ytterligare information. |Begär en ny token från auktoriseringsservern. Om den nya token misslyckas, har ett oväntat fel uppstått. Skicka ett felmeddelande till användaren och försök igen efter slumpmässiga fördröjningar. |
| 403 |insufficient_scope |Åtkomsttoken innehåller inte de personifiering behörigheter som krävs för åtkomst till resursen. |Skicka en ny begäran om godkännande till slutpunkten för auktorisering. Om svaret innehåller omfångets parameter kan du använda scope-värde i begäran till resursen. |
| 403 |insufficient_access |Ämnet för token har inte de behörigheter som krävs för att få åtkomst till resursen. |Uppmana användaren att använda ett annat konto eller begär behörighet till den angivna resursen. |

## <a name="refreshing-the-access-tokens"></a>Uppdatera åtkomsttoken

Åtkomsttoken är tillfällig och måste uppdateras när de går ut om du vill fortsätta få åtkomst till resurser. Du kan uppdatera den `access_token` genom att skicka in en annan `POST` begäran till den `/token` slutpunkt, men den här tiden att tillhandahålla den `refresh_token` i stället för den `code`.  Uppdateringstoken är giltig för alla resurser som klienten redan har fått samtycka till att komma åt - därför en uppdateringstoken som skickats vid en begäran om `resource=https://graph.microsoft.com` kan användas för att begära en ny åtkomsttoken för `resource=https://contoso.com/api`. 

Uppdatera token har inte angiven livslängd. Livslängd för uppdateringstoken är oftast relativt lång. Men i vissa fall kan uppdaterings-tokens upphör att gälla, har återkallats eller saknar tillräcklig behörighet för den önskade åtgärden. Programmet behöver för att förvänta sig och hantera fel som returneras av utfärdande-slutpunkten på rätt sätt.

När du får ett svar med en uppdatering fel kan ta bort den aktuella uppdateringstoken och begär en ny auktoriseringskod eller åtkomsttoken. I synnerhet när med hjälp av en uppdatering av token i flödet Auktoriseringskodsbeviljande om du får ett svar med den `interaction_required` eller `invalid_grant` felkoder, ta bort uppdateringstoken och begär en ny auktoriseringskod.

Ett exempel på begäran till den **klientspecifik** slutpunkt (du kan också använda den **vanliga** slutpunkt) för att hämta en ny token med hjälp av en uppdateringstoken som ser ut så här:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar för token kommer att se ut:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parameter | Beskrivning |
| --- | --- |
| token_type |Tokentypen. Det enda värdet som stöds är **ägar**. |
| expires_in |Den återstående livslängden för token på några sekunder. Ett vanligt värde är 3 600 (en timme). |
| expires_on |Datum och tid då token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. |
| resurs |Identifierar den skyddade resursen som åtkomsttoken kan vara används för att åtkomst. |
| omfång |Personifiering behörigheter till native client-program. Standardbehörigheten är **user_impersonation**. Ägaren av målresursen kan registrera alternativa värden i Azure AD. |
| access_token |Den nya åtkomst-token som begärdes. |
| refresh_token |En ny OAuth 2.0-refresh_token som kan användas för att begära en ny åtkomsttoken när det i det här svaret upphör att gälla. |

### <a name="error-response"></a>Felsvar
En exempel-felsvar kan se ut så här:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa i diagnostik. |
| tidsstämpel |Den tid då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan hjälpa i diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa i diagnostik för komponenter. |

En beskrivning av felkoder och rekommenderade klientåtgärd i [felkoder för tokenslutpunkt fel](#error-codes-for-token-endpoint-errors).

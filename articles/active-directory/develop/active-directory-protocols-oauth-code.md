---
title: Förstå OAuth 2.0-auktoriseringskodflödet i Azure AD
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att bevilja åtkomst till webbprogram och webb-API: er i din klient med hjälp av Azure Active Directory och OAuth 2.0.'
services: active-directory
documentationcenter: .net
author: hpsin
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 241f872b3069a58a35df7104f3335964298c7a20
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Auktorisera åtkomst till webbprogram med OAuth 2.0 och Azure Active Directory
Azure Active Directory (AD Azure) använder OAuth 2.0 för att du ska bevilja åtkomst till webbprogram och webb-API: er i Azure AD-klienten. Den här guiden är språkoberoende och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda någon av våra bibliotek med öppen källkod.

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnittet 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.1). Den används för att utföra autentisering och auktorisering i de flesta programtyper, inklusive webbappar och internt installerade appar.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Flödet för OAuth 2.0-auktorisering
På en hög nivå hela auktorisering flödet för ett program ser ut så här:

![Flödet för OAuth-Auth-kod](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Begära ett auktoriseringskod
Auktoriseringskodflödet börjar med klienten dirigera användare till den `/authorize` slutpunkt. I den här förfrågan anger klienten de behörigheter som behövs för att hämta från användaren. Du kan hämta OAuth 2.0-slutpunkten för din klient genom att välja **App registreringar > slutpunkter** i Azure Portal.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |Krävs |Program-ID som tilldelats din app när du har registrerat med Azure AD. Du hittar du i Azure Portal. Klicka på **Active Directory**, klickar du på katalogen, Välj programmet och klicka på **konfigurera** |
| response_type |Krävs |Måste innehålla `code` för auktoriseringskodflödet. |
| redirect_uri |Rekommenderas |Redirect_uri för din app, där autentisering svar kan skickas och tas emot av din app.  Den måste matcha en redirect_uris som du har registrerat i portalen, förutom det måste vara url-kodade.  Du bör använda standardvärdet för interna & mobila appar, `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app.  Det kan vara `query` eller `form_post`. |
| state |Rekommenderas |Ett värde som ingår i denna begäran returneras också token svar. Ett slumpmässigt genererat unikt värde används vanligtvis för [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12).  Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| resurs |valfri |App-ID URI för webb-API (skyddad resurs). Om du vill hitta URI: N för App-ID för webb-API, i Azure-portalen klickar du på **Active Directory**, klicka på katalogen programmet och klicka sedan på **konfigurera**. |
| kommandotolk |valfri |Ange vilken typ av användarinteraktion som krävs.<p> Giltiga värden är: <p> *inloggningen*: användaren ska uppmanas att autentiseras. <p> *medgivande*: tillstånd har beviljats, men behöver uppdateras. Användaren ska uppmanas att godkänna. <p> *admin_consent*: en administratör ska ange tillstånd för alla användare i organisationen |
| login_hint |valfri |Kan användas för att fylla före adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sitt lösenord i förväg.  Ofta appar använder den här parametern under omautentisering som redan har extraherats användarnamnet från en tidigare inloggning med hjälp av den `preferred_username` anspråk. |
| domain_hint |valfri |Ger en ledtråd om klient eller domän som användaren ska använda för att logga in. Värdet för domain_hint är en registrerad domän för klienten. Om klienten är federerat till en lokal katalog, omdirigerar AAD till den angivna innehavare federationsservern. |
| code_challenge_method | valfri    | Den metod som används för att koda den `code_verifier` för den `code_challenge` parameter. Kan vara något av `plain` eller `S256`.  Om inte, `code_challenge` antas vara klartext om `code_challenge` ingår.  Azure AAD version 2.0 stöder både `plain` och `S256`. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | valfri    | Används för att säkra auktorisering kod ger via bevis nyckel för koden Exchange (PKCE) från en ursprunglig-klient. Krävs om `code_challenge_method` ingår.  Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Om användaren är en del av en organisation, kan en administratör i organisationen medgivande eller neka för användarens räkning eller Tillåt användaren att godkänna. Användaren har möjlighet att godkänna endast när administratören tillåter det.
>
>

Nu uppmanas användaren att ange sina autentiseringsuppgifter och samtycker till att de behörigheter som anges i den `scope` Frågeparametern. När användaren autentiserar och ger ditt medgivande, Azure AD skickar ett svar till din app på den `redirect_uri` adressen i din begäran.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar kan se ut så här:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beskrivning |
| --- | --- |
| admin_consent |Värdet är SANT om en administratör har godkänt för en fråga om medgivande i begäran. |
| Koden |Auktoriseringskoden som programmet har begärt. Programmet kan använda Auktoriseringskoden för att begära en åtkomst-token för målresursen. |
| session_state |Ett unikt värde som identifierar den aktuella användarsessionen. Det här värdet är ett GUID, men ska behandlas som ett täckande värde som skickades utan undersökning. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Det är bra för programmet för att kontrollera att värdena i förfrågan och svar är identiska innan du använder svaret. Detta hjälper dig för att identifiera [webbplatser begära förfalskning (CSRF) attacker](https://tools.ietf.org/html/rfc6749#section-10.12) mot klienten. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att programmet kan hantera dem på lämpligt sätt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felvärde kod som definierats i avsnittet 5.2 av den [OAuth 2.0 auktorisering Framework](http://tools.ietf.org/html/rfc6749). I nästa tabell beskrivs felkoder som returnerar Azure AD. |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsedd att vara slutanvändarens eget. |
| state |Värdet state är ett slumpmässigt genererat inte återanvändas värde som skickades i begäran och returneras i svaret för att förhindra attacker med förfalskning (CSRF) av begäran. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktorisering endpoint fel
I följande tabell beskrivs de olika felkoder som kan returneras i den `error` parameter för felsvar.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis fångas upp under inledande testningen. |
| unauthorized_client |Klientprogrammet är inte tillåtet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| access_denied |Resursägare nekad medgivande |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om användaren godkänner. |
| unsupported_response_type |Auktorisering servern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis fångas upp under inledande testningen. |
| server_error |Ett oväntat fel uppstod på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. |Detta anger resursen, om det finns inte har konfigurerats i klienten. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Använda Auktoriseringskoden för att begära en åtkomst-token
Nu när du har skaffat ett auktoriseringskod och har beviljats behörighet av användaren du lösa in koden för en åtkomst-token till en resurs, genom att skicka en POST-begäran till den `/token` slutpunkt:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |Krävs |Program-Id som tilldelats din app när du har registrerat med Azure AD. Du hittar du i Azure-portalen. Program-Id visas i inställningarna för appregistrering.  |
| grant_type |Krävs |Måste vara `authorization_code` för auktoriseringskodflödet. |
| Koden |Krävs |Den `authorization_code` som du har införskaffade i föregående avsnitt |
| redirect_uri |Krävs |Samma `redirect_uri` värde som användes för att hämta den `authorization_code`. |
| client_secret |krävs för webbprogram som inte är tillåtet för offentliga klienter |Den hemlighet som programmet som du skapade i portalen för registrering av app för din app.  Den kan inte användas i en intern app (offentliga klient), eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter.  Det krävs för webbappar och webb-API: er (alla konfidentiell klienter) som har möjlighet att lagra den `client_secret` på ett säkert sätt på serversidan. |
| resurs |krävs om anges i tillståndet begäran, annars valfritt |App-ID URI för webb-API (skyddad resurs). |
| code_verifier | valfri              | Samma code_verifier som användes för att hämta authorization_code.  Krävs om PKCE användes i tillståndet kod bevilja begäran.  Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Om du vill hitta URI: N för App-ID i Azure-hanteringsportalen, klickar du på **Active Directory**, klickar du på katalogen, klicka på programmet och klicka sedan på **konfigurera**.

### <a name="successful-response"></a>Lyckat svar
Azure AD returnerar en åtkomst-token på ett lyckat svar. För att minimera nätverket anrop från klientprogrammet och deras associerade svarstid, ska klientprogrammet cachelagras åtkomsttoken för livslängd för token som anges i OAuth 2.0-svaret. Använd antingen för att fastställa livslängd för token i `expires_in` eller `expires_on` parametervärden.

Om ett webb-API-resurs returnerar ett `invalid_token` felkoden detta kan tyda på att resursen har fastställt att token har upphört att gälla. Om klienten och resursen klockan tiderna är olika (kallas en ”time skeva”), överväga resursen token har upphört att gälla innan token rensas från klientens cacheminne. Om detta inträffar avmarkera token från cache, även om det är fortfarande inom livslängden beräknade.

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
| access_token |Den begärda åtkomst-token som en signerad JSON-Webbtoken (JWT). Appen kan använda denna token för autentisering till skyddade resursen, till exempel ett webb-API. |
| token_type |Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är ägar. Mer information om ägar-token finns [OAuth2.0 auktorisering Framework: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| expires_on |Tiden då den åtkomst-token upphör att gälla. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC tills förfallotid. Det här värdet används för att fastställa livslängden för cachelagrade token. |
| resurs |App-ID URI för webb-API (skyddad resurs). |
| Omfång |Personifiering behörigheterna för klientprogrammet. Standardbehörigheten är `user_impersonation`. Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. |
| refresh_token |En token för uppdatering av OAuth 2.0. Appen kan använda denna token för att få ytterligare åtkomsttoken när den aktuella åtkomst-token upphör att gälla.  Uppdatera token är långlivade och kan användas för att få åtkomst till resurser för längre tid. |
| id_token |En osignerad JSON-Webbtoken (JWT). Appen kan base64Url avkoda segmenten i den här variabeln för att begäraninformation om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita dig på dem för auktorisering eller säkerhetsgränser. |

### <a name="jwt-token-claims"></a>JWT-Token anspråk
JWT-token i värdet för den `id_token` parameter kan avkodas till följande anspråk:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Läs mer om JSON web token i [JWT IETF utkast till en specifikation](http://go.microsoft.com/fwlink/?LinkId=392344). Mer information om typer av token och anspråk [stöds Token och anspråkstyper](active-directory-token-and-claims.md)

Den `id_token` parametern innehåller följande anspråkstyper:

| Anspråkstyp | Beskrivning |
| --- | --- |
| eller |Målgruppen för token. När token som utfärdas till ett klientprogram, den är den `client_id` av klienten. |
| exp |Förfallotid. Den tid när token upphör att gälla. För token ska vara giltigt måste aktuellt datum och tid vara mindre än eller lika med den `exp` värde. Tiden representeras som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills giltigheten token upphör att gälla.|
| family_name |Användarens senaste eller efternamn. Programmet kan visa det här värdet. |
| given_name |Användarens förnamn. Programmet kan visa det här värdet. |
| IAT |Utfärdad för närvarande. Tiden då JWT utfärdades. Tiden representeras som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills token har utfärdats. |
| ISS |Identifierar token utfärdaren |
| nbf |Inte före tiden. Den tid när token träder i kraft. Aktuellt datum och tid måste vara större än eller lika med värdet Nbf för token ska vara giltigt. Tiden representeras som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills token har utfärdats. |
| oid |Objektidentifierare (ID) för användarobjektet i Azure AD. |
| Sub |Token ämne identifierare. Det här är en permanent och oåterkallelig identifierare för användaren som beskrivs i token. Använd det här värdet i cachelagring logik. |
| tid |Klient identifierare (ID) för Azure AD-klienten som utfärdade token. |
| unique_name |En unik identifierare för som kan visas för användaren. Detta är vanligtvis ett UPN (user Principal name). |
| upn |Användarens huvudnamn för användaren. |
| ver |Version. Versionen av JWT-token, vanligtvis 1.0. |

### <a name="error-response"></a>Felsvar
Utfärdande endpoint felen är http-felkoder eftersom klienten anropar utfärdande slutpunkten direkt. Utöver HTTP-statuskoden returnerar Azure AD utfärdande slutpunkten också ett JSON-dokument med objekt som beskriver felet.

Ett felsvar som exempel kan se ut så här:

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
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa dig i diagnostik. |
| tidsstämpel |Tiden då felet inträffade. |
| trace_id |En unik identifierare för den begäran som hjälper dig diagnostik. |
| correlation_id |En unik identifierare för den begäran som kan hjälpa i diagnostik för komponenter. |

#### <a name="http-status-codes"></a>Statuskoder för HTTP
I följande tabell visas HTTP-statuskoder som returnerar utfärdande-slutpunkten. Felkoden är tillräcklig för att beskriva svaret i vissa fall, men om det finns fel, måste du parsa medföljande JSON-dokumentet och undersöka felkoden.

| HTTP-kod | Beskrivning |
| --- | --- |
| 400 |Standard-http-kod. Används i de flesta fall och beror ofta på en felaktig begäran. Åtgärda och skicka begäran igen. |
| 401 |Autentiseringen misslyckades. Till exempel saknar begäran parametern client_secret. |
| 403 |Det gick inte att auktorisera. Till exempel saknar användaren behörighet att komma åt resursen. |
| 500 |Ett internt fel har uppstått i tjänsten. Gör om begäran. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för token för slutpunkt-fel
| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran på nytt |
| invalid_grant |Auktoriseringskoden är ogiltigt eller har upphört att gälla. |Försök med en ny begäran till den `/authorize` slutpunkt |
| unauthorized_client |Den autentiserade klienten har inte behörighet att använda den här authorization grant-typen. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| invalid_client |Klientautentisering misslyckades. |Klientens autentiseringsuppgifter är inte giltiga. Om du vill åtgärda, uppdaterar programadministratören autentiseringsuppgifterna. |
| unsupported_grant_type |Auktorisering servern stöder inte typen bevilja tillstånd. |Ändra bevilja i begäran. Den här typen av fel bör sker enbart under utveckling och identifieras under inledande testningen. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. |Detta anger resursen, om det finns inte har konfigurerats i klienten. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| interaction_required |Begäran kräver interaktion från användaren. Till exempel krävs ett steg i ytterligare autentisering. | Försök igen med en interaktiv auktoriseringsbegäran för samma resurs i stället för en icke-interaktiv begäran. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |

## <a name="use-the-access-token-to-access-the-resource"></a>Använd den åtkomst-token för åtkomst till resursen
Nu när du har skaffat har en `access_token`, du kan använda token i begäranden till webb-API: er, genom att inkludera den i den `Authorization` rubrik. Den [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) specifikationen förklarar hur du använder ägar-token i HTTP-begäranden att komma åt skyddade resurser.

### <a name="sample-request"></a>Exempel på begäran
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Felsvar
Skyddade resurser som implementerar RFC 6750 problemet HTTP-statuskoder. Om begäran innehåller inte autentiseringsuppgifter eller saknar token, svaret innehåller ett `WWW-Authenticate` huvud. När en begäran inte svarar resursservern med HTTP-statuskoden och en felkod.

Följande är ett exempel på ett misslyckade svar när klientbegäran inte innehåller ägartoken:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Felparametrar
| Parameter | Beskrivning |
| --- | --- |
| authorization_uri |URI: N (fysiska slutpunkt) för auktorisering-servern. Det här värdet används också som en sökning för att hämta mer information om servern från en slutpunkt för identifiering. <p><p> Klienten måste verifiera att tillståndet servern är betrodd. När resursen skyddas av Azure AD, det räcker att verifiera att URL som börjar med https://login.microsoftonline.com eller ett annat värdnamn som har stöd för Azure AD. En klient-specifik resurs ska alltid returnera ett klient-specifika tillstånd URI. |
| fel |Ett felvärde kod som definierats i avsnittet 5.2 av den [OAuth 2.0 auktorisering Framework](http://tools.ietf.org/html/rfc6749). |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsedd att vara slutanvändarens eget. |
| resursid |Returnerar den unika identifieraren för resursen. Klientprogrammet kan använda den här identifieraren som värde för den `resource` parametern vid begäran om en token för resursen. <p><p> Det är viktigt att verifiera det här värdet klientprogrammet, annars skadliga service kanske kan orsaka en **höjning av privilegier** attack <p><p> Den rekommenderade strategin för att förhindra angrepp är att kontrollera att den `resource_id` matchar basen för web API-URL som ska användas. Till exempel om https://service.contoso.com/data används, den `resource_id` kan vara htttps://service.contoso.com/. Klientprogrammet måste avvisa en `resource_id` som inte börjar med en bas-URL Om det inte finns en tillförlitlig alternativa sätt att kontrollera id. |

#### <a name="bearer-scheme-error-codes"></a>Felkoder för ägar-schema
Specifikationen RFC 6750 definierar följande fel för resurser som använder WWW-Authenticate-huvud och ägar-schemat i svaret.

| HTTP-statuskod | Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- | --- |
| 400 |invalid_request |Begäran är inte giltig. Det kan till exempel vara saknas en parameter eller använda samma parameter två gånger. |Åtgärda felet och försök begäran. Den här typen av fel bör sker enbart under utveckling och kan identifieras i inledande tester. |
| 401 |invalid_token |Åtkomst-token saknas, är ogiltigt eller har återkallats. Värdet för parametern error_description ger ytterligare detaljer. |Begär en ny token från servern auktorisering. Om den nya token inte har ett oväntat fel uppstod. Skicka ett felmeddelande till användaren och försök igen efter slumpmässig fördröjning. |
| 403 |insufficient_scope |Åtkomsttoken innehåller inte personifiering behörighet som krävs för åtkomst till resursen. |Skicka en ny auktoriseringsbegäran till slutpunkten för auktorisering. Om svaret innehåller omfattningsparametern, använder du scope-värde i begäran till resursen. |
| 403 |insufficient_access |Ämnet för token har inte de behörigheter som krävs för att få åtkomst till resursen. |Uppmana användaren att använda ett annat konto eller begär behörighet till den angivna resursen. |

## <a name="refreshing-the-access-tokens"></a>Uppdatera åtkomsttoken
Åtkomsttoken är tillfällig och måste uppdateras när de går ut om du vill fortsätta få åtkomst till resurser. Kan du uppdatera den `access_token` genom att skicka in en annan `POST` begäran om att den `/token` slutpunkt, men den här gången som tillhandahåller den `refresh_token` i stället för den `code`.

Uppdatera token inte har angivna livslängd. Livslängd för uppdaterings-tokens normalt relativt lång. Men i vissa fall uppdaterings-tokens upphör, har återkallats eller saknar behörighet för önskad åtgärd. Programmet måste räknar och hantera fel som returneras av utfärdande-slutpunkten på rätt sätt.

När du får ett svar med en uppdatering fel, ta bort den aktuella uppdateringstoken och begär en ny auktoriseringskod eller åtkomst-token. I synnerhet när med hjälp av en uppdatering token i Authorization kod Grant-flöde om du får ett svar med den `interaction_required` eller `invalid_grant` felkoder, ta bort uppdateringstoken och begär en ny auktoriseringskod.

Ett exempel på begäran till den **klient-specifika** slutpunkt (du kan också använda den **vanliga** endpoint) för att hämta en ny token med hjälp av en uppdateringstoken som ser ut så här:

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
Ett lyckat token svar ser ut:

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
| token_type |Tokentyp. Det enda värdet som stöds är **ägar**. |
| expires_in |Återstående livslängd för token i sekunder. Ett vanligt värde är 3 600 (en timme). |
| expires_on |Datum och tid då token upphör att gälla. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC tills förfallotid. |
| resurs |Identifierar skyddade resursen som den åtkomst-token som kan användas för att få åtkomst till. |
| Omfång |Personifiering behörigheterna för native client-program. Standardbehörigheten är **user_impersonation**. Ägaren av målresursen kan registrera alternativ i Azure AD. |
| access_token |Ny åtkomsttoken som begärdes. |
| refresh_token |En ny OAuth 2.0-refresh_token som kan användas för att begära en ny åtkomsttoken när det i det här svaret upphör att gälla. |

### <a name="error-response"></a>Felsvar
Ett felsvar som exempel kan se ut så här:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
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
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa dig i diagnostik. |
| tidsstämpel |Tiden då felet inträffade. |
| trace_id |En unik identifierare för den begäran som hjälper dig diagnostik. |
| correlation_id |En unik identifierare för den begäran som kan hjälpa i diagnostik för komponenter. |

En beskrivning av felkoder och rekommenderade klientåtgärd finns [felkoder för token för slutpunkt fel](#error-codes-for-token-endpoint-errors).

---
title: Förstå Auktoriseringskodflödet för OAuth 2.0 i Azure AD
description: I den hÃ¤r artikeln beskrivs hur du ankÃ¤nder HTTP-meddelanden fÃ¤r att auktorisera åtkomst till webbprogram och webb-API:er i din klient med Azure Active Directory och OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec7cf5a45ce31cde923dce521636589cfcda786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154465"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Auktorisera åtkomst till Azure Active Directory-webbprogram med beviljandeflödet för OAuth 2.0-kod

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Om du inte talar om för servern vilken resurs du planerar att anropa utlöses inte principerna för villkorlig åtkomst för den resursen. Så för att få MFA-utlösare måste du inkludera en resurs i webbadressen. 
>

I Azure Active Directory (Azure AD) används OAuth 2.0 för att du ska kunna bevilja åtkomst till webbprogram och webb-API:er i din klientorganisation. Den här guiden är språkoberoende och beskriver hur du skickar och ta emot [HTTP-meddelanden](active-directory-authentication-libraries.md)utan att använda något av våra bibliotek med öppen källkod .

OAuth 2.0-auktoriseringskodflödet beskrivs i [avsnitt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.1). Den används för att utföra autentisering och auktorisering i de flesta programtyper, inklusive webbappar och inbyggda installerade appar.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Registrera först ditt program med din Azure Active Directory -klientorganisation (Azure AD). Det ger dig en program-ID för ditt program och låter det ta emot tokens.

1. Logga in på [Azure-portalen](https://portal.azure.com).
   
1. Välj din Azure AD-klient genom att välja ditt konto längst upp till höger på sidan, följt av att välja **Switch Directory-navigeringen** och sedan välja lämplig klient. 
   - Hoppa över det här steget om du bara har en Azure AD-klient under ditt konto, eller om du redan har valt lämplig Azure AD-klientorganisation.
   
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
   
1. På **vänster** Azure Active Directory-menyn väljer du **Appregistreringar**och väljer sedan **Ny registrering**.
   
1. Följ anvisningarna och skapa ett nytt program. Det spelar ingen roll om det är ett webbprogram eller en offentlig klient (mobil & skrivbord) ansökan om den här guiden, men om du vill ha specifika exempel för webbapplikationer eller offentliga klientprogram, kolla in våra [snabbstarter](v1-overview.md).
   
   - **Namn** är appens namn och beskriver appen för användarna.
   - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
   - Ange **redirect-URI.** För webbprogram är detta den grundläggande URL:en för din app där användarna kan logga in.  Till exempel `http://localhost:12345`. För offentliga klienter (mobila &-skrivbordet) använder Azure AD den för att returnera tokensvar. Ange ett specifikt värde för ditt program.  Till exempel `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. När du har slutfört registreringen tilldelar Azure AD ditt program en unik klientidentifierare **(program-ID).** Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
   
1. Om du vill hitta ditt program i Azure-portalen väljer du **Appregistreringar**och väljer sedan **Visa alla program**.

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 auktoriseringsflöde

På en hög nivå ser hela auktoriseringsflödet för ett program ut lite så här:

![Oauth Auth-kodflöde](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod

Auktoriseringskodflödet börjar med att klienten dirigerar användaren till `/authorize` slutpunkten. I den här begäran anger klienten de behörigheter som den behöver för att hämta från användaren. Du kan hämta OAuth 2.0-auktoriseringsslutpunkten för din klient genom att välja **Appregistreringar > slutpunkter** i Azure-portalen.

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
| tenant |krävs |Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klientidentifierare, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` till exempel eller `contoso.onmicrosoft.com` eller `common` för klientoberoende token |
| client_id |krävs |Program-ID:t som tilldelats din app när du registrerade det med Azure AD. Du hittar detta i Azure Portal. Klicka på **Azure Active Directory** i sidofältet för tjänster, klicka på **Appregistreringar**och välj programmet. |
| response_type |krävs |Måste `code` inkludera för auktoriseringskodflödet. |
| redirect_uri |Rekommenderas |Den redirect_uri i din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av de redirect_uris du registrerat i portalen, förutom att den måste url-kodas. För inbyggda & mobilappar bör du använda `https://login.microsoftonline.com/common/oauth2/nativeclient`standardvärdet för . |
| response_mode |valfri |Anger den metod som ska användas för att skicka tillbaka den resulterande token till din app. Kan `query`vara `fragment`, `form_post`eller . `query`tillhandahåller koden som en frågesträngparameter på din omdirigerings-URI. Om du begär en ID-token med det implicita flödet kan du inte använda `query` det som anges i [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du bara begär koden kan du `query` `fragment`använda `form_post`, eller . `form_post`kör en POST som innehåller koden till din omdirigera URI. Standard är `query` för ett kodflöde.  |
| state |Rekommenderas |Ett värde som ingår i begäran som också returneras i tokensvaret. Ett slumpmässigt genererat unikt värde används vanligtvis för [att förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| resource | Rekommenderas |App-ID-URI:n för målwebb-API:et (säker resurs). Om du vill hitta app-ID-URI:n klickar du på Azure Active Directory i Azure **Portal,** klickar på **Programregistreringar**, öppnar programmets **inställningssida** och klickar sedan på **Egenskaper**. Det kan också vara `https://graph.microsoft.com`en extern resurs som . Detta krävs i en av antingen auktoriserings- eller tokenbegäranden. För att säkerställa färre autentiseringsanorderingar placera den i auktoriseringsbegäran för att säkerställa att samtycke tas emot från användaren. |
| omfång | **Ignoreras** | För v1 Azure AD-appar måste scope vara statiskt konfigurerade i Azure Portal under **programinställningarna**, **Obligatoriska behörigheter**. |
| Snabb |valfri |Ange vilken typ av användarinteraktion som krävs.<p> Giltiga värden är: <p> *inloggning:* Användaren bör uppmanas att omauktorisera. <p> *select_account*: Användaren uppmanas att välja ett konto och avbryta enstaka inloggning. Användaren kan välja ett befintligt inloggningskonto, ange sina autentiseringsuppgifter för ett ihågkommet konto eller välja att använda ett annat konto helt och hållet. <p> *samtycke*: Användarens samtycke har beviljats, men måste uppdateras. Användaren bör uppmanas att godkänna. <p> *admin_consent*: En administratör bör uppmanas att godkänna på uppdrag av alla användare i organisationen |
| login_hint |valfri |Kan användas för att förfylla fältet användarnamn/e-postadress på inloggningssidan för användaren, om du känner till deras användarnamn i förväg. Ofta använder appar den här parametern under omautentisering, efter att redan ha `preferred_username` extraherat användarnamnet från en tidigare inloggning med anspråket. |
| domain_hint |valfri |Ger en ledtråd om klienten eller domänen som användaren bör använda för att logga in. Värdet för domain_hint är en registrerad domän för klienten. Om klienten är federerad till en lokal katalog omdirigeras AAD till den angivna klientfederationsservern. |
| code_challenge_method | Rekommenderas    | Den metod som används `code_verifier` för `code_challenge` att koda för parametern. Kan vara `plain` en `S256`av eller . Om uteslutas, `code_challenge` antas vara klartext om `code_challenge` ingår. Azure AAD v1.0 `plain` `S256`stöder både och . Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Rekommenderas    | Används för att säkra auktoriseringskodbidrag via Proof Key for Code Exchange (PKCE) från en infödd eller offentlig klient. Krävs `code_challenge_method` om ingår. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Om användaren är en del av en organisation kan en administratör för organisationen godkänna eller avböja för användarens räkning, eller tillåta användaren att godkänna. Användaren ges möjlighet att godkänna endast när administratören tillåter det.
>
>

Nu uppmanas användaren att ange sina autentiseringsuppgifter och samtycka till de behörigheter som begärs av appen i Azure Portal. När användaren har autentiserat och gett sitt samtycke skickar `redirect_uri` Azure AD ett svar till din app på adressen i din begäran med koden.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar kan se ut så här:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beskrivning |
| --- | --- |
| admin_consent |Värdet är sant om en administratör har samtyckt till en begäran om samtycke. |
| kod |Den auktoriseringskod som programmet begärde. Programmet kan använda auktoriseringskoden för att begära en åtkomsttoken för målresursen. |
| session_state |Ett unikt värde som identifierar den aktuella användarsessionen. Det här värdet är ett GUID, men bör behandlas som ett ogenomskinligt värde som godkänns utan granskning. |
| state |Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Det är en bra idé för programmet att kontrollera att tillståndsvärdena i begäran och svaret är identiska innan du använder svaret. Detta hjälper till att identifiera [CSRF-attacker (Cross-Site Request Forgery)](https://tools.ietf.org/html/rfc6749#section-10.12) mot klienten. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas `redirect_uri` till så att programmet kan hantera dem på rätt sätt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkodsvärde som definieras i avsnitt 5.2 i [OAuth 2.0-auktoriseringsramverket](https://tools.ietf.org/html/rfc6749). I nästa tabell beskrivs de felkoder som Azure AD returnerar. |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsett att vara slutanvändarvänligt. |
| state |Tillståndsvärdet är ett slumpmässigt genererat icke-återanvänd värde som skickas i begäran och returneras i svaret för att förhindra csrf-attacker (cross-site request forgery). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktoriseringsslutpunktsfel
I följande tabell beskrivs de olika felkoder `error` som kan returneras i parametern för felsvaret.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en parameter som saknas som krävs. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och fångas vanligtvis under inledande testning. |
| unauthorized_client |Klientprogrammet har inte behörighet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| access_denied |Resursägaren nekas samtycke |Klientprogrammet kan meddela användaren att det inte kan fortsätta om inte användaren samtycker. |
| unsupported_response_type |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och fångas vanligtvis under inledande testning. |
| server_error |Servern påträffade ett oväntat fel. |Försök igen. Dessa fel kan bero på tillfälliga villkor. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller så är den inte korrekt konfigurerad. |Detta indikerar att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Använd auktoriseringskoden för att begära en åtkomsttoken
Nu när du har skaffat en auktoriseringskod och har beviljats behörighet av användaren kan du lösa `/token` in koden för en åtkomsttoken till önskad resurs genom att skicka en POST-begäran till slutpunkten:

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
| tenant |krävs |Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klientidentifierare, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` till exempel eller `contoso.onmicrosoft.com` eller `common` för klientoberoende token |
| client_id |krävs |Program-ID:t som tilldelats din app när du registrerade den med Azure AD. Du hittar detta i Azure-portalen. Program-ID visas i inställningarna för appregistreringen. |
| grant_type |krävs |Det `authorization_code` måste vara för auktoriseringskodflödet. |
| kod |krävs |Det `authorization_code` som du fick i föregående avsnitt |
| redirect_uri |krävs | En `redirect_uri`registrerad på klientprogrammet. |
| client_secret |som krävs för webbappar, inte tillåtet för offentliga klienter |Programhemligheten som du skapade i Azure Portal för din app under **Nycklar**. Den kan inte användas i en inbyggd app (offentlig klient), eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API:er (alla `client_secret` konfidentiella klienter), som har möjlighet att lagra den säkert på serversidan. Den client_secret ska url-kodas innan de skickas. |
| resource | Rekommenderas |App-ID-URI:n för målwebb-API:et (säker resurs). Om du vill hitta app-ID-URI:n klickar du på Azure Active Directory i Azure **Portal,** klickar på **Programregistreringar**, öppnar programmets **inställningssida** och klickar sedan på **Egenskaper**. Det kan också vara `https://graph.microsoft.com`en extern resurs som . Detta krävs i en av antingen auktoriserings- eller tokenbegäranden. För att säkerställa färre autentiseringsanorderingar placera den i auktoriseringsbegäran för att säkerställa att samtycke tas emot från användaren. Om resursens parametrar i både auktoriseringsbegäran och tokenbegäran matchar. | 
| code_verifier | valfri | Samma code_verifier som användes för att få authorization_code. Krävs om PKCE användes i begäran om auktoriseringskodsbidrag. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Om du vill hitta app-ID-URI:n klickar du på Azure Active Directory i Azure **Portal,** klickar på **Programregistreringar**, öppnar programmets **inställningssida** och klickar sedan på **Egenskaper**.

### <a name="successful-response"></a>Lyckat svar
Azure AD returnerar en [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) efter ett lyckat svar. För att minimera nätverksanrop från klientprogrammet och deras associerade svarstid bör klientprogrammet cachelagra åtkomsttoken för tokenlivslängden som anges i OAuth 2.0-svaret. Om du vill bestämma tokenlivslängden `expires_in` använder du antingen parametervärdena eller `expires_on` parametervärdena.

Om en webb-API-resurs returnerar en `invalid_token` felkod kan det tyda på att resursen har fastställt att token har upphört att gälla. Om klient- och resursklocktiderna är olika (kallas "tidsnedskjutning") kan resursen anse att token har upphört att gälla innan token rensas från klientcachen. Om detta inträffar rensar du token från cacheminnet, även om den fortfarande ligger inom den beräknade livslängden.

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
| access_token |Den begärda åtkomsttoken.  Detta är en ogenomskinlig sträng - det beror på vad resursen förväntar sig att ta emot och är inte avsedd för klienten att titta på. Appen kan använda den här token för att autentisera till den skyddade resursen, till exempel ett webb-API. |
| token_type |Anger tokentypsvärdet. Den enda typen som Azure AD stöder är Bärare. Mer information om innehavartoken finns i [OAuth2.0 Auktoriseringsram: Bärare tokenanvändning (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Den tidpunkt då åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till förfallotiden. Det här värdet används för att bestämma livslängden för cachelagrade token. |
| resource |App-ID-URI för webb-API :et (säker resurs). |
| omfång |Personifieringsbehörigheter som beviljats klientprogrammet. Standardbehörigheten är `user_impersonation`. Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. |
| refresh_token |En OAuth 2.0-uppdateringstoken. Appen kan använda den här token för att hämta ytterligare åtkomsttoken efter att den aktuella åtkomsttoken har upphört att gälla. Uppdatera token är långlivade och kan användas för att behålla åtkomsten till resurser under längre tidsperioder. |
| id_token |En osignerad JSON-webbtoken (JWT) som representerar en [ID-token](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Appen kan base64Url avkoda segmenten för den här token för att begära information om användaren som loggade in. Appen kan cachelagra värdena och visa dem, men den bör inte förlita sig på dem för några auktoriserings- eller säkerhetsgränser. |

Mer information om JSON-webbtoken finns i [JWT IETF:s utkastspecifikation](https://go.microsoft.com/fwlink/?LinkId=392344).   Mer information `id_tokens`om finns i [v1.0 OpenID Connect-flödet](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Felsvar
Slutpunktsfel för tokenutfärdande är HTTP-felkoder, eftersom klienten anropar slutpunkten för tokenutfärdande direkt. Förutom HTTP-statuskoden returnerar slutpunkten för Azure AD-tokenutfärdande också ett JSON-dokument med objekt som beskriver felet.

Ett exempelfelsvar kan se ut så här:

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
| fel |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa till med diagnostik. |
| timestamp |Den tidpunkt då felet uppstod. |
| trace_id |En unik identifierare för begäran som kan hjälpa till med diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa till med diagnostik mellan komponenter. |

#### <a name="http-status-codes"></a>HTTP-statuskoder
I följande tabell visas de HTTP-statuskoder som slutpunkten för tokenutfärdande returnerar. I vissa fall är felkoden tillräcklig för att beskriva svaret, men om det finns fel måste du tolka det medföljande JSON-dokumentet och undersöka dess felkod.

| HTTP-kod | Beskrivning |
| --- | --- |
| 400 |Standard-HTTP-kod. Används i de flesta fall och beror vanligtvis på en felaktig begäran. Åtgärda och skicka begäran igen. |
| 401 |Autentiseringen misslyckades. Begäran saknar till exempel parametern client_secret. |
| 403 |Auktoriseringen misslyckades. Användaren har till exempel inte behörighet att komma åt resursen. |
| 500 |Ett internt fel har uppstått vid tjänsten. Försök igen. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för tokenslutpunktsfel
| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en parameter som saknas som krävs. |Åtgärda och skicka begäran igen |
| invalid_grant |Auktoriseringskoden är ogiltig eller har upphört att gälla. |Prova en ny `/authorize` begäran till slutpunkten |
| unauthorized_client |Den autentiserade klienten har inte behörighet att använda den här behörighetsbeviljandetypen. |Detta inträffar vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| invalid_client |Klientautentisering misslyckades. |Klientautentiseringsuppgifterna är ogiltiga. För att åtgärda uppdaterar programadministratören autentiseringsuppgifterna. |
| unsupported_grant_type |Auktoriseringsservern stöder inte behörighetsbidragstypen. |Ändra bidragstypen i begäran. Den här typen av fel bör endast uppstå under utvecklingen och upptäckas under inledande testning. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller så är den inte korrekt konfigurerad. |Detta indikerar att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| interaction_required |Begäran kräver användarinteraktion. Ett ytterligare autentiseringssteg krävs till exempel. | I stället för en icke-interaktiv begäran försöker du igen med en interaktiv auktoriseringsbegäran för samma resurs. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt tillstånd. |

## <a name="use-the-access-token-to-access-the-resource"></a>Använd åtkomsttoken för att komma åt resursen
Nu när du har skaffat en `access_token`kan du använda token i begäranden till `Authorization` webb-API:er genom att inkludera den i huvudet. [RFC 6750-specifikationen](https://www.rfc-editor.org/rfc/rfc6750.txt) förklarar hur du använder innehavartoken i HTTP-begäranden för att komma åt skyddade resurser.

### <a name="sample-request"></a>Exempel på begäran
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Felsvar
Skyddade resurser som implementerar RFC 6750 utfärdar HTTP-statuskoder. Om begäran inte innehåller autentiseringsuppgifter eller saknar token, `WWW-Authenticate` innehåller svaret ett huvud. När en begäran misslyckas svarar resursservern med HTTP-statuskoden och en felkod.

Följande är ett exempel på ett misslyckat svar när klientbegäran inte innehåller innehavartoken:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Felparametrar
| Parameter | Beskrivning |
| --- | --- |
| authorization_uri |URI-platsen (den fysiska slutpunkten) för auktoriseringsservern. Det här värdet används också som en uppslagsnyckel för att få mer information om servern från en identifieringsslutpunkt. <p><p> Klienten måste verifiera att auktoriseringsservern är betrodd. När resursen skyddas av Azure AD är det tillräckligt att `https://login.microsoftonline.com` verifiera att URL:en börjar med eller ett annat värdnamn som Azure AD stöder. En klientspecifik resurs ska alltid returnera en klientspecifik auktoriserings-URI. |
| fel |Ett felkodsvärde som definieras i avsnitt 5.2 i [OAuth 2.0-auktoriseringsramverket](https://tools.ietf.org/html/rfc6749). |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsett att vara slutanvändarvänligt. |
| resource_id |Returnerar resursens unika identifierare. Klientprogrammet kan använda den här identifieraren `resource` som värdet för parametern när den begär en token för resursen. <p><p> Det är viktigt för klientprogrammet att verifiera det här värdet, annars kan en skadlig tjänst framkalla en **behörighetshöjning** <p><p> Den rekommenderade strategin för att förhindra `resource_id` en attack är att kontrollera att matchar basen för webb-API-URL:en som används. Om till `https://service.contoso.com/data` exempel används kan `resource_id` den `https://service.contoso.com/`vara . Klientprogrammet måste avvisa `resource_id` en som inte börjar med bas-URL: n om det inte finns ett tillförlitligt alternativt sätt att verifiera id. |

#### <a name="bearer-scheme-error-codes"></a>Felkoder för bärare schema
RFC 6750-specifikationen definierar följande fel för resurser som använder WWW-Autentisera huvudet och bärare schemat i svaret.

| HTTP-statuskod | Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- | --- |
| 400 |invalid_request |Begäran är inte välformulerad. Det kan till exempel saknas en parameter eller använda samma parameter två gånger. |Åtgärda felet och försök igen begäran. Den här typen av fel bör endast uppstå under utvecklingen och upptäckas vid inledande testning. |
| 401 |invalid_token |Åtkomsttoken saknas, är ogiltig eller återkallas. Värdet för parametern error_description ger ytterligare information. |Begär en ny token från auktoriseringsservern. Om den nya token misslyckas har ett oväntat fel inträffat. Skicka ett felmeddelande till användaren och försök igen efter slumpmässiga fördröjningar. |
| 403 |insufficient_scope |Åtkomsttoken innehåller inte de personifieringsbehörigheter som krävs för att komma åt resursen. |Skicka en ny auktoriseringsbegäran till auktoriseringsslutpunkten. Om svaret innehåller scopeparametern använder du scopevärdet i begäran till resursen. |
| 403 |insufficient_access |Ämnet för token har inte de behörigheter som krävs för att komma åt resursen. |Uppmana användaren att använda ett annat konto eller att begära behörigheter till den angivna resursen. |

## <a name="refreshing-the-access-tokens"></a>Uppdatera åtkomsttoken

Åtkomsttoken är kortlivade och måste uppdateras efter att de upphör att gälla för att fortsätta komma åt resurser. Du kan `access_token` uppdatera genom `POST` att skicka `/token` en annan begäran till `refresh_token` slutpunkten, men den här gången ger i stället för `code`.  Uppdateringstoken är giltiga för alla resurser som klienten redan har fått medgivande till `resource=https://graph.microsoft.com` åtkomst - därför kan en `resource=https://contoso.com/api`uppdateringstoken som utfärdats på en begäran om användas för att begära en ny åtkomsttoken för . 

Uppdatera token har inte angivna livstider. Vanligtvis är livslängden för uppdateringstoken relativt lång. I vissa fall upphör dock uppdateringstoken att gälla, återkallas eller saknar tillräckliga privilegier för den önskade åtgärden. Ditt program måste förvänta sig och hantera fel som returneras av slutpunkten för tokenutfärdande korrekt.

När du får ett svar med ett uppdateringstokenfel ignorerar du den aktuella uppdateringstoken och begär en ny auktoriseringskod eller åtkomsttoken. I synnerhet när du använder en uppdateringstoken i auktoriseringskodsbidragsflödet, om du får ett svar med `interaction_required` eller `invalid_grant` felkoder, ignorera uppdateringstoken och begära en ny auktoriseringskod.

En exempelbegäran till den **klientspecifika** slutpunkten (du kan också använda den **gemensamma** slutpunkten) för att få en ny åtkomsttoken med en uppdateringstoken ser ut så här:

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
Ett lyckat tokensvar kommer att se ut:

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
| token_type |Tokentypen. Det enda värde som stöds är **bärare**. |
| expires_in |Den återstående livslängden för token på några sekunder. Ett typiskt värde är 3600 (en timme). |
| expires_on |Datum och tid då token upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till förfallotiden. |
| resource |Identifierar den skyddade resurs som åtkomsttoken kan användas för åtkomst. |
| omfång |Personifieringsbehörigheter som beviljats det inbyggda klientprogrammet. Standardbehörigheten är **user_impersonation**. Ägaren till målresursen kan registrera alternativa värden i Azure AD. |
| access_token |Den nya åtkomsttoken som begärdes. |
| refresh_token |En ny OAuth 2.0 refresh_token som kan användas för att begära nya åtkomsttoken när den i det här svaret upphör att gälla. |

### <a name="error-response"></a>Felsvar
Ett exempelfelsvar kan se ut så här:

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
| fel |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |
| error_codes |En lista över STS-specifika felkoder som kan hjälpa till med diagnostik. |
| timestamp |Den tidpunkt då felet uppstod. |
| trace_id |En unik identifierare för begäran som kan hjälpa till med diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa till med diagnostik mellan komponenter. |

En beskrivning av felkoderna och den rekommenderade klientåtgärden finns i [Felkoder för tokenslutpunktsfel](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Nästa steg
Mer information om Slutpunkten för Azure AD v1.0 och hur du lägger till autentisering och auktorisering i webbprogram och webb-API:er finns [i exempelprogram](sample-v1-code.md).

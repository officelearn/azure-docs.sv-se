---
title: Avancerad användning av autentisering och auktorisering – Azure App Service | Microsoft Docs
description: Visar hur du anpassar autentisering och auktorisering i App Service och hämta användar- och annan token.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f3e30309b230ec44ddf39648b943f3f76dc7805d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722659"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Avancerad användning av autentisering och auktorisering i Azure App Service

Den här artikeln visar hur du anpassar inbyggt [autentisering och auktorisering i Apptjänst](overview-authentication-authorization.md), och för att hantera identitet från ditt program. 

Om du vill komma igång snabbt, finns i följande Självstudier:

* [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)
* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning](configure-authentication-provider-aad.md)
* [Så här konfigurerar du din app för att använda Facebook-inloggning](configure-authentication-provider-facebook.md)
* [Så här konfigurerar du din app för att använda Google-inloggning](configure-authentication-provider-google.md)
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning](configure-authentication-provider-microsoft.md)
* [Så här konfigurerar du din app för att använda Twitter-inloggning](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Använda flera inloggning-providers

Ett NYCKELFÄRDIGT sätt att presentera flera inloggning providers för dina användare (till exempel både Facebook och Twitter) omfattas inte av Portalkonfiguration. Men det är inte svårt att lägga till funktioner till din app. Stegen beskrivs i följande:

Först i den **autentisering / auktorisering** i Azure-portalen kan konfigurera var och en för den identitetsprovider som du vill aktivera.

I **åtgärd att vidta när begäran inte har autentiserats**väljer **Tillåt anonyma förfrågningar (ingen åtgärd)**.

På sidan logga in eller navigeringsfältet, eller någon annan plats för din app kan lägga till en inloggning länk var och en av de leverantörer som du har aktiverat (`/.auth/login/<provider>`). Exempel:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

När användaren klickar på länken, öppnas respektive inloggningssidan för att logga in användaren.

För att omdirigera användaren efter-registrerings-modulen till en anpassad URL, Använd den `post_login_redirect_url` frågesträngparametern (inte ska inte förväxlas med omdirigerings-URI i din identitet providerkonfigurationen). Till exempel för att vidarebefordra användaren till `/Home/Index` efter inloggning, Använd följande HTML-kod:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validera token från leverantörer

I en klient-riktade inloggning, programmet loggar in användaren till providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering (se [autentiseringsflödet](overview-authentication-authorization.md#authentication-flow)). Den här verifieringen själva ger inte faktiskt dig tillgång till önskad appresurser, men en lyckad validering ger dig en sessionstoken som du kan använda för att komma åt resurser i appen. 

För att validera token provider, konfigureras App Service-app först med den önskade providern. Vid körning, när du hämtar autentiseringstoken från leverantören, publicera denna token till `/.auth/login/<provider>` för verifiering. Exempel: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Token formatet varierar något beroende providern. Se tabellen nedan för mer information:

| Providern värde | Krävs i begärandetexten | Kommentarer |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Den `expires_in` egenskapen är valfri. <br/>När du begär en token från Live-tjänsterna, begär alltid den `wl.basic` omfång. |
| `google` | `{"id_token":"<id_token>"}` | Den `authorization_code` egenskapen är valfri. När du anger det kan också välja åtföljas av den `redirect_uri` egenskapen. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Använd ett giltigt [åtkomsttoken](https://developers.facebook.com/docs/facebook-login/access-tokens) från Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Om provider-token är har verifierats, API: et returnerar med en `authenticationToken` i svarstexten, vilket är din sessionstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

När du har den här sessionstoken kan du komma åt skyddade appresurser genom att lägga till den `X-ZUMO-AUTH` rubrik på HTTP-förfrågningar. Exempel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Logga ut från en session

Användare kan initiera en utloggning genom att skicka en `GET` begäran till appens `/.auth/logout` slutpunkt. Den `GET` begäran utför följande:

- Tar bort autentiseringscookies från den aktuella sessionen.
- Tar bort den aktuella användarens token från arkivet för token.
- Utför en serversidan utloggning identitetsleverantören för Azure Active Directory och Google.

Här är en enkel utloggning länk i en webbsida:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Som standard en lyckad utloggning dirigerar om klienten till URL: en `/.auth/logout/done`. Du kan ändra post-sign-out Omdirigeringssida genom att lägga till den `post_logout_redirect_uri` frågeparameter. Exempel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Det vi rekommenderar att du [koda](https://wikipedia.org/wiki/Percent-encoding) värdet för `post_logout_redirect_uri`.

När du använder fullständiga URL: er, måste URL: en vara antingen finns i samma domän eller konfigurerad som en tillåtna externa omdirigerings-URL för din app. I följande exempel visas att omdirigera till `https://myexternalurl.com` som inte finns i samma domän:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Du måste köra följande kommando i den [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Bevara URL fragment

När användare loggar in på din app, vanligtvis ska omdirigeras till samma avsnitt i samma sida, till exempel `/wiki/Main_Page#SectionZ`. Men eftersom [URL fragment](https://wikipedia.org/wiki/Fragment_identifier) (till exempel `#SectionZ`) skickas aldrig till servern, de inte bevaras som standard när OAuth-inloggningen har slutförts och omdirigerar tillbaka till din app. Användare får sedan en optimal upplevelse när de behöver för att navigera till den önskade fästpunkten igen. Den här begränsningen gäller för alla autentiseringslösningar för serversidan.

Du kan bevara URL fragment över OAuth-inloggningen i App Service-autentisering. Gör detta genom att ange en app som heter `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` till `true`. Du kan göra det den [Azure-portalen](https://portal.azure.com), eller helt enkelt att köra följande kommando den [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Användaranspråk för åtkomst

App Service skickar användaranspråk i ditt program med hjälp av särskilda rubriker. Externa begäranden är inte tillåtet att ställa in dessa huvuden så att de finns bara om anges av App Service. Vissa exempel huvuden är:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Koden som är skrivna på valfritt språk eller ramverk kan hämta den information som krävs från dessa rubriker. För 4.6 för ASP.NET-appar i **ClaimsPrincipal** ställs automatiskt med lämpliga värden.

Ditt program kan också få mer information om den autentiserade användaren genom att anropa `/.auth/me`. Mobile Apps-server SDK: er ger helper-metoder för att arbeta med dessa data. Mer information finns i [hur du använder Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), och [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Hämta token i Appkod

Från din serverkod införs provider-specifik token i huvudet för begäran och så att du enkelt kan komma åt dem. I följande tabell visas möjliga token rubriknamn:

| Leverantör | Rubriknamn |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-konto | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Skicka en HTTP från klientkoden (till exempel en mobilapp eller JavaScript i webbläsaren), `GET` begäran till `/.auth/me`. Den returnerade JSON har provider-specifik token.

> [!NOTE]
> Åtkomsttoken är för att komma åt provider-resurser, så att de finns bara om du konfigurerar din provider med en klienthemlighet. Så här hämtar du uppdateringstoken finns i [uppdatera åtkomsttoken](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Uppdatera åtkomsttoken

När din provider åtkomst-token upphör att gälla måste autentiseras användaren. Du kan undvika giltighetstid för token genom att göra en `GET` anrop till den `/.auth/refresh` slutpunkten för ditt program. När den anropas, uppdaterar App Service automatiskt åtkomsttoken i arkivet token för autentiserade användare. Efterföljande begäranden om token genom din Appkod hämta uppdateras token. Men för tokenuppdatering ska fungera, av tokenarkiv måste innehålla [uppdateringstoken](https://auth0.com/learn/refresh-tokens/) för din provider. Sättet att få uppdaterings-tokens dokumenteras av varje provider, men i följande lista är en kort sammanfattning:

- **Google**: Lägg till en `access_type=offline` frågesträngparametern till din `/.auth/login/google` API-anrop. Om du använder SDK för Mobile Apps, du kan lägga till parametern till en av de `LogicAsync` överlagringar (se [Google uppdatera token](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Ger inte uppdateringstoken. Långlivade token går ut inom 60 dagar (se [Facebook förfallodatum och tillägg av åtkomsttoken](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter-**: Åtkomsttoken inte upphör att gälla (se [Twitter OAuth vanliga frågor och svar](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-konto**: När [konfigurera autentiseringsinställningar för Microsoft-konto](configure-authentication-provider-microsoft.md)väljer den `wl.offline_access` omfång.
- **Azure Active Directory**: I [ https://resources.azure.com ](https://resources.azure.com), gör följande:
    1. Längst ned på sidan Välj **Läs/Skriv**.
    2. I den vänstra webbläsaren, navigerar du till **prenumerationer** > **_\<prenumeration\_namn_**   >  **resourceGroups** > _**\<resource\_grupp\_namn >**_   >  **providers** > **Microsoft.Web** > **platser** > _**\<app \_namn >**_ > **config** > **authsettings**. 
    3. Klicka på **Redigera**.
    4. Ändra följande egenskaper. Ersätt  _\<app\_id >_ med Azure Active Directory-program-ID för tjänsten som du vill komma åt.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicka på **placera**. 

När din provider har konfigurerats, kan du [hitta uppdateringstoken och förfallotid för åtkomsttoken](#retrieve-tokens-in-app-code) i arkivet för token. 

Om du vill uppdatera ditt åtkomst-token när som helst, bara anropa `/.auth/refresh` på valfritt språk. I följande kodfragment används jQuery för att uppdatera ditt åtkomst-token från en JavaScript-klient.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Om en användare återkallas behörigheterna för din app, anrop till `/.auth/me` kan misslyckas med ett `403 Forbidden` svar. Kontrollera programloggarna för information om du vill diagnostisera fel.

## <a name="extend-session-expiration-grace-period"></a>Utöka respitperiod för sessionen upphör att gälla

När en autentiserad session har gått ut, finns det en respitperiod för 72 timmar som standard. I den här tiden kan har du behörighet att uppdatera sessions-cookie eller sessionstoken med App Service utan autentiserades på nytt användaren. Du kan bara anropa `/.auth/refresh` när din sessions-cookie eller sessionstoken blir ogiltiga och du inte behöver spåra giltighetstid för token själv. När respitperioden 72 timmar är uppnås kan måste användaren logga in igen att hämta en giltig sessions-cookie eller sessionstoken.

Om 72 timmar inte tillräckligt med tid för dig, kan du utöka det här fönstret upphör att gälla. Utöka giltighetstid under en längre tid kan ha betydande säkerhetsaspekter (till exempel när en autentiseringstoken läcka ut eller blir stulen). Du bör så lämnar du standardinställningen 72 timmar eller anger tillägg du det minsta värdet.

Om du vill utöka fönstret Standard upphör att gälla, kör du följande kommando den [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Respittiden gäller endast för App Service-autentiserad session inte token från identitetsleverantörer. Det finns inga respitperiod för token som har upphört att gälla providern. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Begränsa domänen för logga in konton

Både Account och Azure Active Directory kan du logga in från flera domäner. Exempelvis kan Account _outlook.com_, _live.com_, och _hotmail.com_ konton. Azure Active Directory kan valfritt antal anpassade domäner för konton loggar in. Det här beteendet kan vara oönskade för en intern app som du inte vill att vem som helst med en _outlook.com_ kontot till åtkomst. Följ dessa steg om du vill begränsa domännamnet konton som loggar in.

I [ https://resources.azure.com ](https://resources.azure.com), gå till **prenumerationer** > **_\<prenumeration\_namn_**   >  **resourceGroups** > _**\<resource\_grupp\_namn >**_   >  **providers** > **Microsoft.Web** > **platser**  >    _**\<app\_namn >**_ > **config** > **authsettings**. 

Klicka på **redigera**, ändra egenskapen följande och klickar sedan på **placera**. Se till att ersätta  _\<domän\_namn >_ med den domän som du vill.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt (Windows)](app-service-web-tutorial-auth-aad.md)
> [självstudien: Autentisera och auktorisera användare slutpunkt till slutpunkt (Linux)](containers/tutorial-auth-aad.md)
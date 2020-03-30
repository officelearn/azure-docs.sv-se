---
title: Avancerad användning av AuthN/AuthO
description: Lär dig att anpassa autentiserings- och auktoriseringsfunktionen i App Service för olika scenarier och hämta användaranspråk och olika tokens.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280838"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Avancerad användning av autentisering och auktorisering i Azure App Service

I den här artikeln beskrivs hur du anpassar den inbyggda [autentiseringen och auktoriseringen i App Service](overview-authentication-authorization.md)och hanterar identitet från ditt program. 

Om du vill komma igång snabbt läser du någon av följande självstudier:

* [Självstudiekurs: Autentisera och auktorisera användare från på ett i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Självstudiekurs: Autentisera och auktorisera användare från på ett i Azure App Service för Linux](containers/tutorial-auth-aad.md)
* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning](configure-authentication-provider-aad.md)
* [Så här konfigurerar du din app för att använda Facebook-inloggning](configure-authentication-provider-facebook.md)
* [Så här konfigurerar du din app för att använda Google-inloggning](configure-authentication-provider-google.md)
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning](configure-authentication-provider-microsoft.md)
* [Så här konfigurerar du din app för att använda Twitter-inloggning](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Använda flera inloggningsleverantörer

Portalkonfigurationen erbjuder inte ett nyckelfärdigt sätt att presentera flera inloggningsleverantörer för användarna (till exempel både Facebook och Twitter). Det är dock inte svårt att lägga till funktionerna i appen. Stegen beskrivs på följande sätt:

Först, på sidan **Autentisering/auktorisering** i Azure-portalen, konfigurera var och en av de identitetsprovider som du vill aktivera.

I **Åtgärd som ska vidtas när begäran inte autentiseras**väljer du Tillåt **anonyma begäranden (ingen åtgärd)**.

På inloggningssidan, navigeringsfältet eller någon annan plats för din app, lägger du till en`/.auth/login/<provider>`inloggningslänk till var och en av de leverantörer som du har aktiverat ( ). Ett exempel:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

När användaren klickar på en av länkarna öppnas respektive inloggningssida för att logga in användaren.

Om du vill omdirigera användaren efter inloggning till `post_login_redirect_url` en anpassad URL använder du frågesträngparametern (ska inte förväxlas med Omdirigera URI i identitetsproviderns konfiguration). Om du till exempel `/Home/Index` vill navigera användaren till efter inloggning använder du följande HTML-kod:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validera token från providers

I en klientstyrd inloggning loggar programmet in användaren till providern manuellt och skickar sedan autentiseringstoken till App Service för validering (se [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow)). Den här valideringen i sig ger dig inte åtkomst till önskade appresurser, men en lyckad validering ger dig en sessionstoken som du kan använda för att komma åt appresurser. 

För att verifiera providertoken måste App Service-appen först konfigureras med önskad leverantör. När du har hämtat autentiseringstoken från din `/.auth/login/<provider>` provider bokför du token för validering. Ett exempel: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Tokenformatet varierar något beroende på providern. Mer information finns i följande tabell:

| Provider-värde | Krävs i begäran organ | Kommentarer |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Boendet `expires_in` är valfritt. <br/>När du begär token från Live-tjänster, begär alltid omfånget. `wl.basic` |
| `google` | `{"id_token":"<id_token>"}` | Boendet `authorization_code` är valfritt. När det anges kan den också eventuellt `redirect_uri` åtföljas av boendet. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Använd en giltig [token för användaråtkomst](https://developers.facebook.com/docs/facebook-login/access-tokens) från Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Om providertoken har validerats returneras `authenticationToken` API:et med en i svarstexten, som är din sessionstoken. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

När du har den här sessionstoken kan du `X-ZUMO-AUTH` komma åt skyddade appresurser genom att lägga till huvudet i dina HTTP-begäranden. Ett exempel: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Logga ut från en session

Användare kan initiera en ut `GET` logga ut genom `/.auth/logout` att skicka en begäran till appens slutpunkt. Begäran `GET` gör följande:

- Rensar autentiseringscookies från den aktuella sessionen.
- Tar bort den aktuella användarens token från tokenarkivet.
- För Azure Active Directory och Google utför en server-side-utskrivning på identitetsprovidern.

Här är en enkel utloggningslänk på en webbsida:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Som standard omdirigerar en lyckad ut signering klienten till URL:en `/.auth/logout/done`. Du kan ändra omdirigeringssidan `post_logout_redirect_uri` efter ut logga ut genom att lägga till frågeparametern. Ett exempel:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Vi rekommenderar att du [kodar](https://wikipedia.org/wiki/Percent-encoding) `post_logout_redirect_uri`värdet för .

När du använder fullständigt kvalificerade webbadresser måste URL:en antingen finnas i samma domän eller konfigureras som en tillåten extern omdirigerings-URL för din app. I följande exempel, för `https://myexternalurl.com` att omdirigera till det är inte värd i samma domän:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Kör följande kommando i [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Bevara URL-fragment

När användarna har loggat in på din app vill de vanligtvis omdirigeras `/wiki/Main_Page#SectionZ`till samma avsnitt på samma sida, till exempel . Men eftersom [URL-fragment](https://wikipedia.org/wiki/Fragment_identifier) (till `#SectionZ`exempel ) aldrig skickas till servern bevaras de inte som standard när OAuth-inloggningen är klar och omdirigeras tillbaka till din app. Användarna får sedan en suboptimal upplevelse när de behöver navigera till önskat ankare igen. Den här begränsningen gäller alla autentiseringslösningar på serversidan.

I App Service-autentisering kan du bevara URL-fragment över OAuth-inloggningen. Det gör du genom att `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true`ange en appinställning som anropas till . Du kan göra det i [Azure-portalen](https://portal.azure.com)eller helt enkelt köra följande kommando i [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Få tillgång till användaranspråk

App Service skickar användaranspråk till ditt program med hjälp av särskilda rubriker. Externa begäranden tillåts inte att ange dessa rubriker, så de finns bara om de anges av App Service. Några exempel rubriker inkluderar:

* X-MS-KLIENT-PRINCIPAL-NAMN
* X-MS-KLIENT-PRINCIPAL-ID

Kod som är skriven på valfritt språk eller ramverk kan hämta den information som den behöver från dessa rubriker. För ASP.NET 4.6-appar ställs **ClaimsPrincipal** automatiskt in med lämpliga värden. ASP.NET Core tillhandahåller dock inte en autentisering mellanprogram som integreras med App Service-användaranspråk. En lösning finns i [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Ditt program kan också få ytterligare information `/.auth/me`om den autentiserade användaren genom att ringa . SdK:erna för mobile apps-servern tillhandahåller hjälpmetoder för att arbeta med dessa data. Mer information finns i [Så här använder du Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)och Arbeta med [.NET-server](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)för server för server för server för azure mobile apps .

## <a name="retrieve-tokens-in-app-code"></a>Hämta token i appkod

Från serverkoden injiceras de leverantörsspecifika token i begäranden, så att du enkelt kan komma åt dem. I följande tabell visas möjliga tokenhuvudnamn:

| Leverantör | Rubriknamn |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-konto | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Från klientkoden (till exempel en mobilapp eller JavaScript i `GET` webbläsaren) skickar du en HTTP-begäran till `/.auth/me`. Den returnerade JSON har de providerspecifika token.

> [!NOTE]
> Åtkomsttoken är för åtkomst till providerresurser, så de finns bara om du konfigurerar din provider med en klienthemlighet. Mer om hur du ser hur du hämtar uppdateringstoken finns i Uppdatera åtkomsttoken.

## <a name="refresh-identity-provider-tokens"></a>Uppdatera identitetsprovidertoken

När din providers åtkomsttoken (inte [sessionstoken)](#extend-session-token-expiration-grace-period)upphör att gälla måste du autentisering av användaren igen innan du använder den token igen. Du kan undvika att `GET` token upphör `/.auth/refresh` att gälla genom att ringa ett samtal till slutpunkten för ditt program. När apptjänsten anropas uppdaterar den automatiskt åtkomsttoken i tokenarkivet för den autentiserade användaren. Efterföljande begäranden om token av din appkod får de uppdaterade tokens. Men för att tokenuppdatering ska fungera måste tokenarkivet innehålla [uppdateringstoken](https://auth0.com/learn/refresh-tokens/) för din provider. Sättet att hämta uppdateringstoken dokumenteras av varje provider, men följande lista är en kort sammanfattning:

- **Google**: Lägg `access_type=offline` till en `/.auth/login/google` frågesträngparameter i ditt API-anrop. Om du använder SDK för mobilappar kan du `LogicAsync` lägga till parametern i en av överbelastningarna (se [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Ger inte uppdateringstoken. Långlivade token upphör att gälla om 60 dagar (se [Facebooks förfallodatum och förlängning av åtkomsttokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Access-tokens upphör inte att gälla (se [Vanliga frågor och svar om Twitter OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-konto**: När [du konfigurerar autentiseringsinställningar för Microsoft-konto](configure-authentication-provider-microsoft.md)väljer du scopet. `wl.offline_access`
- **Azure Active**Directory [https://resources.azure.com](https://resources.azure.com): Gör följande i , gör följande:
    1. Högst upp på sidan väljer du **Läs/skriv**.
    2. I den vänstra webbläsaren navigerar du till prenumerationer**_\<på\_prenumerationsnamn_** **subscriptions** >  >  > **resourceGroups** > **_\<resursgruppsnamn\_\_>_**  >  >   >  **leverantörer av** > **Microsoft.Web****_\<\_sites-appens namn>_** **config** > **authsettings**.**sites** 
    3. Klicka på **Redigera**.
    4. Ändra följande egenskap. Ersätt _ \<\_app-ID>_ med Azure Active Directory-program-ID för den tjänst som du vill komma åt.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klicka på **Sätt**. 

När din provider har konfigurerats kan du [hitta uppdateringstoken och utgångstiden för åtkomsttoken](#retrieve-tokens-in-app-code) i tokenarkivet. 

Om du vill uppdatera din åtkomsttoken när som helst, ring bara på `/.auth/refresh` vilket språk som helst. Följande kodavsnitt använder jQuery för att uppdatera dina åtkomsttoken från en JavaScript-klient.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Om en användare återkallar de behörigheter som `/.auth/me` beviljats din `403 Forbidden` app kan samtalet misslyckas med ett svar. Om du vill diagnostisera fel kontrollerar du om det finns information om dina programloggar.

## <a name="extend-session-token-expiration-grace-period"></a>Förlänga respitperioden för förfallofridsfristen för sessionstoken

Den autentiserade sessionen upphör att gälla efter 8 timmar. När en autentrad session har gått ut finns det en 72-timmars respitperiod som standard. Inom den här respitperioden får du uppdatera sessionstoken med App Service utan att återuthyra användaren. Du kan `/.auth/refresh` bara ringa när din sessionstoken blir ogiltig och du behöver inte spåra tokens förfallodatum själv. När 72-timmars respitperioden har förfaller måste användaren logga in igen för att få en giltig sessionstoken.

Om 72 timmar inte är tillräckligt med tid för dig kan du förlänga utgångsfönstret. Att förlänga förfallodatumet under en lång period kan få betydande säkerhetskonsekvenser (till exempel när en autentiseringstoken läcker eller blir stulen). Så du bör lämna den på standard 72 timmar eller ställa in förlängningsperioden till det minsta värdet.

Om du vill utöka standardfönstret för förfallodatum kör du följande kommando i [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Respitperioden gäller endast för den autentiserade apptjänsten-sessionen, inte token från identitetsleverantörerna. Det finns ingen respitperiod för de utgångna providertoken. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Begränsa domänen för inloggningskonton

Både Microsoft-konto och Azure Active Directory kan du logga in från flera domäner. Med Microsoft-kontot kan du till exempel _outlook.com,_ _live.com_och _hotmail.com_ konton. Azure AD tillåter valfritt antal anpassade domäner för inloggningskonton. Du kanske vill accelerera användarna direkt till din egen märkesbaserade Inloggningssida för Azure AD (till exempel `contoso.com`). Så här föreslår du domännamnet för inloggningskontona.

I [https://resources.azure.com](https://resources.azure.com)navigerar du till prenumerationer**_\<\_på prenumerationsnamn_** **subscriptions** >   >  **providers** >  > **resourceGroups** > **_\<resursgruppsnamn\_\_>_** leverantörer av**Microsoft.Web** > **_\<sites-appnamn\_ _****sites** > > >  **config** > **authsettings**. 

Klicka på **Redigera,** ändra följande egenskap och klicka sedan på **Placera**. Var noga _ \<med\__ att ersätta domännamn>med den domän du vill ha.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Den här inställningen `domain_hint` lägger till frågesträngparametern i url:en för inloggningsomdirigering. 

> [!IMPORTANT]
> Det är möjligt för klienten `domain_hint` att ta bort parametern efter att ha fått omdirigerings-URL:en och sedan logga in med en annan domän. Så medan denna funktion är bekvämt, det är inte en säkerhetsfunktion.
>

## <a name="authorize-or-deny-users"></a>Auktorisera eller neka användare

App Service tar hand om det enklaste auktoriseringsfallet (d.v.s. avvisa oautentiserade begäranden), men appen kan kräva mer detaljerad auktoriseringsbeteende, till exempel begränsa åtkomsten till endast en viss grupp användare. I vissa fall måste du skriva anpassad programkod för att tillåta eller neka åtkomst till den inloggade användaren. I andra fall kan App Service eller din identitetsleverantör hjälpa till utan att kräva kodändringar.

- [Servernivå](#server-level-windows-apps-only)
- [Nivå för identitetsprovider](#identity-provider-level)
- [Programnivå](#application-level)

### <a name="server-level-windows-apps-only"></a>Servernivå (endast Windows-appar)

För alla Windows-appar kan du definiera auktoriseringsbeteende för IIS-webbservern genom att redigera *filen Web.config.* Linux-appar använder inte IIS och kan inte konfigureras via *Web.config*.

1. Navigera till`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. I webbläsarens utforskare av apptjänstfilerna navigerar du till *webbplats/wwwroot*. Om det inte finns en *Web.config* skapar **+**  > du den genom att välja **Ny fil**. 

1. Välj pennan för *Web.config* för att redigera den. Lägg till följande konfigurationskod och klicka på **Spara**. Om *Web.config* redan finns, `<authorization>` bara lägga till elementet med allt i den. Lägg till de konton som `<allow>` du vill tillåta i elementet.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Nivå för identitetsprovider

Identitetsleverantören kan tillhandahålla vissa nyckelfärdiga auktorisering. Ett exempel:

- För [Azure App Service](configure-authentication-provider-aad.md)kan du hantera åtkomst på [företagsnivå](../active-directory/manage-apps/what-is-access-management.md) direkt i Azure AD. Instruktioner finns i [Så här tar du bort en användares åtkomst till ett program](../active-directory/manage-apps/methods-for-removing-user-access.md).
- För [Google](configure-authentication-provider-google.md)kan Google API-projekt som tillhör en [organisation](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) konfigureras så att åtkomst endast tillåts till användare i organisationen (se [Googles supportsida **Konfigurera OAuth 2.0).** ](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Programnivå

Om någon av de andra nivåerna inte anger den auktorisering du behöver, eller om din plattform eller identitetsleverantör inte stöds, måste du skriva anpassad kod för att auktorisera användare baserat på [användarens anspråk](#access-user-claims).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Autentisera och auktorisera användare från på sluten tid (Windows)](app-service-web-tutorial-auth-aad.md)
> [Självstudiekurs: Autentisera och auktorisera användare från på (Linux)](containers/tutorial-auth-aad.md)

---
title: Anpassa autentisering och auktorisering i Azure App Service | Microsoft Docs
description: Visar hur du anpassar autentisering och auktorisering i Apptjänst och hämta användar- och annan token.
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
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: c41cb3ef2939fe7271b1f8738fcf0cb95c4b1111
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763150"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Anpassa autentisering och auktorisering i Azure App Service

Den här artikeln visar hur du anpassar [autentisering och auktorisering i Apptjänst](app-service-authentication-overview.md), och för att hantera identitet från ditt program. 

Om du vill komma igång snabbt, finns i något av följande kurser:

* [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)
* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Så här konfigurerar du din app för att använda Facebook-inloggning](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Så här konfigurerar du din app för att använda Google-inloggning](app-service-mobile-how-to-configure-google-authentication.md)
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Så här konfigurerar du din app för att använda Twitter-inloggning](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Konfigurera flera inloggningsalternativ

Portalen konfigurationen kan inte du nyckelfärdig presentera flera inloggningsalternativ för dina användare (till exempel både Facebook och Twitter). Men det är inte svårt att lägga till funktionen i ditt webbprogram. Steg som beskrivs i följande:

Först i den **autentisering / auktorisering** i Azure-portalen kan du konfigurera varje identitetsleverantör som du vill aktivera.

I **åtgärd att vidta när begäran inte har autentiserats**väljer **Tillåt anonyma begäranden (ingen åtgärd)**.

På sidan logga in eller navigeringsfältet eller någon annan plats för ditt webbprogram, lägga till en inloggning länk var och en av de leverantörer som du har aktiverat (`/.auth/login/<provider>`). Exempel:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

När användaren klickar på länken, öppnas respektive inloggningssidan om du vill logga in användaren.

## <a name="access-user-claims"></a>Åtkomst användaranspråk

Apptjänst skickar användaranspråk i ditt program med hjälp av särskilda huvuden. Externa förfrågningar är inte tillåtet att ställa in dessa huvuden, så att de finns bara om ange av App Service. Vissa exempel huvuden innehåller:

* X-MS-CLIENT--HUVUDNAMN
* X-MS-CLIENT-SÄKERHETSOBJEKT-ID

Kod som skrivs i valfritt språk eller ramverk kan hämta den information som krävs från dessa huvuden. För ASP.NET 4.6 appar i **ClaimsPrincipal** ställs in automatiskt med lämpliga värden.

Programmet kan även hämta ytterligare information om den autentiserade användaren genom att anropa `/.auth/me`. Mobile Apps server SDK: er ger helper metoder att arbeta med dessa data. Mer information finns i [hur du använder Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), och [arbeta med serverdelen .NET SDK för Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Hämta token i Appkod

Från din serverkod injekteras provider-specifik token i begärandehuvudet, så att du enkelt kan komma åt dem. I följande tabell visas möjliga token sidhuvud namn:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-konto | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Skicka en HTTP från din klientkod (till exempel en mobilapp eller i webbläsaren JavaScript) `GET` begäran om att `/.auth/me`. Returnerade JSON har provider-specifik-token.

> [!NOTE]
> Åtkomst-token är för att komma åt providern resurser, så att de finns bara om du konfigurerar din leverantör med en klienthemlighet. Information om hur du hämtar uppdaterings-tokens finns [uppdatering åtkomsttoken](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Uppdatera åtkomst-token

Du måste autentiseras användaren när din leverantör åtkomst-token upphör att gälla. Du kan undvika giltighetstid för token genom att göra en `GET` anrop till den `/.auth/refresh` slutpunkten för ditt program. När den anropas, uppdaterar Apptjänst automatiskt åtkomst-token i arkivet token för den autentiserade användaren. Efterföljande förfrågningar för token av app koden hämta uppdateras token. Men för token uppdatering fungerar arkivet token måste innehålla [uppdateringstoken](https://auth0.com/learn/refresh-tokens/) för providern. Sätt att få uppdaterings-tokens dokumenteras av varje leverantör, men i följande lista finns en kort sammanfattning:

- **Google**: Lägg till en `access_type=offline` frågesträngparametern till din `/.auth/login/google` API-anrop. Med Mobile Apps-SDK kan du lägga till parametern till ett av de `LogicAsync` överlagringar (se [Google uppdatera token](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: inte ger uppdaterings-tokens. Långlivade token ut inom 60 dagar (se [Facebook förfallodatum och tillägget åtkomsttoken](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: åtkomsttoken inte gälla (se [Twitter OAuth vanliga frågor och svar](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq)).
- **Account**: när [konfigurera autentiseringsinställningar för Microsoft-konto](app-service-mobile-how-to-configure-microsoft-authentication.md), Välj den `wl.offline_access` omfång.
- **Azure Active Directory**: I [ https://resources.azure.com ](https://resources.azure.com), gör du följande:
    1. Överst på sidan Välj **Skrivskyddstyp**.
    1. I den vänstra webbläsaren, navigerar du till **prenumerationer** > **_\<prenumeration\_namn_**   >  **resursgrupper** > _**\<resurs\_grupp\_namn >**_   >  **providers** > **Microsoft.Web** > **platser** > _**\<app \_namn >**_ > **config** > **authsettings**. 
    1. Klicka på **Redigera**.
    1. Ändra följande egenskaper. Ersätt  _\<app\_id >_ med Azure Active Directory-program-ID för tjänsten som du vill komma åt.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Klicka på **placera**. 

När din leverantör har konfigurerats kan du kan se om uppdaterings-tokens finns i arkivet token genom att anropa `/.auth/me`. 

Om du vill uppdatera åtkomst-token när som helst, bara anropa `/.auth/refresh` på alla språk. Följande kodavsnitt använder jQuery för att uppdatera åtkomst-token från en JavaScript-klient.

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

Om en användare återkallar behörigheterna för din app anrop till `/.auth/me` kan misslyckas med ett `403 Forbidden` svar. För att diagnostisera fel i loggarna programmet information.

## <a name="extend-session-expiration-grace-period"></a>Utöka respitperiod för sessionen upphör att gälla

När en autentiserad session upphör att gälla, är det respitperiod 72 timmar som standard. Du har rätt att uppdatera sessions-cookie eller sessionstoken med App Service utan reauthenticating användaren i den här tiden. Du kan endast anropa `/.auth/refresh` när din sessions-cookie eller sessionstoken blir ogiltigt och du behöver inte följa giltighetstid för token själv. När respitperioden 72 timmar onlinesäkerhetskopieringen kan måste användaren logga in igen att hämta en giltig sessions-cookie eller sessionstoken.

Om 72 timmar inte tillräckligt med tid för dig, kan du utöka det här fönstret upphör att gälla. Utöka giltighetstid under en längre tid kan ha betydande säkerhetsaspekter (till exempel när en autentiseringstoken läcka ut eller blir stulen). Därför bör du lämna standardinställningen 72 timmar eller ange förlängningen till det minsta värdet.

Kör följande kommando för att utöka fönstret Standard upphör att gälla den [moln Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Respittiden gäller bara för Apptjänst autentiserad session, inte token från identitetsleverantörer. Det finns inga respitperiod för token har upphört att gälla providern. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Begränsa domänen för att logga in konton

Både Account och Azure Active Directory kan du logga in från flera domäner. Account kan till exempel _outlook.com_, _live.com_, och _hotmail.com_ konton. Azure Active Directory kan valfritt antal anpassade domäner för inloggning-konton. Det här problemet kan vara oönskade för en intern app som du inte vill att vem som helst med en _outlook.com_ kontot till åtkomst. Följ dessa steg om du vill begränsa domännamnet för kontona inloggning.

I [ https://resources.azure.com ](https://resources.azure.com), gå till **prenumerationer** > **_\<prenumeration\_namn_**   >  **resursgrupper** > _**\<resurs\_grupp\_namn >**_   >  **providers** > **Microsoft.Web** > **platser**  >    _**\<app\_namn >**_ > **config** > **authsettings**. 

Klicka på **redigera**ändra följande egenskaper och klicka sedan på **placera**. Se till att ersätta  _\<domän\_namn >_ med den domän som du vill använda.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt (Windows)](app-service-web-tutorial-auth-aad.md)
> [Självstudier: autentisera och auktorisera användare slutpunkt till slutpunkt (Linux)](containers/tutorial-auth-aad.md)
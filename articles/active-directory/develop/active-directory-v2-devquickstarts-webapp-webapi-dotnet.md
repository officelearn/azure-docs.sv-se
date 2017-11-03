---
title: "Azure AD v2.0 .NET webb-app anropa API komma igång | Microsoft Docs"
description: "Hur du skapar en .NET MVC-Webbapp som anropar web services personliga Microsoft-konton och arbete eller skola konton för inloggning."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Anropa ett webb-API från en .NET-webbapp
Du kan snabbt lägga till autentisering i dina webbprogram och webb-API: er med stöd för både personliga Microsoft-konton och arbets-eller skolkonton med v2.0-slutpunkten.  Här ska vi skapa en MVC-webbapp som loggar användarna in med OpenID Connect med hjälp från Microsofts OWIN mellanprogram.  Webbprogrammet hämta OAuth 2.0-åtkomsttoken för en webb-api som skyddas av OAuth 2.0 som gör att skapa, läsa och ta bort på en viss användares ”uppgiftslistan”.

Den här självstudiekursen fokuserar huvudsakligen på använder MSAL för att hämta och använda åtkomst-token i en webbapp som beskrivs i fullständig [här](active-directory-v2-flows.md#web-apps).  Som krav kan du kanske vill först Lär dig hur du [lägga till enkel inloggning till en webbapp](active-directory-v2-devquickstarts-dotnet-web.md) eller hur du [att säkra ett webb-API](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Hämta exempelkoden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Du kan också [ladda ned den färdiga appen som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) eller klona den färdiga appen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa dessa [detaljerade steg](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** tilldelats din app måste den snart.
* Skapa en **App hemlighet** av den **lösenord** typ och kopiera värdet för senare
* Lägg till den **Web** plattform för din app.
* Ange rätt **omdirigerings-URI**. Omdirigerings-uri anger till Azure AD där autentisering svar som ska dirigeras - standarden för den här kursen är `https://localhost:44326/`.

## <a name="install-owin"></a>Installera OWIN
Lägg till NuGet-paket OWIN mellanprogram för att den `TodoList-WebApp` projekt med Package Manager-konsolen.  OWIN-mellanprogram används för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarens session och få information om användare, bland annat.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Logga in användaren
Nu konfigurera OWIN-mellanprogram för att använda den [autentiseringsprotokollet OpenID Connect](active-directory-v2-protocols.md).  

* Öppna den `web.config` filen i roten av den `TodoList-WebApp` projektet och ange din Apps konfigurationsvärden i den `<appSettings>` avsnitt.
  * Den `ida:ClientId` är den **program-Id** tilldelats din app i portalen för registrering.
  * Den `ida:ClientSecret` är den **App hemlighet** du skapade i portalen för registrering.
  * Den `ida:RedirectUri` är den **omdirigerings-Uri** du angav på portalen.
* Öppna den `web.config` filen i roten av den `TodoList-Service` projektet och Ersätt den `ida:Audience` med samma **program-Id** som ovan.
* Öppna filen `App_Start\Startup.Auth.cs` och Lägg till `using` instruktioner för bibliotek från ovan.
* I samma fil implementera den `ConfigureAuth(...)` metoden.  De parametrar som du anger i `OpenIDConnectAuthenticationOptions` fungerar som koordinater för din app för att kommunicera med Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Använd MSAL för att få åtkomst-token
I den `AuthorizationCodeReceived` meddelande ska vi använda [OAuth 2.0 tillsammans med OpenID Connect](active-directory-v2-protocols.md) att lösa in authorization_code för tjänsten att göra en åtkomst-token.  MSAL kan göra den här processen enkel du:

* Installera först förhandsversionen av MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* Och lägga till en annan `using` -instruktionen för att den `App_Start\Startup.Auth.cs` filen för MSAL.
* Lägg nu till en ny metod i `OnAuthorizationCodeReceived` händelsehanterare.  Den här hanteraren MSAL ska använda för att få en åtkomsttoken att API för att göra-lista och lagrar token i MSAL'S token-cache för senare:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* I web apps har MSAL en extensible token-cache som kan användas för att lagra token.  Det här exemplet implementerar den `NaiveSessionCache` som använder HTTP-session lagring till cache-token.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Anropa webb-API
Nu är det dags att faktiskt använder access_token som du har införskaffade i steg 3.  Öppna webbapp `Controllers\TodoListController.cs` blir alla CRUD-begäranden till API för att göra-lista.

* Du kan använda MSAL igen här att hämta access_tokens från MSAL-cachen.  Lägg först till en `using` -instruktion för MSAL till den här filen.
  
    `using Microsoft.Identity.Client;`
* I den `Index` åtgärdens, Använd MSAL `AcquireTokenSilentAsync` metod för att hämta en access_token som kan användas för att läsa data från tjänsten för att göra-lista:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* Exemplet lägger sedan till den resulterande token HTTP GET-begäran som den `Authorization` rubriken, som att göra-lista tjänsten använder för att autentisera begäran.
* Om tjänsten för att göra-lista returnerar en `401 Unauthorized` svar, access_tokens i MSAL har blivit ogiltigt av någon anledning.  I det här fallet bör du släppa alla access_tokens från MSAL cache och visar användaren ett meddelande om att de kan behöva logga in igen, vilket startar om token förvärv flödet.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* På liknande sätt bör MSAL kan inte returnera en access_token av någon anledning, du instruera användaren att logga in igen.  Detta är lika enkelt som fångar in alla `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* På exakt samma `AcquireTokenSilentAsync` anropet är implementd i den `Create` och `Delete` åtgärder.  Du kan använda den här metoden MSAL för att hämta access_tokens när du behöver dem i din app i web apps.  MSAL hand tar om införskaffa cachelagring och uppdatera token för dig.

Slutligen skapar och kör appen!  Logga in med ett Account eller en Azure AD-kontot och hur användarens identitet visas i det övre navigeringsfältet.  Lägg till och ta bort några objekt från användarens att göra-lista för att se OAuth 2.0 skyddade API-anrop i åtgärden.  Nu har du en webbprogrammet & webb-API, både skyddas med standardprotokollen, som kan autentisera användare med både sina personliga och arbete/skola konton.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) [här](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Nästa steg
För ytterligare resurser, kolla:

* [Utvecklarhandbok v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow ”azure-active-directory” taggen >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.


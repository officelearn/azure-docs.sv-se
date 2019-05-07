---
title: 'Webbapp som anropar webb-API: er (kod-konfiguration) – Microsoft identity-plattformen'
description: 'Lär dig att skapa en webbapp som anropar webb-API: er (konfiguration av appens kod)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95a5e1ed89b6330a0b6a49cb20d8bf0ef3587d48
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074747"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webbapp att anrop webb-API: er – kod konfiguration

Som visas i den [scenario för Webbappar loggar in användare](scenario-web-app-sign-user-overview.md), med hänsyn till att logga in användaren delegeras till den öppna ID connect (OIDC) mellanprogram, du vill hook upp i OIDC-processen. Sättet att göra det är olika beroende på ramverket du använde (här ASP.NET och ASP.NET Core), men i slutändan prenumererar du på mellanprogram OIDC händelser. Principen är att:

- Du låta ASP.NET eller ASP.NET core begär en auktoriseringskod. Genom att göra låta detta ASP.NET/ASP.NET core tar användaren logga in och ge samtycke,
- Du ska prenumerera mottagningen av Auktoriseringskoden av webbappen.
- När koden auth tas emot, ska du använda MSAL bibliotek för att lösa in koden och de resulterande åtkomsttoken och uppdatera token-butik i token-cache. Därifrån kan kan cacheminnet användas i andra delar av programmet för att hämta andra token tyst.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotek som har stöd för Web App-scenarier

De bibliotek som stöder auktoriseringskodsflödet för Web Apps är:

| MSAL bibliotek | Beskrivning |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds är .NET Framework och .NET Core-plattformar (inte UWP, Xamarin.iOS och Xamarin.Android som dessa plattformar används för att skapa offentliga klientprogram) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Utveckling i förlopp – förhandsversion |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Utveckling i förlopp – förhandsversion |

## <a name="aspnet-core-configuration"></a>ASP.NET Core-konfiguration

I ASP.NET Core, uppstår i den `Startup.cs` filen. Du vill prenumerera på den `OnAuthorizationCodeReceived` öppna ID connect-event och anropa MSAL från den här händelsen. NETS metoden `AcquireTokenFromAuthorizationCode` som påverkar för att lagra i token-cache, åtkomsttoken för de begärda omfång och en uppdateringstoken som ska användas för att uppdatera åtkomsttoken när den är nära förfallodatum eller för att hämta en token för samma användare , men för en annan resurs.

Kommentarerna i koden nedan hjälper dig att förstå vissa knepigt aspekter av vävning MSAL.NET och ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

I ASP.NET Core använder att skapa konfidentiell klientprogrammet information som är i HttpContext. Den här HttpContext känner till URL: en för Webbappen och den inloggade användaren (i en `ClaimsPrincipal`). Den använder också ASP.NET Core-konfigurationen, som har ett ”AzureAD”-avsnitt och som är bunden till den `_applicationOptions` datastruktur. Programmet måste slutligen att underhålla token cacheminnen.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` verkligen redeems auktoriseringskod som begärs av ASP.NET och hämtar de token som läggs till MSAL.NET användaren token-cache. Därifrån kan de är sedan används i ASP.NET Core-styrenheter.

## <a name="aspnet-configuration"></a>ASP.NET Configuration

Hanteringen av saker i ASP.NET är liknande, förutom att konfigurationen av OpenIdConnect och din prenumeration på den `OnAuthorizationCodeReceived` händelse inträffar i den `App_Start\Startup.Auth.cs` filen. Du hittar liknande koncept, förutom att här måste du ange RedirectUri i konfigurationsfilen, vilket är lite mindre robust:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
  NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET Token-cache för en webbapp med ASP.NET (kärnor)

I web apps (eller webb-API: er som en följd av fakta) token-cache-implementeringen skiljer sig från token-cache-implementeringar skrivbordsprogram (vilket är ofta [filbaserad](scenario-desktop-acquire-token.md#file-based-token-cache). Det kan använda ASP.NET/ASP.NET Core-session eller en Redis-cache eller en databas eller även Azure BLOB-lagring. I koden är kodfragmentet ovan detta objekt av den `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` metodanrop som binder en cachetjänst. Informationen om vad som händer här ligger utanför omfånget för den här guiden för scenariot, men länkar finns nedan.

> [!IMPORTANT]
> En mycket viktiga för att upptäcka är att för Webbappar och webb-API: er, det ska vara en token-cache per användare (per konto). Du behöver att serialisera tokens cacheminne för varje konto.

Exempel på hur du använder token cacheminnen för webbappar och webb-API: er är tillgängliga i den [ASP.NET Core Web app självstudien](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) i fasen [2-2-Tokencache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Om du vill implementeringar har en titt på följande mapp [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) i den [microsoft-autentisering-tillägg-för-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteket (i den [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mapp.

## <a name="next-steps"></a>Nästa steg

Nu när användaren loggar in lagras en token i token-cache. Nu ska vi se hur det används sedan i andra delar av webbappen.

> [!div class="nextstepaction"]
> [Logga in till Webbappen](scenario-web-app-call-api-sign-in.md)

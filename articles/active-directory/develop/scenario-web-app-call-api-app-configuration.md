---
title: 'Webbapp som anropar webb-API: er (kod konfiguration) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (appens kod konfiguration)'
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 391546b4d3ac9ad3674897b39284fdd16e9025a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562274"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webbapp som anropar webb-API: er – kod konfiguration

Som det visas i [scenariot med Web App Sign-in-Users](scenario-web-app-sign-user-overview.md), så att den inloggade användaren är delegerad till program varan Open ID Connect (OIDC), som du vill koppla samman i OIDC-processen. Det sätt på vilket du kan göra detta beror på vilket ramverk du använde (här ASP.NET och ASP.NET Core), men i slutet kommer du att prenumerera på OIDC händelser mellan flera. Principen är att:

- Du ska låta ASP.NET eller ASP.NET Core begära en auktoriseringskod. Genom att göra den här ASP.NET/ASP.NET-kärnan kan användaren logga in och godkänna,
- Du prenumererar på mottagning av auktoriseringskod från webbappen.
- När auth-koden tas emot använder du MSAL-bibliotek för att lösa in koden och de resulterande åtkomsttoken och uppdaterade token-lagringen i token-cachen. Därifrån kan du använda cachen i andra delar av programmet för att hämta andra token tyst.

> [!NOTE]
> Kodfragmenten från den här artikeln extraheras från följande exempel på GitHub, som är fullt funktionella:
>
> - [Stegvis självstudie för ASP.NET Core-webbappar](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Exempel på ASP.NET-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotek som stöder webb program scenarier

Biblioteken som stöder auktoriseringskod för Web Apps är:

| MSAL-bibliotek | Beskrivning |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds är .NET Framework-och .NET Core-plattformar (inte UWP, Xamarin. iOS och Xamarin. Android som dessa plattformar används för att bygga offentliga klient program) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Utveckling pågår – i offentlig för hands version |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Utveckling pågår – i offentlig för hands version |

## <a name="aspnet-core-configuration"></a>ASP.NET Core konfiguration

I ASP.net Core sker saker i `Startup.cs` filen. Du vill prenumerera `OnAuthorizationCodeReceived` på händelsen öppna ID Connect och från den här händelsen anropar du MSAL. NET-metoden `AcquireTokenFromAuthorizationCode` som har att lagra i token cache, åtkomsttoken för begärd `scopes`och en uppdateringstoken som ska användas för att uppdatera åtkomsttoken när den upphör att gälla, eller för att få en token åt samma användare , men för en annan resurs.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

Kommentarerna i koden nedan hjälper dig att förstå några praktiska aspekter av att väva MSAL.NET och ASP.NET Core. Fullständig information finns i den stegvisa självstudien om [ASP.net Core Web App, kapitel 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
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

I ASP.NET Core använder det konfidentiella klient programmet information som finns i HttpContext. Detta `HttpContext` känner till om webb adressen för webbappen och den inloggade användaren (i a `ClaimsPrincipal`). 

Den använder också ASP.NET Core konfiguration, som har avsnittet "AzureAD", som är kopplat till båda:

- data strukturen av typen [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`
- instansen av typen [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) som definierats i ASP.net Core `Authentication.AzureAD.UI`. `azureAdOptions` Slutligen måste programmet hantera token-cacheminnen.

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
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Mer information om token cache-providers finns i självstudier för [ASP.net Core Web Apps | Token-cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`verkligen löser den auktoriseringskod som begärs av ASP.NET och hämtar de token som har lagts till i MSAL.NET cache för användartoken. Därifrån används de, i ASP.NET Core kontrollanter.

## <a name="aspnet-configuration"></a>ASP.NET-konfiguration

Hur ASP.net hanterar saker liknar varandra, förutom att konfigurationen av OpenIdConnect och prenumerationen på `OnAuthorizationCodeReceived` händelsen inträffar `App_Start\Startup.Auth.cs` i filen. Du hittar liknande koncept, förutom att här måste du ange RedirectUri i konfigurations filen, vilket är mindre robust:

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

I stället för en klient hemlighet kan det konfidentiella klient programmet också bevisa sin identitet med hjälp av ett klient certifikat eller en klient kontroll.
Att använda klient kontroll är ett avancerat scenario som beskrivs i [klientens kontroll](msal-net-client-assertions.md)

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET token cache för en ASP.NET (Core)-webbapp

I Web Apps (eller webb-API: er som själva fakta) skiljer sig implementationen för token-cacheminnet från programapparna för cachelagring av [](scenario-desktop-acquire-token.md#file-based-token-cache)appar (som ofta är filbaserade. Den kan använda ASP.NET/ASP.NET Core-sessionen, eller en Redis-cache, en databas eller till och med Azure Blob Storage. I kodfragmentet ovan är detta objektet i `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` metod anropet, vilket binder en cache-tjänst. Informationen om vad som händer här ligger utanför omfattningen för den här scenario guiden, men länkarna visas nedan.

> [!IMPORTANT]
> En mycket viktig sak att realisera är att för webbappar och webb-API: er bör det finnas ett token cache per användare (per konto). Du måste serialisera token-cachen för varje konto.

Exempel på hur du kan använda token-cacheminnen för webbappar och webb-API: er finns i självstudien [ASP.net Core Web Apps](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) i fas [2-2-token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). För implementeringar har du en titt på följande mapp [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) i biblioteket [Microsoft-Authentication-Extensions-for-dotNet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (i mappen [Microsoft. Identity. client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) .

## <a name="next-steps"></a>Nästa steg

När användaren sedan loggar in en token lagras i token cache. Nu ska vi se hur det används i andra delar av webbappen.

> [!div class="nextstepaction"]
> [Logga in på webbappen](scenario-web-app-call-api-sign-in.md)

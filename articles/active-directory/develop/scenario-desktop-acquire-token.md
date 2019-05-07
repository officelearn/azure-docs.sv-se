---
title: 'Skrivbordsappen som anropar webb-API: er (skaffa en token som appen) – Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp som anropar webb-API: er (skaffa en token som appen |)'
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
ms.openlocfilehash: 6d4389af86e27ddb04f5a3e5f53c5509eeede005
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075347"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Skrivbordsappen som anropar webb-API: er – hämta en token

När du har skapat du `IPublicClientApplication`, använder du det för att hämta en token som du sedan använder för att anropa ett webb-API.

## <a name="recommended-pattern"></a>Rekommenderat mönster

Webb-API: definieras av dess `scopes`. Den upplevelsen du tillhandahåller i ditt program, är det mönster som du vill använda:

- Systematiskt försöker hämta en token från token cachen genom att anropa `AcquireTokenSilent`
- Om det här anropet misslyckas använder den `AcquireToken` flöde som du vill använda (här representeras av `AcquireTokenXX`)

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

Här är nu information för de olika sätten att hämta token i ett skrivbordsprogram

## <a name="acquiring-a-token-interactively"></a>Skaffa en token som interaktivt

I följande exempel visas minimal kod att hämta en token interaktivt för att läsa in användarens profil med Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Obligatoriska parametrar

`AcquireTokenInteractive` har endast en obligatorisk parameter ``scopes``, som innehåller en uppräkning med strängar som definiera omfång för vilka en token krävs. Om token är för Microsoft Graph, finns krävs scope i api-referens för varje Microsoft graph API i avsnittet med namnet ”behörigheter”. Till exempel att [lista användarens kontakter](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), omfånget ”User.Read”, ”Contacts.Read” måste användas. Se även [behörighetsreferens för Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

På Android, måste du också ange den överordnade aktiviteten (med hjälp av `.WithParentActivityOrWindow`, se nedan) så att token kommer tillbaka till den överordnade aktiviteten efter interaktionen. Om du inte anger den ett undantagsfel när du anropar `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Specifika valfria parametrar

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Att interaktiva och är UI viktigt. `AcquireTokenInteractive` har en specifik valfri parameter som aktiverar för att ange för plattformar som stöder den överordnade Användargränssnittet. När de används i ett skrivbordsprogram `.WithParentActivityOrWindow` har en annan typ beroende på vilken plattform:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Anmärkning:

- På .NET Standard, den förväntade `object` är en `Activity` på Android, en `UIViewController` på iOS-, en `NSWindow` på MAC och en `IWin32Window` eller `IntPr` på Windows.
- På Windows, måste du anropa `AcquireTokenInteractive` tråd så att den inbäddade webbläsaren hämtar rätt kontext för synkronisering av Användargränssnittet i användargränssnittet.  Anropa inte från UI-tråden kan det leda till att meddelanden inte pump korrekt och/eller dödläge scenarier med Användargränssnittet. Ett sätt att anropa MSAL från UI-tråden om du inte i UI-tråden är redan att använda den `Dispatcher` på WPF.
- Om du använder WPF, för att hämta ett fönster från en WPF-kontroll kan du använda `WindowInteropHelper.Handle` klass. Anropet är sedan från en WPF-kontroll (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` används för att styra interaktivitet med användaren genom att ange en uppmaning

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klassen definierar följande konstanter:

- ``SelectAccount``: tvingar STS att visa dialogrutan för val av konto som innehåller konton som användaren har en session. Det här alternativet är användbart när program utvecklare vill du tillåta att användaren väljer bland olika identiteter. Det här alternativet styr MSAL för att skicka ``prompt=select_account`` till identitetsleverantören. Det här alternativet är standard, så att den inte av bra för att ge bästa möjliga upplevelse baserat på informationen som är tillgänglig för (konto, förekomsten av en session för användare och så vidare. ...). Inte ändra det om du har goda skäl att göra det.
- ``Consent``: gör att programmet utvecklare för att tvinga användaren att tillfrågas även om godkännande har beviljats innan. I det här fallet MSAL skickar `prompt=consent` till identitetsleverantören. Det här alternativet kan användas i vissa fokuserar säkerhetsprogram när styrning för organisation kräver att användaren kan se godkännande i dialogrutan varje gång programmet används.
- ``ForceLogin``: gör det möjligt för programutvecklare att få användaren att ange några autentiseringsuppgifter av tjänsten även om den här användarprompten skulle behövas. Det här alternativet kan vara användbart om en token hämtades misslyckas, så att användarna återexport-registrerings-modulen. I det här fallet MSAL skickar `prompt=login` till identitetsleverantören. Igen, har vi sett det används i vissa fokuserar säkerhetsprogram när styrning för organisation kräver att användaren relogs in varje gång som de har åtkomst till vissa delar av ett program.
- ``Never`` (för .NET 4.5 och WinRT endast) kommer inte uppmana användaren, men i stället att försöka använda den cookie som lagras i dolda embedded webbvyn (se nedan: Webbvyer i MSAL.NET). Med det här alternativet kan misslyckas, och i så fall `AcquireTokenInteractive` genereras ett undantagsfel för att meddela att en gränssnittsinteraktioner krävs, och du måste använda en annan `Prompt` parametern.
- ``NoPrompt``: Inte skicka någon uppmaning till identitetsleverantören. Det här alternativet är bara användbara för Azure AD B2C redigera principer (se [B2C ärendets](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Den här modifieraren används i ett avancerat scenario där du vill att användaren att godkänna före förskott flera resurser (och inte vill använda inkrementella samtycke, som normalt används tillsammans med MSAL.NET / Microsoft identity-plattformen v2.0). Mer information finns i [How-to: få användaren att godkänna förskott för flera resurser](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi är en utökningsbarhet

`WithCustomWebUi` är en tidpunkt för utökningsbarhet som gör att du anger ditt eget användargränssnitt i offentliga klientprogram och att användaren går igenom /Authorize slutpunkten för identitetsprovidern och låter dem logga in och godkänna. MSAL.NET kan sedan lösa in Autentiseringskod för och hämta en token. Den används till exempel ha elektroner program (till exempel VS Feedback) ger web interaktion, men låter du MSAL.NET att utföra de flesta av arbetet i Visual Studio. Du kan också använda den om du vill ge UI automation. I offentliga klientprogram MSAL.NET används PKCE-standarden ([RFC 7636 - bevis nyckeln för kod Exchange med OAuth offentliga klienter](https://tools.ietf.org/html/rfc7636)) så att security iakttas: Endast MSAL.NET kan lösa in koden.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Hur du använder WithCustomWebUi

För att kunna använda `.WithCustomWebUI`, måste du:
  
  1. Implementera de `ICustomWebUi` gränssnitt (se [här](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Du behöver i princip att implementera en metod `AcquireAuthorizationCodeAsync` acceptera authorization code-URL (beräknas av MSAL.NET), så att användaren går igenom interaktionen med identitetsprovidern och returnerar sedan tillbaka den URL som identitetsprovidern skulle har anropat implementeringen tillbaka (inklusive auktoriseringskod). Om du har problem med din implementering utlösa en `MsalExtensionException` undantag till ett snyggt sätt samarbeta med MSAL.
  2. I din `AcquireTokenInteractive` -anrop som du kan använda `.WithCustomUI()` modifieraren skicka instans av dina anpassade webb-UI

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Exempel på implementering av ICustomWebUi i test automation - SeleniumWebUI

MSAL.NET-teamet har om vår UI-test för att använda den här mekanismen för utökningsbarhet. Om du är intresserad av kan du ta en titt på de [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) klassen i källkoden MSAL.NET

#### <a name="other-optional-parameters"></a>Andra valfria parametrar

Mer information om alla andra valfria parametrar för `AcquireTokenInteractive` från referensdokumentation för [AcquireTokenInteractiveParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om du vill att logga in en domänanvändare i en domän eller Azure AD ansluten dator, måste du använda:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Villkor

- AcquireTokenByIntegratedWindowsAuth (IWA) kan endast användas för **federerade** användare, som är, användare skapas i en Active Directory och backas upp av Azure Active Directory. Användare som skapas direkt i AAD, utan AD säkerhetskopiering - **hanteras** användare - kan inte använda det här autentiseringsflödet. Den här begränsningen påverkar inte flödet användarnamn/lösenord.
- IWA är för appar som är skrivna för .NET Framework, .NET Core och UWP-plattformar
- IWA kringgås inte MFA (multifaktorautentisering). Om MFA har konfigurerats kan IWA misslyckas om en MFA-kontrollen krävs, eftersom MFA kräver interaktion från användaren.
  > [!NOTE]
  > Det är svårt att den här. IWA är icke-interaktivt, men 2FA kräver användarinteraktion. Du kan inte styra när identitetsprovidern begär 2FA som ska utföras, administratör har. Från våra observationer krävs 2FA när du loggar in från ett annat land när inte ansluten via VPN till ett företagsnätverk, och ibland även när ansluten via VPN. Förväntar sig inte en deterministisk uppsättning regler, Azure Active Directory använder AI att kontinuerligt lära dig om 2FA krävs. Du bör vid användaruppmaning (Interaktiv autentisering eller enhet kod flow) om IWA misslyckas.

- Behörighet som har skickats in den `PublicClientApplicationBuilder` måste vara:
  - klient-ed (i formatet `https://login.microsoftonline.com/{tenant}/` där `tenant` är antingen guid som representerar klient-ID eller en domän som är kopplade till klienten.
  - för alla arbets- och skolkonton (`https://login.microsoftonline.com/organizations/`)

  > Personliga Microsoft-konton stöds inte (du kan inte använda/Common eller /consumers klienter)

- Eftersom integrerad Windows-autentisering är ett tyst flöde:
  - användare av ditt program måste ha tidigare godkänt att använda programmet
  - eller administratör måste tidigare har samtyckt till alla användare i klienten att använda programmet.
  - Med andra ord:
    - antingen du som utvecklare har tryckt på **bevilja** knappen på Azure portal för dig själv
    - eller en klientadministratör har tryckts ned den **Grant/revoke administratörens godkännande för {}** knappen i den **API-behörigheter** fliken registreringen för programmet (se [Lägg till behörigheter till åtkomst till webb-API: er](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - eller du har angett ett sätt för användarna att godkänna att programmet (se [begär enskilda användargodkännande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - eller du har angett ett sätt för administratör att ge samtycke för programmet (se [administratörsmedgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Det här flödet har aktiverats för .net skrivbords-, .net core- och Windows Universal (UWP) appar. Endast överlagringen tar användarnamnet är tillgängligt på .NET core som .NET Core-plattformen inte kan ställa användarnamnet för att Operativsystemet.
  
Mer information om medgivande finns [v2.0 behörigheter och samtycke](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Hur du använder den

Normalt behöver endast en parameter (`scopes`). Men beroende på hur din Windows-administratör har konfigurerat principerna, det kan vara möjligt att program på windows-datorn inte får Leta upp den inloggade användaren. I så fall kan använda en andra `.WithUsername()` och ange användarnamnet för den inloggade användaren som UPN-format - `joe@contoso.com`.

I följande exempel ger det mest aktuella ärendet, förklaringar av vilken typ av undantag som du kan hämta och åtgärder

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Lista över möjliga modifierare på AcquireTokenByIntegratedWindowsAuthentication, finns i [AcquireTokenByIntegratedWindowsAuthParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Användarnamn / lösenord

Du kan också hämta en token genom att ange användarnamn och lösenord. Det här flödet är begränsad och rekommenderas inte, men det finns fortfarande använda fall där det är nödvändigt.

### <a name="this-flow-isnt-recommended"></a>Det här flödet rekommenderas inte

Det här flödet är **rekommenderas inte** eftersom ditt program som en användare ombeds ange sina lösenord inte är säker. Mer information om det här problemet finns i [i den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Prioriterade flödet för att skaffa en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Annars kan du också använda [kodflöde för enhet](https://aka.ms/msal-net-device-code-flow)

> Även om detta är användbart i vissa fall (DevOps-scenario), om du vill använda användarnamn/lösenord i interaktiva scenarier där du anger din onw Användargränssnittet, bör du tycker om hur du flyttar från den. Med hjälp av användarnamn/lösenord du ge upp ett antal saker:

> - viktiga innehavare av moderna identitet: lösenord hämtar fiskas återupprepas. Eftersom vi har detta begrepp av en resurs-hemlighet som kan fångas.
> Det här är inte kompatibel med lösenordslös.
> - användare som behöver du MFA kommer inte kunna logga in (eftersom det finns inga åtgärder från)
> - Användare kommer inte att kunna enkel inloggning

### <a name="constraints"></a>Villkor

Det gäller även följande begränsningar:

- Flödet användarnamn/lösenord inte är kompatibel med villkorlig åtkomst och autentisering med flera faktorer: Om din app körs i en Azure AD-klient där administratör kräver Multi-Factor authentication kan använda du följaktligen kommer inte det här flödet. Många företag gör det.
- Det fungerar endast för arbete och skola konton (inte MSA)
- Flödet är tillgänglig på .net desktop- och .net core, men inte på UWP

### <a name="b2c-specifics"></a>B2C-specifika

[Mer information om hur du använder ROPC med B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Hur du använder den?

`IPublicClientApplication`innehåller metoden `AcquireTokenByUsernamePassword`

I följande exempel visar ett förenklat ärende

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

I följande exempel ger det mest aktuella ärendet, förklaringar av vilken typ av undantag som du kan hämta och åtgärder

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See http://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Mer information om alla modifierare som kan tillämpas på `AcquireTokenByUsernamePassword`, se [AcquireTokenByUsernamePasswordParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Kommandoradsverktyget (utan webbläsare)

### <a name="device-code-flow-why-and-how"></a>Enheten flow varför? och hur?

Om du skriver ett kommandoradsverktyg (som inte har webbkontroller), och kan inte eller inte vill använda de tidigare flödena, måste du använda `AcquireTokenWithDeviceCode`.

Interaktiv autentisering med Azure AD kräver en webbläsare (Mer information finns i [användningen av webbläsare](https://aka.ms/msal-net-uses-web-browser)). För att autentisera användare på enheter eller operativsystem som inte har en webbläsare, kan kodflöde för enheten dock att användare kan använda en annan enhet (till exempel en annan dator eller en mobiltelefon) för att logga in interaktivt. Programmet hämtar token via en tvåstegsprocess som särskilt utformats för dessa enheter/OS med hjälp av kodflöde för enheten. Exempel på sådana program är program som körs på iOT- eller kommandoradsverktyg (CLI). Tanken är att:

1. När autentisering av användare krävs appen innehåller en kod och uppmanar användaren att använda en annan enhet (till exempel en internet-anslutna smartphone) för att navigera till en URL (till exempel `http://microsoft.com/devicelogin`), där användaren uppmanas att ange koden. Att klar sidan kommer att ge användaren genom en normal autentiseringsupplevelse, inklusive medgivande och multifaktorautentisering om det behövs.

2. Efter lyckad autentisering kommandoradsappen får de nödvändiga token via en tillbaka-kanal och använda den för att utföra webb-API-anrop som behövs.

### <a name="code"></a>Kod

`IPublicClientApplication`innehåller en metod med namnet `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Den här metoden tar som parametrar:

- Den `scopes` begär en åtkomsttoken för
- En motringning som tar emot den `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Följande exempelkod visar det mest aktuella ärendet, med förklaringar av vilken typ av undantag som du kan hämta och deras lösning.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
  {
       // This will print the message on the console which tells the user where to go sign-in using
       // a separate browser and the code to enter once they sign in.
       // The AcquireTokenWithDeviceCode() method will poll the server after firing this
       // device code callback to look for the successful login of the user via that browser.
       // This background polling (whose interval and timeout data is also provided as fields in the
       // deviceCodeCallback class) will occur until:
       // * The user has successfully logged in via browser and entered the proper code
       // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
       // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
       //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
       Console.WriteLine(deviceCodeResult.Message);
       return Task.FromResult(0);
  }).ExecuteAsync();

  Console.WriteLine(result.Account.Username);
  return result;
 }
 catch (MsalServiceException ex)
 {
  // Kind of errors you could have (in ex.Message)

  // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
  // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
  // your public client application with the following authorities:
  // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

  // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
  // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

  // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
  // no active subscriptions for the tenant. Check with your subscription administrator.
  // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
  // tenantId (GUID) or tenant domain name.
 }
 catch (OperationCanceledException ex)
 {
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Filbaserad token-cache

I MSAL.NET som en token minnescache standard.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialisering kan anpassas i Windows-skrivbordsappar och web apps/webb-API: er

Om du inte gör allt extra, när det gäller .NET Framework och .NET core varar token cachen i minnet för hela programmet. Att förstå varför serialisering inte tillhandahålls direkt, komma ihåg MSAL .NET desktop/core-program kan vara konsolen eller Windows-program (som skulle ha åtkomst till filsystemet), **utan även** webbprogram eller webb-API. Dessa webbappar och webb-API: er kan använda en specifik cache-mekanismer som databaser, distribuerad cache, redis-cacher och så vidare. Om du vill att ett program för beständiga token-cache i .NET Desktop eller Core, måste du anpassa serialisering.

Klasser och gränssnitt som ingår i token-cache serialisering är följande typer:

- ``ITokenCache``, som definierar händelser att prenumerera på token cachebegäranden för serialisering, samt metoder för att serialisera eller deserialisera cache i olika format (ADAL v3.0, MSAL 2.x och MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` skickas en motringning på händelser så att du kan hantera serialisering. de ska anropas med argument av typen ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` endast innehåller de ``ClientId`` på programmet och en referens till användaren som token är tillgänglig

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET skapar token cacheminnen för dig och förser dig med den `IToken` cachelagra när du anropar ett programs `GetUserTokenCache` och `GetAppTokenCache` metoder. Du ska inte implementerar gränssnittet själv. Det är ditt ansvar, när du implementerar en anpassad tokencache-serialisering att:
>
> - Reagera på `BeforeAccess` och `AfterAccess` ”händelser”. Den`BeforeAccess` ombudet är ansvarig för att deserialisera cache, medan den `AfterAccess` en ansvarar för serialisering av cachen.
> - En del av händelserna lagra eller läsa in blobbar som skickas via argumentet händelse till det lagringsutrymme som du vill.

Strategierna är olika beroende på om du skapar en token-cache-serialisering för en offentlig klient (skrivbord) eller en konfidentiell klientprogram (web app/web API, daemon app).

Sedan MSAL V2.x har du flera alternativ, beroende på om du vill serialisera cachen bara till formatet MSAL.NET (enhetligt format cacheminne som är vanligt med MSAL, utan även på plattformarna), eller om du vill stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Token-cache serialisering av ADAL V3.

Anpassning av Token cache serialisering att dela SSO-tillstånd mellan ADAL.NET 3.x ADAL.NET 5.x och MSAL.NET förklaras delvis i följande exempel: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Enkel tokencache serialisering (MSAL)

Nedan visas ett exempel på en naïve implementering av anpassade serialisering av en token cache för datorprogram. Användaren token här cache i en fil i samma mapp som programmet.

När du har skapat programmet kan du aktivera serialisering genom att anropa ``TokenCacheHelper.EnableSerialization()`` skicka programmet `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Hjälparklassen ser ut som följande kodavsnitt:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

En förhandsversion av en produkt kvalitet tokencache filbaserade serialiserare för offentliga klientprogram (för program som körs på Windows, Mac och linux) är tillgänglig från den [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) bibliotek med öppen källkod. Du kan inkludera den i dina program från följande nuget-paket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> FRISKRIVNING. Microsoft.Identity.Client.Extensions.Msal biblioteket är ett tillägg över MSAL.NET. Klasserna i biblioteken kan hamna i MSAL.NET i framtiden, direkt eller med större ändringar.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Dubbel tokencache serialisering (MSAL unified cache + ADAL V3)

Om du vill implementera tokencache serialisering båda med enhetligt cachelagra format (vanligt att ADAL.NET 4.x och MSAL.NET 2.x, och med andra MSALs av samma generation eller äldre, på samma plattform), du kan få inspiration av följande kod :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Den här gången fonthelper-klass som ser ut som följande kod:

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API från skrivbordsappen](scenario-desktop-call-api.md)

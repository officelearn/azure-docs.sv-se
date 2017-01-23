---
title: Azure AD B2C | Microsoft Docs
description: "Hur du skapar ett .NET-webb-API med Azure Active Directory B2C som skyddas med hjälp av OAuth 2.0-åtkomsttoken för autentisering."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1062af9abfd167dd251621a43943dea399aed027


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: Skapa ett .NET-webb-API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Med Azure Active Directory (Active AD) B2C kan du skydda ett webb-API med hjälp av OAuth 2.0-åtkomsttoken. Med hjälp av dessa token kan dina klientappar som använder Azure AD B2C autentisera mot API:et. Den här artikeln visar hur du skapar ett .NET Model-View-Controller (MVC) API med "att göra-lista" som tillåter användare att utföra CRUD-uppgifter. Webb-API:t skyddas med hjälp av Azure AD B2C och tillåter endast autentiserade användare att hantera sina att göra-listor.

## <a name="create-an-azure-ad-b2c-directory"></a>Skapa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en B2C-katalog [skapar du en](active-directory-b2c-get-started.md) innan du fortsätter den här guiden.

## <a name="create-an-application"></a>Skapa ett program
Nu måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

* Lägga till en **webbapp** eller ett **webb-API** i programmet.
* Använda **omdirigerings-URI:n** `https://localhost:44316/` för webbappen. Det här är standardplatsen för klienten för webbappen i det här kodexemplet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver det senare.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Skapa dina principer
I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Klienten i det här kodexemplet innehåller tre identitetsmiljöer: registrering, inloggning och profilredigering. Du måste skapa en princip för varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Tänk på följande när du skapar dina tre principer:

* Välj antingen **Registrering med användar-ID** eller **Registrering med e-postadress** i bladet för identitetsproviders.
* Välj **Visningsnamn** och andra registreringsattribut i registreringsprincipen.
* Välj **Visningsnamn** och **Objekt-ID** som programanspråk för varje princip. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat dem. Du behöver principnamnen senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat de tre principerna är du redo att bygga din app.

## <a name="download-the-code"></a>Ladda ned koden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Om du vill bygga exemplet allteftersom kan du [ladda ned stommen av ett projekt som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Du kan också klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Den färdiga appen finns också [som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) eller i `complete`-grenen för samma centrallager.

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp`är en MVC-webbapp som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista.

## <a name="configure-the-task-web-app"></a>Konfigurera webbappen för att göra-listan
När en användare interagerar med `TaskWebApp` skickar klienten förfrågningar till Azure AD och hämtar tillbaka token som kan användas för att anropa `TaskService`-webb-API:et. För att logga in användaren och hämta token måste du ge `TaskWebApp` en del information om din app. I `TaskWebApp`-projektet öppnar du `web.config`-filen i roten av projektet och ersätter värdena i `<appSettings>`-avsnittet.  Du kan lämna värdena `AadInstance`, `RedirectUri` och `TaskServiceUrl` som de är.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Den här artikeln tar inte upp hur man bygger `TaskWebApp`-klienten.  Om du vill veta hur du skapar en webbapp med Azure AD B2C finns [våra webbappsjälvstudier för .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Skydda API:et
När en klient anropar API:et för användarnas räkning kan du skydda `TaskService` genom att använda OAuth 2.0-ägartoken. API:et kan acceptera och validera token med hjälp av Microsofts OWIN-bibliotek (Open Web Interface för .NET).

### <a name="install-owin"></a>Installera OWIN
Börja med att installera autentiseringsflödet för OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Ange din B2C-information
Öppna `web.config`-filen i roten för `TaskService`-projektet och ersätt värdena i avsnittet `<appSettings>`. Dessa värden används i hela API:et och OWIN-biblioteket.  Du kan lämna `AadInstance`-värdet oförändrat.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Lägga till en OWIN-startklass
Lägg till en OWIN-startklass i `TaskService`-projektet med namnet `Startup.cs`.  Högerklicka på projektet, välj **Lägg till** och **Nytt objekt** och sök sedan efter OWIN.

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Konfigurera OAuth 2.0-autentisering
Öppna filen `App_Start\Startup.Auth.cs` och implementera `ConfigureAuth(...)`-metoden:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Skydda uppgiftskontrollanten
När appen har konfigurerats att använda OAuth 2.0-autentisering kan du skydda webb-API:et genom att lägga till en `[Authorize]`-tagg i uppgiftskontrollanten. Det här är domänkontrollanten där all manipulering av att göra-listan äger rum. Därför bör du skydda hela kontrollanten på klassnivå. Du kan också lägga till `[Authorize]`-taggen för enskilda åtgärder för en mer detaljerad kontroll.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Hämta användarinformation från token
`TasksController` lagrar uppgifter i en databas där varje uppgift har en associerad användare som ”äger” uppgiften. Ägaren identifieras med hjälp av användarens **objekt-ID**. (Det är därför du måste lägga till objekt-ID:t som ett programanspråk i alla dina principer.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen
Slutligen bygger du och kör både `TaskWebApp` och `TaskService`. Registrera dig för appen med en e-postadress eller ett användarnamn. Skapa några uppgifter i användarens att göra-lista och notera hur de finns kvar i API:et även om du stoppar och startar om klienten.

## <a name="edit-your-policies"></a>Redigera dina principer
När du har skyddat ett API med hjälp av Azure AD B2C kan du experimentera med appens principer och se effekterna (eller avsaknaden av dem) på API:et. Du kan manipulera programanspråken i principerna och ändra användarinformationen som är tillgänglig i webb-API:et. Eventuella anspråk som du lägger till är tillgängliga för det .NET MVC-baserade webb-API:et i `ClaimsPrincipal`-objektet på det sätt som beskrivits tidigare i den här artikeln.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Dec16_HO4-->



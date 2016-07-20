<properties
    pageTitle="Azure AD B2C Preview | Microsoft Azure"
    description="Hur du skapar ett .NET-webb-API med Azure Active Directory B2C som skyddas med hjälp av OAuth 2.0-åtkomsttoken för autentisering."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="dastrock"/>

# Azure Active Directory B2C Preview: Skapa ett .NET-webb-API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Med Azure Active Directory (Active AD) B2C kan du skydda ett webb-API med hjälp av OAuth 2.0-åtkomsttoken. Med hjälp av dessa token kan dina klientappar som använder Azure AD B2C autentisera mot API:et. Den här artikeln beskriver hur du skapar en .NET MVC-baserad (Model-View-Controller) att göra-app som har funktioner för användarregistrering, inloggning och profilhantering. Varje användares att göra-lista lagras av en uppgiftstjänst. Det här är ett webb-API som gör att autentiserade användare kan skapa och läsa uppgifter i sina att göra-listor.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Skapa en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en B2C-katalog [skapar du en](active-directory-b2c-get-started.md) innan du fortsätter den här guiden.

## Skapa ett program

Nu måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

- Lägga till en **webbapp** eller ett **webb-API** i programmet.
- Använda **omdirigerings-URI:n** `https://localhost:44316/` för webbappen. Det här är standardplatsen för klienten för webbappen i det här kodexemplet.
- Kopiera **program-ID:t** som har tilldelats din app. Du behöver det senare.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Skapa dina principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Klienten i det här kodexemplet innehåller tre identitetsmiljöer: registrering, inloggning och profilredigering. Du måste skapa en princip för varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Tänk på följande när du skapar dina tre principer:

- Välj antingen **Registrering med användar-ID** eller **Registrering med e-postadress** i bladet för identitetsproviders.
- Välj **Visningsnamn** och andra registreringsattribut i registreringsprincipen.
- Välj **Visningsnamn** och **Objekt-ID** som programanspråk för varje princip. Du kan också välja andra anspråk.
- Kopiera **namnet** på varje princip när du har skapat dem. Du behöver principnamnen senare.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat de tre principerna är du redo att bygga din app.

## Ladda ned koden

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-devquickstarts-bug-fix.md)]

Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Om du vill bygga exemplet allteftersom kan du [ladda ned stommen av ett projekt som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Du kan också klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Den färdiga appen finns också [som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) eller i `complete`-grenen för samma centrallager.

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp` är en MVC-webbapp som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista.

## Konfigurera webbappen för att göra-listan

När en användare interagerar med `TaskWebApp` skickar klienten förfrågningar till Azure AD och hämtar tillbaka token som kan användas för att anropa `TaskService`-webb-API:et. För att logga in användaren och hämta token måste du ge `TaskWebApp` en del information om din app. I `TaskWebApp`-projektet öppnar du `web.config`-filen i roten av projektet och ersätter värdena i `<appSettings>`-avsnittet:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Det finns även två `[PolicyAuthorize]`-dekoratörer som kräver att du anger namnet på din inloggningsprincip. `[PolicyAuthorize]`-attributet används för att anropa en viss princip när en användare försöker komma åt en sida i appen som kräver autentisering.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

Information om hur en webbapp som `TaskWebApp` använder Azure AD B2C finns i [Skapa en .NET-webbapp](active-directory-b2c-devquickstarts-web-dotnet.md).

## Skydda API:et

När en klient anropar API:et för användarnas räkning kan du skydda `TaskService` genom att använda OAuth 2.0-ägartoken. API:et kan acceptera och validera token med hjälp av Microsofts OWIN-bibliotek (Open Web Interface för .NET).

### Installera OWIN
Börja med att installera autentiseringsflödet för OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Ange din B2C-information
Öppna `web.config`-filen i roten för `TaskService`-projektet och ersätt värdena i avsnittet `<appSettings>`. Dessa värden används i hela API:et och OWIN-biblioteket.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

### Lägga till en OWIN-startklass
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

### Konfigurera OAuth 2.0-autentisering
Öppna filen `App_Start\Startup.Auth.cs` och implementera `ConfigureAuth(...)`-metoden:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
    private const string discoverySuffix = ".well-known/openid-configuration";

    public void ConfigureAuth(IAppBuilder app)
    {   
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API accepts tokens only from its own clients
            ValidAudience = clientId,
        };

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {   
            // This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
        });
    }
}
```

### Skydda uppgiftskontrollanten
När appen har konfigurerats att använda OAuth 2.0-autentisering kan du skydda webb-API:et genom att lägga till en `[Authorize]`-tagg i uppgiftskontrollanten. Det här är domänkontrollanten där all manipulering av att göra-listan äger rum. Därför bör du skydda hela kontrollanten på klassnivå. Du kan också lägga till `[Authorize]`-taggen för enskilda åtgärder för en mer detaljerad kontroll.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### Hämta användarinformation från token
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

## Kör exempelappen

Slutligen bygger du och kör både `TaskWebApp` och `TaskService`. Registrera dig för appen med en e-postadress eller ett användarnamn. Skapa några uppgifter i användarens att göra-lista och notera hur de finns kvar i API:et även om du stoppar och startar om klienten.

## Redigera dina principer

När du har skyddat ett API med hjälp av Azure AD B2C kan du experimentera med appens principer och se effekterna (eller avsaknaden av dem) på API:et. Du kan <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->manipulera programanspråken i principerna och ändra användarinformationen som är tillgänglig i webb-API:et. Eventuella anspråk som du lägger till är tillgängliga för det .NET MVC-baserade webb-API:et i `ClaimsPrincipal`-objektet på det sätt som beskrivits tidigare i den här artikeln.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Jun16_HO2-->



---
title: Azure AD B2C | Microsoft Docs
description: "Skapa ett .NET-webb-API med Azure Active Directory B2C som skyddas med hjälp av OAuth 2.0-åtkomsttoken för autentisering."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 3dd207805c1a8f53c6cc74da08cc9378609581c5
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: Skapa ett .NET-webb-API

Med Azure Active Directory (Active AD) B2C kan du skydda ett webb-API med hjälp av OAuth 2.0-åtkomsttoken. Dessa token gör att dina klientappar kan autentisera mot API:et. Den här artikeln beskriver hur du skapar ett .NET MVC-baserat API för en att göra-lista som låter användare av ditt klientprogram köra CRUD-uppgifter. Webb-API:t skyddas med hjälp av Azure AD B2C och tillåter endast autentiserade användare att hantera sina att göra-listor.

## <a name="create-an-azure-ad-b2c-directory"></a>Skapa en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en B2C-katalog [skapar du en](active-directory-b2c-get-started.md) innan du fortsätter den här guiden.

> [!NOTE]
> Klientprogrammet och webb-API:et måste använda samma Azure AD B2C-katalog.
>

## <a name="create-a-web-api"></a>Skapa ett webb-API

Därefter måste du skapa en webb-API-app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

* Lägga till en **webbapp** eller ett **webb-API** i programmet.
* Använd **omdirigerings-URI:n** `https://localhost:44332/` för webbappen. Det här är standardplatsen för klienten för webbappen i det här kodexemplet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver det senare.
* Ange en appidentifierare i **App-ID-URI**.
* Lägg till behörigheter via menyn med **publicerade omfång**.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Skapa dina principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Du måste skapa en princip för att kommunicera med Azure AD B2C. Vi rekommenderar att du använder den kombinerade registrerings- och inloggningsprincipen som beskrivs i [referensartikeln för principer](active-directory-b2c-reference-policies.md). Se till att göra följande när du skapar en princip:

* Välj **Visningsnamn** och andra registreringsattribut i principen.
* Välj **Visningsnamn** och **Objekt-ID** som programanspråk för varje princip. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat den. Du behöver principnamnet senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat principen är det dags att skapa appen.

## <a name="download-the-code"></a>Ladda ned koden

Koden för den här självstudiekursen finns på [GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi). Du kan klona exemplet genom att köra:

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp`är en MVC-webbapp som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista. I den här artikeln beskrivs bara `TaskService`-programmet. Information om hur du skapar `TaskWebApp` med hjälp av Azure AD B2C finns i [självstudiekursen för .NET-webbappar](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Uppdatera Azure AD B2C-konfigurationen

Det här exemplet är konfigurerat att använda principerna och klient-ID:t för vår demoklientorganisation. Om du vill använda din egen klientorganisation måste du göra följande:

1. Öppna `web.config` i `TaskService`-projektet och ersätt värdena för
    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med program-ID:t för ditt webb-API
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip

2. Öppna `web.config` i `TaskWebApp`-projektet och ersätt värdena för
    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med program-ID:t för din webbapp
    * `ida:ClientSecret` med den hemliga nyckeln för din webbapp
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip
    * `ida:EditProfilePolicyId` med namnet på din profilredigeringsprincip
    * `ida:ResetPasswordPolicyId` med namnet på din lösenordsåterställningsprincip


## <a name="secure-the-api"></a>Skydda API:et

Om du har en klient som anropar ditt API kan du skydda API:et (t.ex `TaskService`) med hjälp av OAuth 2.0-ägartoken. Detta säkerställer att varje begäran till API:et endast är giltig om begäran har en ägartoken. API:et kan acceptera och validera ägartoken med hjälp av Microsofts OWIN-bibliotek (Open Web Interface för .NET).

### <a name="install-owin"></a>Installera OWIN

Börja med att installera OWIN OAuth-autentiseringspipelinen med hjälp av Visual Studio Package Manager-konsolen.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

När du gör det installeras OWIN-mellanprogrammet som accepterar och validerar ägartoken.

### <a name="add-an-owin-startup-class"></a>Lägga till en OWIN-startklass

Lägg till en OWIN-startklass i API:et med namnet `Startup.cs`.  Högerklicka på projektet, välj **Lägg till** och **Nytt objekt** och sök sedan efter OWIN. OWIN-mellanprogrammet anropar `Configuration(…)`-metoden när appen startas.

I vårt exempel ändrade vi klassdeklarationen till `public partial class Startup` och implementerade den andra delen av klassen i `App_Start\Startup.Auth.cs`. I `Configuration`-metoden lade vi till ett anrop till `ConfigureAuth`, som definieras i `Startup.Auth.cs`. Efter ändringarna ser `Startup.cs` ut så här:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Konfigurera OAuth 2.0-autentisering

Öppna filen `App_Start\Startup.Auth.cs` och implementera `ConfigureAuth(...)`-metoden. Det kan till exempel se ut så här:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Skydda uppgiftskontrollanten

När appen har konfigurerats att använda OAuth 2.0-autentisering kan du skydda webb-API:et genom att lägga till en `[Authorize]`-tagg i uppgiftskontrollanten. Det här är domänkontrollanten där all manipulering av att göra-listan äger rum. Därför bör du skydda hela kontrollanten på klassnivå. Du kan också lägga till `[Authorize]`-taggen för enskilda åtgärder för en mer detaljerad kontroll.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Hämta användarinformation från token

`TasksController` lagrar uppgifter i en databas där varje uppgift har en associerad användare som ”äger” uppgiften. Ägaren identifieras med hjälp av användarens **objekt-ID**. (Det är därför du måste lägga till objekt-ID:t som ett programanspråk i alla dina principer.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Validera behörigheterna i token

Ett vanligt krav för webb-API:er är att validera ”omfång” i token. Detta säkerställer att användaren har samtyckt till behörigheterna som krävs för att komma åt tjänsten för att göra-listan.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Slutligen bygger du och kör både `TaskWebApp` och `TaskService`. Skapa några uppgifter i användarens att göra-lista och notera hur de finns kvar i API:et även om du stoppar och startar om klienten.

## <a name="edit-your-policies"></a>Redigera dina principer

När du har skyddat ett API med hjälp av Azure AD B2C kan du experimentera med registrerings- och inloggningsprincipen och se effekterna (eller avsaknaden av dem) i API:et. Du kan manipulera programanspråken i principerna och ändra användarinformationen som är tillgänglig i webb-API:et. Eventuella anspråk som du lägger till är tillgängliga för det .NET MVC-baserade webb-API:et i `ClaimsPrincipal`-objektet på det sätt som beskrivits tidigare i den här artikeln.


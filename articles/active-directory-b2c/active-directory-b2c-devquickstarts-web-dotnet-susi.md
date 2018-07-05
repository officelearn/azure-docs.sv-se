---
title: Autentisering, registrera dig för återställning av lösenord i Azure Active Directory B2C | Microsoft Docs
description: Hur du skapar ett webbprogram som har registrerings-registreringen/inloggning, profilredigering och lösenordsåterställning med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 66cd3644611c1d837de717661a3515a0c0d3956f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447650"
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Skapa en ASP.NET-webbapp med Azure Active Directory B2C registrering, inloggning, profilredigering och återställning av lösenord

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Lägg till Azure AD B2C identitetsfunktioner till din webbapp
> * Registrera din webbapp i din Azure AD B2C-katalog
> * Skapa en användare registrerings-registreringen/inloggning, profilredigering och principen för återställning av lösenord för din webbapp

## <a name="prerequisites"></a>Förutsättningar

- Din B2C-klient måste du ansluta till ett Azure-konto. Du kan skapa ett kostnadsfritt konto [här](https://azure.microsoft.com/).
- Du behöver [Microsoft Visual Studio](https://www.visualstudio.com/) eller ett liknande program visa och ändra exempelkoden.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

Innan du kan använda Azure AD B2C måste du skapa en klient. En klient är en behållare för alla användare, appar, grupper och mer. Om du inte har ett redan, kan du skapa en B2C-klient innan du fortsätter i den här guiden.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Du måste ansluta Azure AD B2C-klient till din Azure-prenumeration. När du har valt **skapa**väljer den **länka en befintlig Azure AD B2C-Klientorganisation till min Azure-prenumeration** alternativet, och sedan i den **Azure AD B2C-klient** nedrullningsbar listruta, Välj den innehavaren som du vill associera.

## <a name="create-and-register-an-application"></a>Skapa och registrera ett program

Därefter måste du skapa och registrera appen i din Azure AD B2C-klient. Detta ger information som Azure AD B2C som krävs för säker kommunikation med din app. 

Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**. Du bör nu använda den klient som du skapade tidigare.

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

När du är klar har du både ett API och ett internt program i programinställningarna.

## <a name="create-policies-on-your-b2c-tenant"></a>Skapa principer på din B2C-klient

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Det här kodexemplet innehåller tre identitetsupplevelser: **registrering och inloggning i**, **profilera redigera**, och **lösenordsåterställning**.  Du måste skapa en princip av varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md). För varje princip måste du välja Visa namnattributet eller anspråk och kopiera ned namnet på din princip för senare användning.

### <a name="add-your-identity-providers"></a>Lägg till dina Identitetsproviders

Dina inställningar, Välj **Identitetsprovidrar** och välj registrering för användarnamn eller e-postregistrering.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Skapa en princip för registrering och inloggning

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

När du har skapat dina principer är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Hämta exempelkoden

Koden för den här självstudiekursen finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Du kan klona exemplet genom att köra:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp` är MVC-webbprogram som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista. I den här artikeln beskrivs bara `TaskWebApp`-programmet. Lär dig hur du skapar `TaskService` med Azure AD B2C finns i [våra .NET webb-api – självstudiekurs](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Uppdatera kod för att använda din klient och principer

Det här exemplet är konfigurerat att använda principerna och klient-ID:t för vår demoklientorganisation. Om du vill ansluta den till en egen klient, måste du öppna `web.config` i den `TaskWebApp` projektet och Ersätt följande värden:

* `ida:Tenant` med namnet på din klientorganisation
* `ida:ClientId` med program-ID:t för din webbapp
* `ida:ClientSecret` med den hemliga nyckeln för din webbapp
* `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip
* `ida:EditProfilePolicyId` med namnet på din profilredigeringsprincip
* `ida:ResetPasswordPolicyId` med namnet på din lösenordsåterställningsprincip

## <a name="launch-the-app"></a>Starta appen
Starta appen från Visual Studio. Gå till fliken att göra-lista och notera URL: en är: https://login.microsoftonline.com/ *YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id =*YourclientID*.....

Registrera dig för appen med hjälp av namnet på din e-postadress eller användarnamn. Logga ut, och sedan logga in igen och redigera profilen eller återställa lösenordet. Logga ut och logga in som en annan användare. 

## <a name="add-social-idps"></a>Lägg till sociala IDP: er

Appen stöder för närvarande bara användaren registrera dig och logga in med hjälp av **lokala konton**; konton som lagras i din B2C-katalog som använder ett användarnamn och lösenord. Med hjälp av Azure AD B2C kan du lägga till stöd för andra **identitetsprovidrar** (IDPs) utan att ändra någon av din kod.

Börja genom att följa instruktionerna i följande artiklar om du vill lägga till sociala IDP: er i din app. För varje IDP som du vill kunna använda, måste du registrera ett program i systemet och få ett klient-ID.

* [Konfigurera Facebook som en IDP](active-directory-b2c-setup-fb-app.md)
* [Konfigurera Google som en IDP](active-directory-b2c-setup-goog-app.md)
* [Konfigurera Amazon som en IDP](active-directory-b2c-setup-amzn-app.md)
* [Konfigurera LinkedIn som en IDP](active-directory-b2c-setup-li-app.md)

När du lägger till identitetsprovidrarna som din B2C-katalog kan redigera var och en av dina tre principer att inkludera de nya IDP: er, enligt beskrivningen i den [referensartikeln om principer](active-directory-b2c-reference-policies.md). När du har sparat dina principer kan du köra appen igen.  Du bör se de nya IDP: er som har lagts till som inloggning och registreringsalternativ i var och en av din identitet inträffar.

Du kan experimentera med dina principer och studera effekten på din exempelapp. Lägg till eller ta bort IDP: er, manipulera programanspråken eller ändra registreringsattribut. Experimentera tills du kan se hur principer, begäranden om autentisering och OWIN knyta ihop.

## <a name="sample-code-walkthrough"></a>Kod exempelgenomgång
I följande avsnitt visas hur programmet exempelkoden konfigureras. Du kan använda detta som en vägledning i din framtida apputveckling.

### <a name="add-authentication-support"></a>Lägg till stöd för autentisering

Du kan nu konfigurera din app om du vill använda Azure AD B2C. Appen kan kommunicera med Azure AD B2C genom att skicka autentiseringsförfrågningar OpenID Connect. Begäranden bestämmer användarupplevelsen appen vill köra genom att ange principen. Du kan använda Microsofts OWIN-bibliotek för att skicka dessa begäranden, köra principer, hantera användarsessioner och mycket mer.

#### <a name="install-owin"></a>Installera OWIN

Börja genom att lägga till OWIN-mellanprogrammet NuGet-paket i projektet med hjälp av Visual Studio Package Manager-konsolen.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Lägga till en OWIN-startklass

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

#### <a name="configure-the-authentication-middleware"></a>Konfigurera mellanprogram för autentisering

Öppna filen `App_Start\Startup.Auth.cs` och implementera den `ConfigureAuth(...)` metoden. De parametrar som du anger i `OpenIdConnectAuthenticationOptions` fungerar som koordinater för din app att kommunicera med Azure AD B2C. Om du inte anger vissa parametrar används standardvärdet. Exempelvis kan vi inte anger den `ResponseType` i det här exemplet så standardvärdet `code id_token` används i varje utgående begäran till Azure AD B2C.

Du måste också konfigurera cookie-autentisering. OpenID Connect-mellanprogram använder cookies för att underhålla användarsessioner, bland annat.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

I `OpenIdConnectAuthenticationOptions` ovan, definierar vi en uppsättning återanropsfunktionerna för specifika meddelanden som tas emot av OpenID Connect-mellanprogram. Dessa beteenden definieras med hjälp av en `OpenIdConnectAuthenticationNotifications` objekt och lagras i den `Notifications` variabeln. I vårt exempel definierar vi tre olika återanrop beroende på händelsen.

### <a name="using-different-policies"></a>Med hjälp av olika principer

Den `RedirectToIdentityProvider` avisering utlöses när en begäran skickas till Azure AD B2C. I Återanropsfunktionen `OnRedirectToIdentityProvider`, vi checka in utgående anropet om vi vill använda en annan princip. För att göra en återställning av lösenord eller redigera en profil som du behöver använda motsvarande principen som principen i stället för ”registrering eller inloggning” standardprincipen för lösenordsåterställning.

I vårt exempel när en användare vill återställa lösenordet eller redigera profilen måste vi lägga till principen vi föredrar att använda till samma kontext som OWIN. Detta kan göras genom att göra följande:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Och du kan implementera Återanropsfunktionen `OnRedirectToIdentityProvider` genom att göra följande:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Hantering av auktoriseringskoder

Den `AuthorizationCodeReceived` avisering utlöses när en auktoriseringskod tas emot. OpenID Connect-mellanprogram stöder inte utbyte koder för åtkomsttoken. Du kan manuellt byta koden för token i en återanropsfunktion. Mer information finns i [dokumentation](active-directory-b2c-devquickstarts-web-api-dotnet.md) som förklarar hur.

### <a name="handling-errors"></a>Hantera fel

Den `AuthenticationFailed` avisering utlöses när autentiseringen misslyckas. Du kan hantera fel som du vill i dess motringningsmetoden. Dock bör du lägga till en kontroll för felkoden `AADB2C90118`. Under körningen av principen ”registrering eller inloggning” användaren har möjlighet att välja en **har du glömt lösenordet?** länk. I den här händelsen skickar Azure AD B2C appen den felkod som att din app ska göra en begäran i stället använda principen för lösenordsåterställning.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Skicka autentiseringsförfrågningar till Azure AD

Din app är nu korrekt konfigurerad för att kommunicera med Azure AD B2C med hjälp av autentiseringsprotokollet OpenID Connect. OWIN hanterar autentiseringsmeddelanden, verifiera token från Azure AD B2C och upprätthålla användarsessioner. Allt som återstår är att starta flödet för varje användare.

När en användare väljer **logga in/logga in**, **profilredigering**, eller **Återställ lösenord** i web-app associerade åtgärden anropas i `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Du kan också använda OWIN logga ut användaren från appen. I `Controllers\AccountController.cs` har vi:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Förutom att explicit anropa en princip, kan du använda en `[Authorize]` tagg i dina domänkontrollanter som kör en princip om användaren inte har loggat in. Öppna `Controllers\HomeController.cs` och lägga till den `[Authorize]` tagg till kontrollanten anspråk.  OWIN väljer den senaste principen konfigureras när den `[Authorize]` tagg med samma namn.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Visa användarinformation

När du autentiserar användare med hjälp av OpenID Connect, Azure AD B2C returnerar ett ID-token till appen som innehåller **anspråk**. Det här är intyg om användaren. Du kan använda anspråk för att anpassa din app.

Öppna filen `Controllers\HomeController.cs`. Du kan komma åt användaranspråk i dina domänkontrollanter via den `ClaimsPrincipal.Current` säkerhetsobjekt.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Du kan komma åt några anspråk som programmet tar emot på samma sätt.  En lista över alla anspråk som appen tar emot finns tillgänglig för på den **anspråk** sidan.

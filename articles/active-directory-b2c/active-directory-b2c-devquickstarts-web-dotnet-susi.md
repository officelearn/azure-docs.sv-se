---
title: "Autentisering, registrering för lösenordsåterställning ASP.NET Azure Active Directory B2C"
description: "Hur du skapar ett webbprogram som har sign-upp/inloggning, Redigera profil och lösenordsåterställning med hjälp av Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: barbaraselden
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.custom: seohack1
ms.openlocfilehash: ffc46f4348a2ac3cae51c859a24c609756a710fe
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Skapa en ASP.NET-webbapp med Azure Active Directory B2C profil för registrering, inloggning, redigera och återställning av lösenord

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Lägg till Azure AD B2C identitet funktioner i ditt webbprogram
> * Registrera ditt webbprogram i din Azure AD B2C-katalog
> * Skapa en användare sign-upp/inloggning, Redigera profil och principen för lösenordsåterställning för ditt webbprogram

## <a name="prerequisites"></a>Förutsättningar

- Din B2C-klient måste du ansluta till ett Azure-konto. Du kan skapa ett kostnadsfritt Azure-konto [här](https://azure.microsoft.com/en-us/).
- Du behöver [Microsoft Visual Studio](https://www.visualstudio.com/) eller ett liknande program att visa och ändra exempelkoden.

## <a name="create-an-azure-ad-b2c-directory"></a>Skapa en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte har någon redan skapar du en B2C-katalog innan du fortsätter i den här guiden.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Du måste ansluta B2C-klient till din Azure-prenumeration. När du har valt **skapa**, Välj den **länka ett befintligt Azure AD B2C-klient till min Azure-prenumeration** alternativet, och klicka sedan på den **Azure AD B2C-klient** listrutan, Välj den klient som du vill associera.

## <a name="create-and-register-an-application"></a>Skapa och registrera ett program

Därefter måste du skapa och registrera app i B2C-katalogen. Detta ger information som behöver för att kommunicera säkert med din app i Azure AD B2C. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

När du är klar har du både en API och det ursprungliga programmet i inställningarna för programmet.

## <a name="create-policies-on-your-b2c-tenant"></a>Skapa principer på din B2C-klient

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Det här kodexemplet innehåller tre identitetsupplevelser: **registrering och inloggning i**, **profilen redigera**, och **lösenordsåterställning**.  Du måste skapa en princip av varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md). För varje princip Glöm inte att välja Visa name-attribut eller anspråk och kopiera ned namnet på din princip för senare användning.

### <a name="add-your-identity-providers"></a>Lägg till din identitetsleverantörer

Välj inställningarna för **identitetsleverantörer** och välj signup användarnamn eller e-registreringen.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Skapa en princip för registrering och inloggning

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Skapa en profil Redigera princip

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Skapa en princip för lösenordsåterställning

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

När du har skapat dina principer är du redo att bygga din app.

## <a name="download-the-sample-code"></a>Hämta exempelkoden

Koden för den här självstudiekursen finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Du kan klona exemplet genom att köra:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp`är MVC-webbapp som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista. I den här artikeln beskrivs bara `TaskWebApp`-programmet. Mer information om hur du skapar `TaskService` med Azure AD B2C finns i [våra självstudier för .NET web api](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Uppdatera koden för att använda din klient och principer

Det här exemplet är konfigurerat att använda principerna och klient-ID:t för vår demoklientorganisation. För att ansluta den till en egen klient, måste du öppna `web.config` i den `TaskWebApp` projektet och Ersätt följande värden:

* `ida:Tenant` med namnet på din klientorganisation
* `ida:ClientId` med program-ID:t för din webbapp
* `ida:ClientSecret` med den hemliga nyckeln för din webbapp
* `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip
* `ida:EditProfilePolicyId` med namnet på din profilredigeringsprincip
* `ida:ResetPasswordPolicyId` med namnet på din lösenordsåterställningsprincip

## <a name="launch-the-app"></a>Starta appen
Starta appen från Visual Studio. Gå till fliken Uppgiftslista och skriver ner webbadressen: https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id =*YourclientID*...

Registrera dig för appen genom att använda din e-postadress eller användaren namn. Logga ut och sedan logga in igen och redigera profilen eller återställa lösenordet. Logga ut och logga in som en annan användare. 

## <a name="add-social-idps"></a>Lägg till sociala IDPs

För närvarande appen stöder bara användaren registrering och inloggning med hjälp av **lokala konton**; konton som lagras i din B2C-katalog som använder ett användarnamn och lösenord. Med hjälp av Azure AD B2C kan du lägga till stöd för andra **identitetsleverantörer** (IDPs) utan att ändra någon av din kod.

Börja genom att följa de detaljerade anvisningarna i de här artiklarna om du vill lägga till sociala IDPs i appen. För varje IDP som du vill använda, måste du registrera ett program i systemet och skaffa ett klient-ID.

* [Konfigurera Facebook som en IDP](active-directory-b2c-setup-fb-app.md)
* [Konfigurera Google som en IDP](active-directory-b2c-setup-goog-app.md)
* [Ställa in Amazon som en IDP](active-directory-b2c-setup-amzn-app.md)
* [Ställa in LinkedIn som en IDP](active-directory-b2c-setup-li-app.md)

När du lägger till identitetsleverantörer din B2C-katalog, redigera var och en av dina tre principer att inkludera de nya IDPs som beskrivs i den [referensartikeln om principer](active-directory-b2c-reference-policies.md). Kör appen igen när du har sparat dina principer.  Du bör se nya IDPs läggas till som inloggning och registreringsalternativ i varje din identitet inträffar.

Du kan experimentera med dina principer och studera effekten på din exempelapp. Lägg till eller ta bort IDPs, manipulera programanspråken eller ändra registreringsattribut. Experimentera tills du kan se hur principer, autentiseringsbegäranden och OWIN knyta ihop.

## <a name="sample-code-walkthrough"></a>Exempel kod genomgång
Följande avsnitt visar hur exempelkod för programmet är konfigurerat. Du kan använda den som en vägledning i framtida app-utveckling.

### <a name="add-authentication-support"></a>Lägga till stöd för autentisering

Du kan nu konfigurera din app att använda Azure AD B2C. Appen kommunicerar med Azure AD B2C genom att skicka OpenID Connect autentiseringsbegäranden. Begäranden styr användarupplevelsen appen vill köra genom att ange principen. Du kan använda Microsofts OWIN-biblioteket för att skicka dessa begäranden, köra principer, hantera användarsessioner och mycket mer.

#### <a name="install-owin"></a>Installera OWIN

Om du vill börja lägga till OWIN mellanprogram NuGet-paket i projektet med hjälp av Visual Studio Package Manager-konsolen.

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

Öppna filen `App_Start\Startup.Auth.cs` och genomföra den `ConfigureAuth(...)` metoden. De parametrar som du anger i `OpenIdConnectAuthenticationOptions` fungerar som koordinater för din app för att kommunicera med Azure AD B2C. Om du inte anger vissa parametrar används standardvärdet. Till exempel vi inte anger den `ResponseType` i det här exemplet därför standardvärdet `code id_token` kommer att användas i varje utgående begäran till Azure AD B2C.

Du måste också konfigurera cookie-autentisering. Mellanprogram OpenID Connect använder cookies för att upprätthålla användarsessioner, bland annat.

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
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

I `OpenIdConnectAuthenticationOptions` ovan, vi definierar en uppsättning Återanropsfunktioner för specifika meddelanden som tas emot av mellanprogram OpenID Connect. Dessa beteenden definieras med hjälp av en `OpenIdConnectAuthenticationNotifications` objekt och lagras i den `Notifications` variabeln. I vårt exempel definierar vi tre olika återanrop beroende på händelsen.

### <a name="using-different-policies"></a>Med hjälp av olika principer

Den `RedirectToIdentityProvider` avisering utlöses när en begäran skickas till Azure AD B2C. I Återanropsfunktionen `OnRedirectToIdentityProvider`, vi kontrollerar i utgående anropet om vi vill använda en annan princip. För att göra en återställning av lösenord eller redigera en profil som du behöver använda motsvarande princip som principen i stället för ”registrering eller inloggning” standardprincipen för återställning av lösenord.

I vårt exempel, när en användare vill återställa lösenordet eller Redigera profil, vi lägga till principen som vi vill använda i kontexten OWIN. Som kan göras genom att göra följande:

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

### <a name="handling-authorization-codes"></a>Hantera auktoriseringskoder

Den `AuthorizationCodeReceived` avisering utlöses när en Auktoriseringskoden tas emot. Mellanprogram OpenID Connect stöder inte utbyte koder för åtkomst-token. Du kan manuellt utväxla koden för token i en callback-funktion. Mer information finns i den [dokumentationen](active-directory-b2c-devquickstarts-web-api-dotnet.md) som förklarar hur.

### <a name="handling-errors"></a>Felhantering

Den `AuthenticationFailed` avisering utlöses när autentiseringen misslyckas. Du kan hantera fel som du vill i dess Återanropsmetoden. Dock bör du lägga till en kontroll för felkoden `AADB2C90118`. Under körningen av principen ”registrering eller inloggning” användaren har möjlighet att välja en **har du glömt lösenordet?** länk. I den här händelsen skickar Azure AD B2C appen den felkod som att din app ska göra en förfrågan med en princip för lösenordsåterställning i stället.

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

### <a name="send-authentication-requests-to-azure-ad"></a>Skicka autentiseringsbegäranden till Azure AD

Appen har nu konfigurerats korrekt för att kommunicera med Azure AD B2C genom att använda autentiseringsprotokollet OpenID Connect. OWIN hanterar information om utforma autentiseringsmeddelanden, verifiera token från Azure AD B2C och upprätthålla användarsessioner. Allt som återstår är att initiera flödet för varje användare.

När en användare väljer **logga upp/inloggning**, **Redigera profil**, eller **Återställ lösenord** i webbapp associerade åtgärden anropas i `Controllers\AccountController.cs`:

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

Förutom explicit anropar en princip, kan du använda en `[Authorize]` tagg i dina domänkontrollanter som kör en princip om användaren inte är inloggad. Öppna `Controllers\HomeController.cs` och lägga till den `[Authorize]` taggen till anspråk-styrenhet.  OWIN väljer den senaste principen konfigureras när den `[Authorize]` taggen med samma namn.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Visa användarinformation

När du autentiserar användare med OpenID Connect Azure AD B2C returnerar en ID-token till appen som innehåller **anspråk**. Dessa är intyg om användaren. Du kan använda anspråk för att anpassa din app.

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

Du kan komma åt några anspråk som programmet tar emot på samma sätt.  En lista över alla appen tar emot anspråk som är tillgänglig för dig på den **anspråk** sidan.

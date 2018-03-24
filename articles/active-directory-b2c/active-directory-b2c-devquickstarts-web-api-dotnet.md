---
title: Anropa en säker webb-api ASP.NET Azure Active Directory B2C | Microsoft Docs
description: 'Hur du skapar ett .NET-webbprogram och anropa ett webb-API: et med Azure Active Directory B2C och OAuth 2.0-åtkomsttoken.'
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 75ae88898d3d6b275ec462e865347056305fd666
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: Anropa .NET-webb-API från en .NET-webbapp

Med hjälp av Azure AD B2C kan du lägga till kraftfulla Identitetshantering till ditt webbprogram och webb-API: er. Den här artikeln beskriver hur du begär åtkomst-token och se anrop från en .NET ”uppgiftslistan” webbapp till en .NET webb-api.

Den här artikeln beskriver inte hur du implementerar inloggning, registrering och profilhantering med Azure AD B2C. Den fokuserar på anropande webb-API: er när användaren redan autentiserats. Om du inte redan gjort bör du:

* Kom igång med en [.NET-webbapp](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Kom igång med en [.NET webb-api](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Krav

Att skapa en webbapp som anropar ett webb-api, som du behöver:

1. [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).
2. [Registrera ett web api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Registrera en webbapp](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Konfigurera principer](active-directory-b2c-reference-policies.md).
5. [Bevilja webben appen behörighet att använda Internet api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Klientprogrammet och webb-API:et måste använda samma Azure AD B2C-katalog.
>

## <a name="download-the-code"></a>Ladda ned koden

Koden för den här självstudiekursen finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Du kan klona exemplet genom att köra:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp` är en MVC-webbapp som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista. Den här artikeln täcker inte skapa den `TaskWebApp` webbprogram eller `TaskService` webb-api. Information om hur du skapar .NET-webbapp med Azure AD B2C finns våra [.NET-självstudiekurs om web app](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Information om hur du skapar .NET-webb-API som kan skyddas med Azure AD B2C finns våra [.NET-självstudiekurs om webb-API](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Uppdatera Azure AD B2C-konfigurationen

Det här exemplet är konfigurerat att använda principerna och klient-ID:t för vår demoklientorganisation. Om du vill använda en egen klient:

1. Öppna `web.config` i `TaskService`-projektet och ersätt värdena för

    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med web api program-ID
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip

2. Öppna `web.config` i `TaskWebApp`-projektet och ersätt värdena för

    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med program-ID:t för din webbapp
    * `ida:ClientSecret` med den hemliga nyckeln för din webbapp
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip
    * `ida:EditProfilePolicyId` med namnet på din profilredigeringsprincip
    * `ida:ResetPasswordPolicyId` med namnet på din lösenordsåterställningsprincip



## <a name="requesting-and-saving-an-access-token"></a>Begär och spara en åtkomst-token

### <a name="specify-the-permissions"></a>Ange behörigheter

För att göra anropet till webb-API du behöver att autentisera användaren (med din sign-upp/inloggningsprincip) och [få en åtkomsttoken](active-directory-b2c-access-tokens.md) från Azure AD B2C. För att få en åtkomsttoken som måste först du ange de behörigheter som du vill bevilja åtkomst-token. Behörigheterna som har angetts i den `scope` parameter när du gör en begäran om att den `/authorize` slutpunkt. Till exempel för att få en åtkomst-token med behörigheten ”läsa” till resursprogram som har URI: N för App-ID för `https://contoso.onmicrosoft.com/tasks`, omfattningen skulle vara `https://contoso.onmicrosoft.com/tasks/read`.

Öppna filen för att ange omfånget i vårt exempel, `App_Start\Startup.Auth.cs` och definiera de `Scope` variabeln i OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Exchange auktoriseringskod för en åtkomst-token

När en användare har slutförts registrering eller inloggning erfarenhet får din app en Auktoriseringskoden från Azure AD B2C. Mellanprogram OWIN OpenID Connect lagrar koden, men kommer inte exchange för en åtkomst-token. Du kan använda den [MSAL biblioteket](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) att exchange. I vårt exempel vi har konfigurerat ett återanrop i OpenID Connect mellanprogram när en Auktoriseringskoden tas emot. I återanropet använder vi MSAL för exchange-koden för en token och spara token i cacheminnet.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Anropa webb-API

Det här avsnittet beskrivs hur du använder den token som togs emot under sign-upp/inloggning med Azure AD B2C för att komma åt webb-API.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Hämta det sparade token i domänkontrollanter

Den `TasksController` ansvarar för att kommunicera med webb-API och för HTTP-begäranden skickas till API för att läsa, skapa och ta bort uppgifter. Eftersom API skyddas av Azure AD B2C måste du först hämta token som du sparade i steget ovan.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Läsa uppgifter från webb-API

När du har en token kan du koppla den till HTTP `GET` begäran i den `Authorization` sidhuvudet på ett säkert sätt anropa `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Skapa och ta bort uppgifter på webb-API

Följer samma mönster när du skickar `POST` och `DELETE` förfrågningar till webb-API, använda MSAL för att hämta den åtkomst-token från cacheminnet.

## <a name="run-the-sample-app"></a>Kör exempelappen

Slutligen skapar och kör både appar. Logga och logga in och skapa aktiviteter för den inloggade användaren. Logga ut och logga in som en annan användare. Skapa aktiviteter för användaren. Lägg märke till hur uppgifterna lagras per användare i API: et eftersom det extraherar användarens identitet från token tas emot. Prova också spelar med scope. Ta bort behörighet att ”write” och försök sedan att lägga till en aktivitet. Kontrollera att logga ut varje gång du ändrar omfånget.


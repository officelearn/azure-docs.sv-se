---
title: Anropa ett .NET webb-API från en .NET-webbapp i Azure Active Directory B2C | Microsoft Docs
description: Hur du skapar en webbapp med .NET och anropa ett webb-api med Azure Active Directory B2C och OAuth 2.0-åtkomsttoken.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7296954a17b21183eb8be2744b42289522cf7f57
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012504"
---
# <a name="call-a-net-web-api-from-a-net-web-app-in-azure-active-directory-b2c"></a>Anropa ett .NET webb-API från en .NET-webbapp i Azure Active Directory B2C

Med hjälp av Azure AD B2C kan du kan lägga till kraftfulla identitetsfunktioner för hantering av dina webbappar och webb-API: er. Den här artikeln beskrivs hur du begär åtkomsttoken och kontrollera anrop från en webbapp med .NET ”att göra-lista till en .NET webb-api.

Den här artikeln beskriver inte hur du implementerar inloggning, registrering och profilhantering med Azure AD B2C. Den fokuserar på anropande webb-API: er när användaren redan har autentiserats. Om du inte redan gjort bör du:

* Kom igång med en [.NET-webbapp](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Kom igång med en [.NET webb-api](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Krav

Skapa ett webbprogram som anropar ett webb-API: et, måste du:

1. [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).
2. [Registrera ett webb-API: et](active-directory-b2c-app-registration.md).
3. [Registrera ett webbprogram](active-directory-b2c-app-registration.md).
4. [Konfigurera principer för](active-directory-b2c-reference-policies.md).
5. [Tilldela webben behörighet att använda webb-API: et](active-directory-b2c-access-tokens.md).

> [!IMPORTANT]
> Klientprogrammet och webb-API:et måste använda samma Azure AD B2C-katalog.
>

## <a name="download-the-code"></a>Ladda ned koden

Koden för den här självstudiekursen finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Du kan klona exemplet genom att köra:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Lösningsfilen innehåller två projekt: `TaskWebApp` och `TaskService`. `TaskWebApp` är ett MVC-webbprogram som användaren interagerar med. `TaskService` är appens backend-webb-API som lagrar varje användares att göra-lista. Den här artikeln beskriver inte att skapa den `TaskWebApp` webbapp eller `TaskService` webb-api. Information om hur du skapar .NET-webbapp med hjälp av Azure AD B2C finns i vår [webbappsjälvstudier](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Information om hur du skapar .NET-webb-API som skyddas med hjälp av Azure AD B2C finns i vår [.NET webb-API-självstudiekurs](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Uppdatera Azure AD B2C-konfigurationen

Det här exemplet är konfigurerat att använda principerna och klient-ID:t för vår demoklientorganisation. Om du vill använda din egen klientorganisation:

1. Öppna `web.config` i `TaskService`-projektet och ersätt värdena för

    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med webb-API: et program-ID
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip

2. Öppna `web.config` i `TaskWebApp`-projektet och ersätt värdena för

    * `ida:Tenant` med namnet på din klientorganisation
    * `ida:ClientId` med program-ID:t för din webbapp
    * `ida:ClientSecret` med den hemliga nyckeln för din webbapp
    * `ida:SignUpSignInPolicyId` med namnet på din registrerings- eller inloggningsprincip
    * `ida:EditProfilePolicyId` med namnet på din profilredigeringsprincip
    * `ida:ResetPasswordPolicyId` med namnet på din lösenordsåterställningsprincip



## <a name="requesting-and-saving-an-access-token"></a>Begär och spara en åtkomsttoken

### <a name="specify-the-permissions"></a>Ange behörigheter

För att göra anrop till webb-API, måste du autentisera användaren (med din registrerings-registreringen/inloggningsprincip) och [få en åtkomsttoken](active-directory-b2c-access-tokens.md) från Azure AD B2C. För att få en åtkomsttoken måste först du ange de behörigheter som du vill att bevilja åtkomst-token. Behörigheterna som anges i den `scope` parameter när du gör begäran till den `/authorize` slutpunkt. Till exempel för att hämta en åtkomsttoken med behörigheten ”läsa” till resursprogrammet med URI: N för App-ID för `https://contoso.onmicrosoft.com/tasks`, omfattningen skulle vara `https://contoso.onmicrosoft.com/tasks/read`.

Öppna filen för att ange omfånget i vårt exempel, `App_Start\Startup.Auth.cs` och definiera den `Scope` variabel i OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Byta auktoriseringskod för en åtkomsttoken

När en användare har slutförts registrering eller inloggning erfarenhet, får appen en auktoriseringskod från Azure AD B2C. Mellanprogram OWIN OpenID Connect lagrar koden, men kommer inte att byta den för en åtkomsttoken. Du kan använda den [MSAL biblioteket](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) att exchange. I vårt exempel vi har konfigurerat ett återanrop för frågeavisering till OpenID Connect-mellanprogram när en auktoriseringskod tas emot. I återanropet använder vi MSAL för exchange-koden för en token och spara token i cacheminnet.

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

Det här avsnittet beskrivs hur du använder den token som togs emot under registrerings-registreringen/logga in med Azure AD B2C för att komma åt webb-API.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Hämta sparad token i styrenheterna

Den `TasksController` ansvarar för att kommunicera med webb-API och för att skicka HTTP-begäranden till API för att läsa, skapa och ta bort uppgifter. Eftersom API: et skyddas av Azure AD B2C, måste du först hämta en token som du sparade i ovanstående steg.

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

När du har en token kan du koppla den till HTTP `GET` begär i den `Authorization` sidhuvudet på ett säkert sätt anropa `TaskService`:

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

Följer samma mönster när du skickar `POST` och `DELETE` begäranden till webb-API, med MSAL för att hämta åtkomsttoken från cachen.

## <a name="run-the-sample-app"></a>Kör exempelappen

Slutligen skapar och kör båda apparna. Registrera dig och logga in och skapa aktiviteter för den inloggade användaren. Logga ut och logga in som en annan användare. Skapa aktiviteter för den användaren. Observera hur uppgifter som är lagrade per användare i API: et eftersom extraherar användarens identitet från den token som tas emot. Prova också spela med säkerhetsomfattningarna. Ta bort behörigheten ”Skriva” och sedan försöka lägga till en uppgift. Se bara till att logga ut varje gång du ändrar omfånget.


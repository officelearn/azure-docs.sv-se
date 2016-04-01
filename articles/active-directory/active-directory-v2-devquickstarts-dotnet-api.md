<properties
    pageTitle="App-Modell v2.0 .Net Web-API | Microsoft Azure"
    description="Vorgehensweise beim Erstellen einer .NET-MVC-Web-API, die sowohl Token von persönlichen Microsoft-Konten als auch Geschäfts- oder Schulkonten akzeptiert."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Schützen einer MVC-Web-API

Mit dem v2. 0-app-Modell können Sie einer Web-API mit Protecet [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) Zugriffstoken, dass Benutzer mit den beiden persönlichen Microsoft-Konto und Geschäfts- oder schulkonto Konten zur sicheren Zugriff auf Ihre Web-API.

> [AZURE.NOTE]
Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

Für ASP.NET-Web-APIs erreichen Sie das Gleiche durch die in .NET Framework 4.5 enthaltene OWIN-Middleware von Microsoft.  Hier verwenden wir OWIN zum Erstellen einer "Aufgabenliste" MVC-Web-API, die:
- Ermöglicht Clients erstellen und Lesen von Aufgaben in der Aufgabenliste des Benutzers.
-   Legt fest, welche APIs geschützt sind.
-   Überprüft, dass die Web-API-Aufrufe ein gültiges Zugriffstoken enthalten.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer App in Azure AD
2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline
3. Konfigurieren einer Client-App für den Aufruf der Web-API „To Do List“

Der Code für dieses Lernprogramm [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Um folgen zu können, können Sie [Anwendungsgerüst als einer ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) oder das Skelett Klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

Die fertige App wird außerdem am Ende dieses Lernprogramms bereitgestellt.


## 1. Registrieren einer App
Erstellen einer neuen app auf [apps.dev.microsoft.com](https://apps.dev.microsoft.com), oder führen Sie die folgenden [Ausführliche Schritte](active-directory-v2-app-registration.md).  Stellen Sie sicher, dass Sie:

- Kopieren Sie die **Id der Anwendung** Ihrer app zugewiesen, Sie benötigen diese bald.

Diese Visual Studio-Lösung enthält auch "TodoListClient", eine einfache WPF-App.  Anhand von TodoListClient wird veranschaulicht, wie ein Benutzer sich anmeldet und wie ein Client Anforderungen an Ihre Web-API senden kann.  In diesem Fall sind TodoListClient und TodoListService in einer App kombiniert.  Zum Konfigurieren des TodoListClient sollten Sie außerdem folgende Schritte ausführen:

- Hinzufügen der **Mobile** Plattform für Ihre app.
- Kopieren Sie die **Redirect URI** aus dem Portal. Sie müssen den Standardwert `urn:ietf:wg:oauth:2.0:oob` verwenden.


## 2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline

Nachdem Sie eine App registriert haben, müssen Sie die App für die Kommunikation mit dem v2.0-Endpunkt konfigurieren, damit eingehende Anforderungen und Token überprüft werden können.

-   Öffnen Sie dazu zunächst die Projektmappe, und fügen Sie dem Projekt „TodoListService“ über die Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Fügen Sie dem Projekt „TodoListService“ eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu.  Rechtsklick auf das Projekt--> **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach "OWIN".  Die OWIN-Middleware ruft beim Starten Ihrer Anwendung die Methode `Configuration(…)` auf.
-   Ändern Sie die Klassendeklaration in `public partial class Startup` – einen Teil dieser Klasse haben wir bereits für Sie in einer anderen Datei implementiert.  Rufen Sie in der Methode `Configuration(…)` „ConfigureAuth(...)“ auf, um die Authentifizierung für Ihre Webanwendung einzurichten.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Öffnen Sie die Datei `App_Start\Startup.Auth.cs` und implementieren Sie die `ConfigureAuth(…)`-Methode, die die Web-API so einrichtet, dass Token vom v2.0-Endpunkt akzeptiert werden.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

-   Nun können Sie Ihre Controller und Aktionen mit `[Authorize]`-Attributen durch eine OAuth 2.0-Bearer-Authentifizierung schützen.  Geben Sie für die Klasse `Controllers\TodoListController.cs` ein Autorisierungstag an.  Dadurch wird der Benutzer gezwungen, sich vor dem Zugriff auf diese Seite anzumelden.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Wenn ein autorisierter Aufrufer eine der `TodoListController`-APIs erfolgreich aufruft, muss die Aktion möglicherweise auf Informationen über den Aufrufer zugreifen können.  OWIN stellt über das Objekt `ClaimsPrincpal` Zugriff auf die Ansprüche innerhalb des Bearer-Tokens bereit.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Öffnen Sie abschließend die Datei `web.config` aus dem Stammverzeichnis des Projekts „TodoListService“, und geben Sie Ihre Konfigurationswerte im Abschnitt `<appSettings>` ein.
  - Die `ida:Audience` ist die **Id der Anwendung** der app, die Sie im Portal eingegeben haben.

## 3. Konfigurieren einer Client-App und Ausführen des Dienstes
Um den Dienst „TodoListService“ in Aktion sehen zu können, müssen Sie den TodoList-Client so konfigurieren, dass er Token vom v2.0-Endpunkt erhält und Aufrufe an den Dienst stellen kann.

- Öffnen Sie im Projekt "TodoListClient" die Datei `App.config`, und geben Sie Ihre Konfigurationswerte im Abschnitt `<appSettings>` ein.
  - `ida:ClientId` ist die App-ID, die Sie aus dem Portal kopiert haben.
    - Die `ida:RedirectUri` ist die **Redirect Uri** aus dem Portal.

Bereinigen, erstellen und führen Sie zum Schluss alle Projekte aus!  Sie verfügen nun über eine .NET-MVC-Web-App, die sowohl Token von persönlichen Microsoft-Konten als auch Geschäfts- oder Schulkonten akzeptiert.  Melden Sie sich bei „TodoListClient“ an, und rufen Sie Ihre Web-API ab, um Aufgaben zur Aufgabenliste des Benutzers hinzuzufügen.

Als Referenz das vollständige Beispiel (ohne Ihre Konfigurationswerte) [als einer ZIP-Datei hier bereitgestellte](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), oder Sie können von GitHub Klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Nächste Schritte
Sie können sich nun weiteren Themen zuwenden.  Sie können beispielsweise Folgendes testen:

[Aufrufen einer Web-API über eine Web-App mit dem app-Modell v2. 0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Weitere Ressourcen:
- [Die App-Modell v2. 0-Vorschau >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "Azure-Active-Directory" Tag >>](http://stackoverflow.com/questions/tagged/azure-active-directory)



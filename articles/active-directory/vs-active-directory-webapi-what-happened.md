<properties
    pageTitle="Wo befindet sich meine WebApi-Projekt (Visual Studio Azure Active Directory verbunden Service) | Microsoft Azure "
    description="Beschreibt, was dem MVC-Projekt WebApi Sie mithilfe von Visual Studio-Dienste mit Azure AD verbinden ="active-directory"
    services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor="tglee"/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/03/2015"
    ms.author="tarcher"/>

# Was ist mit dem WebApi-Projekt passiert (verbundene Visual Studio-Dienste für Azure Active Directory)?

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-active-directory-webapi-getting-started.md)
> - [Was ist passiert?](vs-active-directory-webapi-what-happened.md)

##Verweise wurden hinzugefügt

###NuGet-Paketverweise

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###.NET-Verweise

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Änderungen am Code

###Ihrem Projekt wurden Codedateien hinzugefügt

Eine authentifizierungsstartklasse **App_Start/Startup.Auth.cs** wurde hinzugefügt, um das Projekt enthält Startlogik für Azure AD-Authentifizierung.

###Ihrem Projekt wurde Startcode hinzugefügt

Wenn Sie bereits eine Startklasse in Ihrem Projekt die **Konfiguration** Methode wurde aktualisiert, um einen Aufruf von `ConfigureAuth(app)`. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt.


###Die Datei "app.config" oder "web.config" weist neue Konfigurationswerte auf.

Die folgenden Konfigurationseinträge wurden hinzugefügt.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###Eine Azure AD-App wurde erstellt

Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

##Wenn ich habe *Deaktivieren der Authentifizierung einzelner Benutzerkonten*, welche zusätzlichen Änderungen wurden an meinem Projekt vorgenommen?
NuGet-Paketverweise wurden entfernt, und die Dateien wurden entfernt und gesichert. Abhängig vom Status des Projekts müssen Sie möglicherweise manuell zusätzliche Verweise oder Dateien entfernen oder Code entsprechend ändern.

###NuGet-Paketverweise entfernt (die vorhanden waren)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Codedateien gesichert und entfernt (die vorhanden waren)

Jede der folgenden Dateien wurde gesichert und aus dem Projekt entfernt. Sicherungsdateien befinden sich in einem Ordner "Backup" im Stammverzeichnis des Projektverzeichnisses.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Codedateien gesichert (die vorhanden waren)

Jede der folgenden Dateien wurde gesichert, bevor sie ersetzt wurde. Sicherungsdateien befinden sich in einem Ordner "Backup" im Stammverzeichnis des Projektverzeichnisses.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Wenn ich habe *Lesen der Verzeichnisdaten*, welche zusätzlichen Änderungen wurden an meinem Projekt vorgenommen?

###An "app.config" oder "web.config" wurden zusätzliche Änderungen vorgenommen

Die folgenden zusätzlichen Konfigurationseinträge wurden hinzugefügt.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###Ihre Azure Active Directory-App (AD) wurde aktualisiert
Ihre Azure Active Directory-App wurde aktualisiert, um enthalten die *Lesen der Verzeichnisdaten* Berechtigung und ein zusätzlicher Schlüssel wurde erstellt, die dann als verwendet wurde die *Ida: Password* in der `web.config` Datei.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)



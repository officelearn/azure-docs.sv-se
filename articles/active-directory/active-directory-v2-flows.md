<properties
    pageTitle="App-Modell v2.0: App-Typen | Microsoft Azure"
    description="Die App- und Szenariotypen, die vom Azure AD-App-Modell v2.0 (öffentliche Vorschaufunktion) unterstützt werden."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): App-Typen
V2. 0-app-Modell unterstützt die Authentifizierung für eine Vielzahl von modernen app-Architekturen, die basieren auf dem branchenüblichen Protokollen [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) und/oder [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Dieses Dokument beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können.  Es zeigt Ihnen die allgemeine Szenarien vor [direkt im Code](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Die Grundlagen
Jede app, das v2. 0-app verwendet, bei registriert werden müssen [apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendung Id** eindeutig identifiziert, die die app
- Ein **Umleitungs-URI** weiterleiten, Antworten zurück an Ihre app verwendet werden kann
- Einige andere szenariospezifische Werte.  Weitere Einzelheiten erfahren Sie, wie Sie [Registrieren einer Anwendung](active-directory-v2-app-registration.md).

Sobald die App registriert ist, kommuniziert sie mit Azure AD, indem sie Anforderungen an den v2.0-Endpunkt sendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Jede Interaktion der App mit dem v2.0-Endpunkt folgt einem ähnlichen Muster auf oberer Ebene:

1. Die App leitet den Endbenutzer zur Anmeldung an den v2.0-Endpunkt.
2. Der Benutzer authentifiziert sich, indem er beispielsweise Benutzernamen und Kennwort eingibt. 
3. Der Benutzer autorisiert die App, an seiner Stelle zu agieren, indem er der App die angeforderten Berechtigungen erteilt.
4. Die Anwendung erhält ein bestimmtes Sicherheitstoken vom v2.0-Endpunkt.
5. Die App verwendet das Sicherheitstoken für den Zugriff auf vertrauliche Informationen oder eine Ressource.
6. Der Ressourcenserver überprüft das Sicherheitstoken, um sicherzustellen, dass der Zugriff gewährt werden kann.
7. Die Anwendung aktualisiert das Sicherheitstoken in regelmäßigen Abständen.

<!-- TODO: Need a page for libraries to link to -->
Jeder dieser sieben Schritte unterscheidet sich je nach Typ der erstellten Anwendung geringfügig. In unseren Open Source-Bibliotheken werden die Einzelheiten abgedeckt.  Finden Sie weitere Informationen [Berechtigungen, die Zustimmung und Bereiche](active-directory-v2-scopes.md), oder Lesen Sie auf einige konkrete Beispiele zum durcharbeiten.

## Web-Apps
Für Web-apps (.NET, PHP, Java, Ruby, Python, Knoten usw.), die über einen Browser zugegriffen werden, v2. 0-app-Modell unterstützt Benutzer anmelden mit [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  In OpenID Connect empfängt die Web-App ein `id_token`, ein Sicherheitstoken, das die Identität des Benutzers überprüft und Informationen über den Benutzer in Form von Ansprüchen enthält:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Erfahren Sie über alle Arten von Token und Ansprüche verfügbar für eine app in den [v2. 0-Tokenverweis](active-directory-v2-tokens.md).

In Webserver-Apps werden beim Authentifizierungsablauf für die Anmeldung folgende Schritte auf hoher Ebenen durchgeführt:

![Abbildung der Web-App-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Die Überprüfung des ID-Tokens mit einem öffentlichen Signaturschlüssel, der vom v2.0-Endpunkt empfangen wird, ist ausreichend, um die Benutzeridentität zu validieren und ein Sitzungscookie festzulegen, das zur Identifizierung des Benutzers bei nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, versuchen, Sie eine Web-app-in Codebeispielen in unserer [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) Abschnitt.

Neben der einfachen Anmeldung benötigt eine Webserver-App möglicherweise auch den Zugriff auf einige andere Webdienste wie z. B. eine REST-API.  Datenfluss in diesem Fall die Web-Server-app in einem kombinierten OpenID Connect und OAuth 2.0 erfassen kann, indem Sie die [OAuth 2.0-Autorisierungscode Fluss](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Dieses Szenario wird im folgenden behandelt die [Web-APIs Abschnitt](#web-apis), und klicken Sie in unserer [Thema WebApp-WebAPI-erste Schritte](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## Web-APIs
Sie können das App-Modell v2.0 auch zum Absichern von Webdiensten verwenden, wie z. B. die RESTful-Web-API Ihrer App.  Anstelle von ID-Token und Sitzungscookies verwenden Web-APIs OAuth 2.0-Zugriffstoken zum Sichern ihrer Daten und zum Authentifizieren eingehender Anforderungen.  Der Aufrufer einer Web-API fügt ein Zugriffstoken in den Autorisierungs-Header einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API kann dann das Zugriffstoken zum Überprüfen der Identität des API-Aufrufers verwenden, und Informationen über den Aufrufer aus Ansprüchen extrahieren, die in den Zugriffstoken codiert sind.  Erfahren Sie über alle Arten von Token und Ansprüche verfügbar für eine app in den [v2. 0-Tokenverweis](active-directory-v2-tokens.md).

Eine Web-API können Benutzern die Möglichkeit, opt-in/opt-Out bestimmter Funktionen oder Daten durch Verfügbarmachen von Berechtigungen, auch bekannt als [Bereiche](active-directory-v2-scopes.md).  Damit eine aufrufende App Berechtigungen für einen Bereich erhält, muss der Benutzer für den Bereich während eines Ablaufs seine Zustimmung erteilen.  Der v2.0-Endpunkt kümmert sich darum, den Benutzer um Erlaubnis zu fragen, und zeichnet die Berechtigungen in allen Zugriffstoken auf, die die Web-API empfängt.  Die Web-API muss nur noch die erhaltenen Zugriffstoken überprüfen, die sie bei jedem Aufruf erhält, und die entsprechenden Autorisierungsprüfungen durchführen.

Eine Web-API kann Zugriffstoken von allen Apptypen empfangen, einschließlich Webserver-Apps, Desktop-Apps, mobilen Apps, Single Page-Apps, serverseitigen Daemons und sogar anderen Web-APIs.  Als Beispiel sehen wir uns den vollständigen Vorgang für eine Webserver-App an, die eine Web-API aufruft.

![Abbildung der Web-App-Web-API-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Weitere Informationen zu Authorization_codes, Refresh_tokens und die einzelnen Schritte der erste Access_tokens erfahren Sie mehr über die [OAuth 2.0-Protokoll](active-directory-v2-protocols-oauth-code.md).

Informationen zum Sichern einer Web-API mit dem app-Modell v2. 0 und OAuth 2.0-Access_tokens, sehen Sie sich die Web-API-Codebeispiele in unserer [im Abschnitt Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started).


## Mobile und systemeigene Apps
Auf einem Gerät installierte Apps wie mobile und Desktop-Apps benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs, die Daten speichern und verschiedene Funktionen im Auftrag eines Benutzers ausführen.  Diese apps können auf Back-End-Dienste, die mit dem Modell v2. 0-Anmeldung und Autorisierung hinzufügen und die [OAuth 2.0-Autorisierungscode Fluss](active-directory-v2-protocols-oauth-code.md).  

Bei diesem Vorgang empfängt die App bei der Anmeldung des Benutzers einen Autorisierungscode vom v2.0-Endpunkt, der die Berechtigung für die App darstellt, Back-End-Dienste für den derzeit angemeldeten Benutzer aufzurufen.  Die App kann den Autorisierungscode dann in den Hintergrund treten lassen und gegen ein OAuth 2.0-Zugriffstoken und ein -Aktualisierungstoken austauschen.  Die App kann mithilfe des Zugriffstokens Web-APIs in HTTP-Anforderungen authentifizieren und kann das Aktualisierungstoken verwenden, um neue Zugriffstoken zu erhalten, wenn die älteren abgelaufen sind.

![Abbildung der Verantwortlichkeitsbereiche systemeigener Apps](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Singe Page-Apps (Javascript)
Viele moderne Apps verfügen über ein Single Page-App-Front-End, das in erster Linie in Javascript geschrieben ist und häufig SPA-Frameworks wie z. B. AngularJS, Ember.js oder Durandal verwendet.  Azure AD-app-Modell v2. 0 unterstützt diese apps mithilfe der [OAuth 2.0 impliziten Fluss](active-directory-v2-protocols-implicit.md).

In diesem Datenfluss empfängt die App Token vom Autorisierungsendpunkt der Version 2.0 direkt, ohne dass eine Kommunikation zwischen Back-End-Server und Server stattfindet.  Dadurch können sämtliche Authentifizierungslogik und die Verarbeitung der Sitzung vollständig im JavaScript-Client erfolgen, ohne Seitenumleitungen durchzuführen.

Um dieses Szenario in Aktion zu sehen, versuchen, Sie eine einzelne Seite app-Codebeispiele in unserer [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) Abschnitt.

## Aktuelle Einschränkungen der Vorschau
Diese Typen von Apps werden derzeit von der App-Modell v2.0-Vorschau nicht unterstützt, sind jedoch in Planung, um bei allgemeiner Verfügbarkeit rechtzeitig unterstützt zu werden.  Zusätzliche Einschränkungen und Einschränkungen für die öffentliche Vorschau von v2. 0-app-Modell wird in beschrieben die [v2. 0-Vorschau-Einschränkungen Artikel](active-directory-v2-limitations.md).

### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen.  Diese apps können authentifizieren und Abrufen von Token mithilfe des app Identität (anstelle der delegierten Benutzeridentität) die [OAuth 2.0-Clientanmeldeinformationen Fluss](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).  

Dieser Vorgang wird derzeit nicht vom App-Modell v2.0 unterstützt, d. h. Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzeranmeldevorgang aufgetreten ist.  Der Ablauf für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt.  Wenn der Client finden Sie unter Anmeldeinformationen geleitet werden soll die allgemein verfügbare Azure AD-Dienst finden Sie in den [Daemon-Beispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### Verkettete Web-APIs (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, die eine andere heruntergestreamte Web-API aufrufen muss. Beide werden vom App-Modell v2.0 gesichert.  Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, wie z. B. Office 365 oder die Graph-API.

Hierzu verketteten Web-API unterstützt werden, mithilfe der OAuth 2.0 Jwt Träger Anmeldeinformationen Grant, auch bekannt als die [On-Behalf-Of Flow](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Der Im-Auftrag-von-Vorgang ist in der Vorschaufunktion des App-Modells v2.0 derzeit noch nicht implementiert.  Sehen Sie die Funktionsweise von diesem Datenstrom in die allgemein verfügbare Azure AD-service finden Sie in der [On-Behalf-Of Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).



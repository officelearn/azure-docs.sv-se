
<properties
    pageTitle="App-Modell v2.0: OAuth-Protokoll | Microsoft Azure"
    description="Erstellen von Webanwendungen mit der Azure AD-Implementierung des OAuth 2.0-Authentifizierungsprotokolls."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# App-Modell v2.0 Vorschauversion: Protokolle – OAuth 2.0-Autorisierungscodefluss

Der OAuth 2.0-Autorisierungcodefluss kann in Apps verwendet werden, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu gewähren.  Die Implementierung von OAuth 2.0 im App-Modell v2.0 ermöglicht es Ihnen, sich bei mobilen Apps und Desktop-Apps anzumelden und über APIs darauf zuzugreifen.  Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

<!-- TODO: Need link to libraries -->   

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

Eines der OAuth 2.0-Autorisierungscodes wird in in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749).  Dient zur Authentifizierung und Autorisierung in der Mehrzahl der app-Typen, einschließlich durchführen [web-apps](active-directory-v2-flows.md#web-apps) und [systemintern installiert apps](active-directory-v2-flows.md#mobile-and-native-apps).  Durch den Codefluss können Apps Zugriffstoken sicher abrufen, die zum Zugriff auf Ressourcen verwendet werden können, die mithilfe des App-Modells v2.0 geschützt werden.  



## Anfordern eines Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize`-Endpunkt leitet.  In dieser Anforderung gibt der Client die Berechtigungen an, die er vom Benutzer abrufen muss:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e        // Your registered Application Id
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob     // Your registered Redirect Uri, url encoded
&response_mode=query                                    // 'query', 'form_post', or 'fragment'
&scope=                                              // See table below
openid%20
offline_access%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345                                           // Any value provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | erforderlich | Die Id der Anwendung, die das registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen. |
| response_type | erforderlich | Muss `code` für den Autorisierungscodefluss enthalten. |
| redirect_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können.  Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen.  Ein einzelner Bereichswert zeigt dem v2.0-Endpunkt sowohl die Ressource als auch die Berechtigungen für diese angeforderte Ressource an.  Bereiche nehmen die Form einer `<app identifier URI>/<scope value>` an.  Im obigen Beispiel wird der App-Bezeichner für die Azure AD Graph-API verwendet, `https://graph.windows.net`, und zwei Berechtigungen werden angefordert: `directory.read` und `directory.write`.  Eine ausführlichere Erläuterung von Bereichen, finden Sie in der [app Modellverweis v2. 0 Bereich](active-directory-v2-scopes.md).  |
| response_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll.  Kann entweder "Abfrage", "form_post" oder "Fragment" sein.
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird.  Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln.  Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern.  Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an.  Zu diesem Zeitpunkt die einzigen gültigen Werte sind 'Anmeldung', 'none', "stimmen".  `prompt=login` den Benutzer zur Eingabe von Anmeldeinformationen auf die Anforderung, Inverser_Operator für einmaliges Anmelden wird erzwungen werden.  `prompt=none` ist das Gegenteil – Dadurch wird sichergestellt, dass der Benutzer eine interaktive Aufforderung überhaupt nicht angezeigt werden.  Wenn die Anforderung über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, wird der Endpunkt v2. 0 einen Fehler zurück.  `prompt=consent` OAuth Zustimmungsdialogfeld wird ausgelöst werden, nachdem der Benutzer, meldet sich der Benutzer zum Gewähren von Berechtigungen für die app. |
| login_hint | optional | Dieser Wert kann dazu verwendet werden, das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen.  Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind.  Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen.  Details der [Berechtigungen, die Zustimmung und Multi-Tenant-apps sind hier bereitgestellten](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...   // the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF            // a value generated by the v2.0 endpoint
&state=12345                                                      // the value provided in the request
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Code | Die Authorization_code, die die Anwendung angefordert hat. Die app kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden.  Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| session_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die app sollte überprüfen, dass der Status in der Anforderung und Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.  |

## Anfordern eines Zugriffstokens
Nun, da Sie einen Autorisierungscode erworben und die Berechtigung vom Benutzer erhalten haben, können Sie den `code` für ein `access_token` auf die gewünschte Ressource einlösen, indem Sie eine `POST`-Anforderung an den `/token`-Endpunkt senden:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
    "scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------------- |
| client_id | erforderlich | Die Id der Anwendung, die das registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen. |
| grant_type | erforderlich | Muss der `authorization_code` für den Autorisierungscodefluss sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen.  Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ersten Abschnitt angefordert wurden.  Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück.  Eine ausführlichere Erläuterung von Bereichen, finden Sie unter [Berechtigungen, die Zustimmung und Bereiche](active-directory-v2-scopes.md).  |
| Code | erforderlich | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben.   |
| client_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben.  Es sollte nicht in eine systemeigene Anwendung, verwendet werden, weil Client_secrets zuverlässig auf Geräten gespeichert werden kann.  Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
    "id_token_expires_in": "3599"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access_token | Das angeforderte Zugriffstoken. Die Anwendung kann dieses Token verwenden, die geschützte Ressource, z. B. eine Web-API zu authentifizieren. |
| token_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt.  |
| expires_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh_token |  Ein Aktualisierungstoken von OAuth 2.0. Die Anwendung kann dieses Token verwenden Weitere Zugriffstoken abzurufen, nachdem das aktuelle Zugriffstoken abgelaufen ist.  Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten.  Weitere Informationen finden Sie in der [v2. 0-Tokenverweis](active-directory-v2-tokens.md).  |
| id_token | Ein unsigniertes JSON-Webtoken (JWT). Die app kann base64Url Decodieren der Segmente dieses Token zum Anfordern von Informationen über den angemeldeten Benutzer. Die app die Werte zwischengespeichert und angezeigt werden kann, aber es sollte nicht auf diesen beruhen, für die Autorisierung oder Sicherheitsgrenzen.  Weitere Informationen zu Id_tokens finden Sie unter der [v2. 0-app-Modell Tokenverweis](active-directory-v2-tokens.md). |
| id_token_expires_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |


Fehlerantworten sehen wie folgt aus:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.  |

## Verwenden des Zugriffstokens
Nachdem Sie erfolgreich ein `access_token` abgerufen haben, können Sie das Token für Anforderungen an Web-APIs verwenden, indem Sie es in den `Authorization`-Header einschließen:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Aktualisieren des Zugriffstokens
Zugriffstoken sind kurzlebig. Daher müssen Sie sie nach Ablauf aktualisieren, um weiterhin auf Ressourcen zuzugreifen.  Dazu übermitteln Sie eine weitere `POST`-Anforderung an den `/token`-Endpunkt, dieses Mal unter Angabe des `refresh_token` statt des `code`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "refresh_token",
    "client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
    "scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | -------- |
| client_id | erforderlich | Die Id der Anwendung, die das registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen. |
| grant_type | erforderlich | Muss der `refresh_token` für diesen Abschnitt des Autorisierungscodeflusses sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen.  Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ursprünglichen Autorisierungscode-Abschnitt angefordert wurden.  Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück.  Eine ausführlichere Erläuterung von Bereichen, finden Sie unter [Berechtigungen, die Zustimmung und Bereiche](active-directory-v2-scopes.md).  |
| refresh_token | erforderlich | Das Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben.   |
| client_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben.  Es sollte nicht in eine systemeigene Anwendung, verwendet werden, weil Client_secrets zuverlässig auf Geräten gespeichert werden kann.  Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
    "id_token_expires_in": "3599"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access_token | Das angeforderte Zugriffstoken. Die Anwendung kann dieses Token verwenden, die geschützte Ressource, z. B. eine Web-API zu authentifizieren. |
| token_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt.  |
| expires_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh_token |  Ein neues Aktualisierungstoken von OAuth 2.0. Ersetzen Sie das alte Aktualisierungstoken durch das neu erworbene, um sicherzustellen, dass Ihre Aktualisierungstoken so lange wie möglich gültig bleiben.  |
| id_token | Ein unsigniertes JSON-Webtoken (JWT). Die app kann base64Url Decodieren der Segmente dieses Token zum Anfordern von Informationen über den angemeldeten Benutzer. Die app die Werte zwischengespeichert und angezeigt werden kann, aber es sollte nicht auf diesen beruhen, für die Autorisierung oder Sicherheitsgrenzen.  Weitere Informationen zu Id_tokens finden Sie unter der [v2. 0-app-Modell Tokenverweis](active-directory-v2-tokens.md). |
| id_token_expires_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |

Fehlerantworten sehen wie folgt aus:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.  |

## Zusammenfassung
Allgemein sieht der gesamte Authentifizierungsablauf für eine systemeigene oder mobile Anwendung etwa wie folgt aus:

![OAuth-Autorisierungscodefluss](../media/active-directory-v2-flows/convergence_scenarios_native.png)


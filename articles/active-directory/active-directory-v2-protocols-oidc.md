
<properties
    pageTitle="App-Modell v2.0: OpenID Connect-Protokoll | Microsoft Azure"
    description="Erstellen von Webanwendungen mit der Azure AD-Implementierung des OpenID Connect-Authentifizierungsprotokolls."
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

# App-Modell v2.0 (Vorschauversion): Protokolle – OpenID Connect
OpenID Connect ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, mit dem Benutzer sicher bei Webanwendungen angemeldet werden können.  Die Implementierung von OpenID Connect im App-Modell v2.0 ermöglicht es Ihnen, sich bei Ihren webbasierten Anwendungen anzumelden und über APIs darauf zuzugreifen.  Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) Erweitert das OAuth 2.0 *Autorisierung* Protokoll für die Verwendung als ein *Authentifizierung* Protokoll, das einzelnen Installationsablauf anmelden mithilfe von OAuth.  Dabei wird das Konzept eines `id_token` eingeführt, eines Sicherheitstokens, mit dem der Client die Identität des Benutzers überprüfen und grundlegende Profilinformationen über den Benutzer erhalten kann.  Da sie OAuth 2.0 erweitert, können apps sicher abrufen **Access_tokens** die kann verwendet werden, Zugriff auf Ressourcen, die von gesicherte ein [autorisierungsserver](active-directory-v2-protocols.md#the-basics).  OpenID Connect wird empfohlen, wenn Sie erstellen ein [-Webanwendung](active-directory-v2-flows.md#web-apps) die auf einem Server gehostet und über einen Browser zugegriffen wird.

## Senden der Anmeldeanforderung
Wenn die Web-App den Benutzer authentifizieren muss, kann sie ihn direkt an den `/authorize`-Endpunkt weiterleiten.  Diese Anforderung ähnelt der erste Abschnitt einer der [OAuth 2.0 Authorization Codefluss](active-directory-v2-protocols-oauth-code.md), mit ein paar wichtige Unterschiede:

- Die Anforderung muss im `scope`-Parameter den Bereich `openid` enthalten.
- Der `response_type`-Parameter muss `id_token` enthalten.
- Die Anforderung muss den `nonce`-Parameter enthalten.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=query                                  // 'query', 'form_post', or 'fragment'
&scope=openid                                        // Translates to the 'Read your profile' permission
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| client_id | erforderlich | Die Id der Anwendung, die das registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen. |
| response_type | erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten.  Es können auch andere Antworttypen enthalten sein, z. B. `code`. |
| redirect_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können.  Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen.  Für OpenID Connect muss der Bereich `openid` enthalten sein, der sich auf die Berechtigung "Anmelden im Profil und Lesen des Profils" in der Zustimmungsbenutzeroberfläche übersetzt.  Sie können in diese Anforderung auch andere Bereiche für das Anfordern der Zustimmung aufnehmen.  |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird.  Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern.  Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren.  |
| response_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll.  Kann entweder "Abfrage", "form_post" oder "Fragment" sein.  
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird.  Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln.  Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern.  Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an.  Zu diesem Zeitpunkt die einzigen gültigen Werte sind 'Anmeldung', 'none', "stimmen".  `prompt=login` den Benutzer zur Eingabe von Anmeldeinformationen auf die Anforderung, Inverser_Operator für einmaliges Anmelden wird erzwungen werden.  `prompt=none` ist das Gegenteil – Dadurch wird sichergestellt, dass der Benutzer eine interaktive Aufforderung überhaupt nicht angezeigt werden.  Wenn die Anforderung über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, wird der Endpunkt v2. 0 einen Fehler zurück.  `prompt=consent` OAuth Zustimmungsdialogfeld wird ausgelöst werden, nachdem der Benutzer, meldet sich der Benutzer zum Gewähren von Berechtigungen für die app. |
| login_hint | optional | Dieser Wert kann dazu verwendet werden, das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen.  Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind.  Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen.  Details der [Berechtigungen, die Zustimmung und Multi-Tenant-apps sind hier bereitgestellten](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...   // the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF            // a value generated by the v2.0 endpoint
&state=12345                                                    // the value provided in the request
&id_token_expires_in=3600

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id_token | Die Id_token, die die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen.  Weitere Informationen zu Id_tokens und deren Inhalt befindet sich auf der [token v2. 0-Endpunktverweis](active-directory-v2-tokens.md).  |
| session_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die app sollte überprüfen, dass der Status in der Anforderung und Antwort identisch sind. |
| id_token_expires_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |


Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.  |

## Überprüfen des ID-Tokens
Nur empfangen ein Id_token reicht nicht aus, um den Benutzer zu authentifizieren; Sie müssen die Id_token Signatur überprüfen und überprüfen die Ansprüche im Token pro Ihrer app-Anforderungen.  Der Endpunkt v2. 0 verwendet [JSON Web Tokens (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und Kryptografie mit öffentlichem Schlüssel zum Signieren von Token, und stellen Sie sicher, dass sie gültig sind.

Das App-Modell v2.0 verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die App Informationen über das App-Modell v2.0 in Laufzeit abrufen kann.  Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel.  Der Metadatenendpunkt enthält ein JSON-Dokument, das Sie hier finden:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Eine der Eigenschaften dieses Konfigurationsdokuments ist der `jwks_uri`, dessen Wert für das App-Modell v2.0 wie folgt lautet:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Sie können die öffentlichen RSA256-Schlüssel an diesem Endpunkt zum Überprüfen der Signatur des ID-Tokens verwenden.  Es gibt mehrere Schlüssel an diesem Endpunkt zu einem bestimmten Zeitpunkt. Sie werden jeweils durch ein `kid` identifiziert.  Der Header des ID-Tokens enthält außerdem einen `kid`-Anspruch, der anzeigt, welcher Schlüssel zum Signieren des ID-Tokens verwendet wurde.  

Finden Sie unter der [v2. 0-app-Modell Tokenverweis](active-directory-v2-tokens.md) Weitere Informationen einschließlich [Token überprüfen](active-directory-v2-tokens.md#validating-tokens) und [wichtige Informationen zum Signaturschlüsselrollover](active-directory-v2-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen:

- Sie sollten den `nonce`-Anspruch überprüfen, um Token-Replay-Angriffe zu verhindern.  Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
- Sie sollten den `aud`-Anspruch überprüfen, um sicherzustellen, dass das ID-Token für Ihre App ausgestellt wurde.  Dessen Wert sollte der `client_id` der App entsprechen.
- Sie sollten die Ansprüche `iat` und `exp` überprüfen, um sicherzustellen, dass das ID-Token noch nicht abgelaufen ist.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen.  Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen der Benutzerorganisation wurde für die app registriert.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die Multi-Factor Authentication.

Weitere Informationen zu den Ansprüchen in einer Id_token finden Sie unter der [v2. 0-app-Modell Tokenverweis](active-directory-v2-tokens.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden.  Diese Informationen kann für die Anzeige, für Datensätze, für die Autorisierung usw. verwendet werden.

## Senden einer Abmeldungsanforderung

Der OpenIdConnect-`end_session_endpoint` wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Dies bedeutet, dass Ihre App keine Anforderung an den v2.0-Endpunkt zum Beenden der Sitzung eines Benutzers und zum Löschen von Cookies, die vom v2.0-Endpunkt festgelegt wurden, senden kann.
Um einen Benutzer abzumelden, kann eine App ganz einfach die eigene Sitzung mit dem Benutzer beenden, und die Sitzung des Benutzers mit dem v2.0-Endpunkt unverändert lassen.  Wenn der Benutzer das nächste Mal versucht, sich anzumelden, wird eine Seite mit dem Hinweis "Konto auswählen" angezeigt, auf der die aktiv angemeldeten Konten aufgeführt werden.
Auf dieser Seite kann der Benutzer sich von jedem Konto abmelden, sodass die Sitzung mit dem v2.0-Endpunkt beendet wird.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## Übersichtsdiagramm für die Anmeldung
Der grundlegendste Anmeldevorgang umfasst die folgenden Schritte:

![OpenId Connect-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Abrufen von Zugriffstoken
Viele Web-Apps müssen nicht nur den Benutzer anmelden, sondern auch mithilfe von OAuth im Namen dieses Benutzers auf einen Webdienst zugreifen.  Dieses Szenario kombiniert OpenID Connect für die Benutzerauthentifizierung und ruft gleichzeitig einen Autorisierungscode („authorization_code) ab, der mithilfe des OAuth-Autorisierungscodeflusses Zugriffstoken („access_token“) abrufen kann:  Zum Abrufen von Zugriffstoken müssen Sie die oben aufgeführte Anmeldeanforderung leicht abwandeln:


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=query                                  // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20                                        
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Durch das Einschließen von Berechtigungsbereichen in die Anforderung und durch die Verwendung von `response_type=code+id_token` stellt der v2.0-Endpunkt sicher, dass der Benutzer den im `scope`-Abfrageparameter angegebenen Berechtigungen zugestimmt hat, und gibt dann der App einen Autorisierungscode zum Austausch für ein Zugriffstoken zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...   // the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...  // the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF            // a value generated by the v2.0 endpoint
&state=12345                                                    // the value provided in the request
&id_token_expires_in=3599

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id_token | Die Id_token, die die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen.  Weitere Informationen zu Id_tokens und deren Inhalt befindet sich auf der [v2. 0-app-Modell Tokenverweis](active-directory-v2-tokens.md).  |
| Code | Die Authorization_code, die die Anwendung angefordert hat. Die app kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden.  Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| session_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die app sollte überprüfen, dass der Status in der Anforderung und Antwort identisch sind. |
| id_token_expires_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.  |

Nachdem Sie einen Autorisierungs`code` und einen `id_token` erhalten haben, können Sie den Benutzer anmelden und Zugriffstoken in seinem Namen abrufen.  Um den Benutzer anzumelden, müssen Sie überprüfen die `id_token` genau wie beschrieben [über](#validating-the-id-token).  Um Zugriffstoken zu erhalten, Sie können die Schritte beschrieben, die unserer [OAuth-Protokoll-Dokumentation](active-directory-v2-protocols-oidc.md#request-an-access-token).

## Übersichtsdiagramm für das Abrufen von Token

Der vollständige Ablauf für die Anmeldung mit OpenID Connect und den Abruf von Token sieht etwa wie folgt aus:

![OpenId Connect-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)


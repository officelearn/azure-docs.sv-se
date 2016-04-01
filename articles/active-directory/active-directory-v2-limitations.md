<properties
    pageTitle="App-Modell v2.0: Einschränkungen | Microsoft Azure"
    description="Eine Liste der Einschränkungen im Azure AD App-Modell v2.0."
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

# App-Modell v2.0 (Vorschauversion): Einschränkungen

Es gibt mehrere Features und Funktionen im App-Modell v2.0, die während der Phase der öffentlichen Vorschauversion noch nicht unterstützt werden.  Jede dieser Einschränkungen wird aufgehoben, bevor das App-Modell v2.0 allgemein verfügbar gemacht wird, allerdings sollten Sie diese kennen, wenn Sie während der öffentlichen Vorschauphase Apps erstellen.

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Unterstützung für Produktions-Apps
Apps, die mit dem App-Modell v2.0 integriert werden, sollten der Öffentlichkeit nicht als Produktionsanwendungen bereitgestellt werden.  Das App-Modell v2.0 befindet sich derzeit in der öffentlichen Vorschau. Wichtige Änderungen könnten daher jederzeit eingeführt werden, und es wird keine SLA durch den Dienst gewährt.  Für möglicherweise auftretende Vorfälle wird kein Support gewährt.  Wenn Sie bereit sind, die Risiken einer Abhängigkeit von einem Dienst zu übernehmen, der sich in der Entwicklung befindet, kontaktieren Sie uns unter @AzureAD, um den Umfang der App oder des Dienstes zu erörtern.

## Einschränkungen für Apps
Die folgenden Typen von Apps werden derzeit nicht in der öffentlichen Vorschau von App-Modell v2.0 unterstützt.  Eine Beschreibung der unterstützten Typen von apps, finden Sie unter [in diesem Artikel](active-directory-v2-flows.md).

##### Singe Page-Apps (Javascript)
Viele moderne Apps verfügen über ein Single Page-App-Front-End, das in erster Linie in Javascript geschrieben ist und häufig SPA-Frameworks wie z. B. AngularJS, Ember.js oder Durandal verwendet.  Die allgemein verfügbare Azure AD-Dienst unterstützt diese apps mithilfe der [implizite OAuth 2.0-Fluss](active-directory-v2-protocols.md#oauth2-implicit-flow) -jedoch diesem Datenstrom ist noch nicht verfügbar in V2. 0-app-Modell.  Dies wird sich in Kürze ändern.

Wenn Sie zum Abrufen einer einseitigen Anwendung mit dem v2. 0-app-Modell arbeiten gespannt sind, können Sie mit Authentifizierung implementieren die [web-app-Fluss](active-directory-v2-flows.md#web-apps).  Dies ist allerdings nicht die empfohlene Vorgehensweise, und die Dokumentation für dieses Szenario ist begrenzt.  Wenn Sie ein Gefühl für das SPA-Szenario möchten, sehen Sie sich die [Allgemein verfügbare Azure AD-SPA Codebeispiel](active-directory-devquickstarts-angular.md).

##### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen.  Diese apps können authentifizieren und Abrufen von Token mithilfe des app Identität (anstelle der delegierten Benutzeridentität) die [OAuth 2.0-Clientanmeldeinformationen Fluss](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).  

Dieser Vorgang wird derzeit nicht vom App-Modell v2.0 unterstützt, d. h. Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzeranmeldevorgang aufgetreten ist.  Der Ablauf für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt.  Wenn Sie, um den Client finden Sie unter möchten Anmeldeinformationen in einem allgemein verfügbaren Azure AD-app-Modell, sehen Sie sich die [Daemon-Beispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Verkettete Web-APIs (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, die eine andere heruntergestreamte Web-API aufrufen muss. Beide werden vom App-Modell v2.0 gesichert.  Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, wie z. B. Office 365 oder die Graph-API.

Hierzu verketteten Web-API unterstützt werden, mithilfe der OAuth 2.0 Jwt Träger Anmeldeinformationen Grant, auch bekannt als die [On-Behalf-Of Flow](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Der Im-Auftrag-von-Vorgang ist in der Vorschaufunktion des App-Modells v2.0 derzeit noch nicht implementiert.  Sehen Sie die Funktionsweise von diesem Datenstrom in die allgemein verfügbare Azure AD-service finden Sie in der [On-Behalf-Of Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### Eigenständige Web-APIs
In der Vorschau der v2. 0-app-Modell, haben Sie die Möglichkeit, [Erstellen einer Web-API, die gesichert wird mithilfe von OAuth-Token](active-directory-v2-flows.md#web-apis) aus dem Endpunkt v2. 0.  Allerdings kann die Web-API nur Token von einem Client empfangen, der die gleiche Anwendung-ID teilt.  Das Erstellen eines Drittanbieter-Webdienstes, auf den von mehreren verschiedenen Clients zugegriffen werden kann, wird nicht unterstützt.

Erstellen einer Web-API, die Token von einem bekannten Client mit der gleichen App-Id akzeptiert, finden Sie unter v2. 0-app-Modell Web-API-Beispiele in unserer [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) Abschnitt.

## Einschränkungen für Benutzer
Derzeit wird jede Anwendung, die mit dem App-Modell v2.0 erstellt wird, für alle Benutzer mit einem Microsoft-Konto oder einem Azure AD-Konto veröffentlicht. Jeder Benutzer kann mit beiden Kontotypen erfolgreich zur App navigieren oder diese installieren, ihre Anmeldeinformationen im App-Modell v2.0 eingeben, und den Berechtigungen der App zustimmen.  Sie können den Code der App so schreiben, dass die App Anmeldevorgänge von bestimmten Gruppen von Benutzern ablehnt. Allerdings verhindern Sie damit nicht, dass diese Benutzer der App zustimmen.

Ihre Apps können tatsächlich nicht die Benutzertypen einschränken, die sich in der App anmelden können.  Sie können keine branchenspezifischen Apps erstellen (die auf Benutzer in einer Organisation beschränkt sind), Apps, die ausschließlich für Unternehmensbenutzer verfügbar sind (mit einem Azure AD-Konto), oder Apps, die nur für Verbraucher gedacht sind (mit einem Microsoft-Konto).

## Einschränkungen für App-Registrierungen
Zu diesem Zeitpunkt müssen alle apps, die mit dem Modell der v2. 0-app integrieren möchten erstellen eine neue app-Registrierung unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Alle vorhandenen Azure AD- oder Microsoft-Konto-Apps sind weder mit dem App-Modell v2.0 kompatibel, noch können Apps in einem anderen Portal als dem neuen App-Registrierungsportal registriert werden.  Es gibt keinen Migrationspfad für eine App aus dem allgemein verfügbaren Azure AD-Dienst an das App-Modell v2.0.

Ebenso funktionieren Apps, die im neuen App-Registrierungsportal registriert wurden, ausschließlich mit dem App-Modell v2.0.  Das App-Registrierungsportal kann nicht zum Erstellen von Apps verwendet werden, die erfolgreich in Azure AD oder Microsoft Account-Diensten integriert werden.

Apps, die im neuen Anwendungs-Registrierungsportal registriert sind, sind derzeit auf eine begrenzte Anzahl von Umleitungs-URI-Werten beschränkt.  Der Umleitungs-URI für Web-Apps und -Dienste muss mit dem Schema oder `https` beginnen, während er für alle anderen Plattformen den hartcodierten Wert `urn:ietf:oauth:2.0:oob` verwenden muss.

Um weitere Informationen zum Registrieren einer app im neuen Portal Anwendung Registrierung finden Sie unter [in diesem Artikel](active-directory-v2-app-registration.md).

## Einschränkungen für Dienste und APIs
V2. 0-app-Modell unterstützt derzeit-in für jede Anwendung, die im neuen Portal zur Registrierung von Anwendung registriert, sofern es in der Liste der fällt [authentifizierungsflüsse unterstützt](active-directory-v2-flows.md).  Diese Apps können OAuth 2.0-Zugriffstoken jedoch nur für einen sehr begrenzten Satz von Ressourcen erhalten.  Der v2.0-Endpunkt gibt Zugriffstoken nur für folgende Apps aus:

- Die App, die das Token angefordert hat.  Eine App kann einen Zugriffstoken für sich selbst anfordern, wenn die logische App aus mehreren unterschiedlichen Komponenten oder Schichten besteht.  Um dieses Szenario in Aktion sehen möchten, sehen Sie sich unsere [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) Lernprogramme.
- Outlook E-Mail, Kalender und Kontakte-REST-APIs, die am https://outlook.office.com befinden.  Um zu erfahren, wie eine Anwendung schreiben, die diese APIs zugreift, bezeichnen diese [Office Getting Started](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) Lernprogramme.

In naher Zukunft werden weitere Microsoft Online-Dienste sowie der Support für Ihre Web-APIs und Dienste hinzugefügt.

## Einschränkungen für Bibliotheken und SDKs
Nicht alle Sprachen und Plattformen verfügen über Bibliotheken, die das App-Modell v2.0 unterstützen.  Der Satz von Authentifizierungsbibliotheken ist zurzeit auft .NET, iOS, Android, NodeJS und Javascript beschränkt.  Entsprechende Codebeispiele und Lernprogramme für jede stehen in unserer [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) Abschnitt.

Wenn Sie mit einer anderen Sprache oder Plattform v2. 0-app-Modell eine app integrieren möchten, lesen Sie die [OAuth 2.0 und OpenID Connect-Protokoll – Referenz](active-directory-v2-protocols.md) die wird angewiesen, wie die HTTP-Nachrichten, die zum Kommunizieren mit dem Endpunkt v2. 0 erstellt.

## Einschränkungen für Protokolle
Das App-Modell v2.0 unterstützt Open ID Connect und OAuth 2.0.  Allerdings wurden nicht alle Features und Funktionen der einzelnen Protokolle in das App-Modell v2.0 integriert.  Beispiele hierfür sind:

- Vollständige Unterstützung für den OpenID Connect-`prompt`-Parameter
- Der OpenID Connect-`login_hint`-Parameter
- Der OpenID Connect-`domain_hint`-Parameter
- Der OpenID Connect-`end_sesssion_endpoint`

Um den Bereich der protokollfunktionalität in V2. 0-app-Modell unterstützt besser zu verstehen, lesen Sie unsere [OpenID Connect und OAuth 2.0-Protokoll – Referenz](active-directory-v2-protocols.md).



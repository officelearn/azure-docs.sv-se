<properties
   pageTitle="Entwicklerhandbuch zu Azure Active Directory | Microsoft Azure"
   description="Dieser Artikel enthält eine umfassende Übersicht über die für Entwickler wichtigen Ressourcen zu Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/10/2015"
   ms.author="mbaldwin"/>


# Entwicklerhandbuch zu Azure Active Directory

## Übersicht
Als Plattform vom Typ "Identity Management as a Service (IDMaaS)" stellt Azure Active Directory für Entwickler eine effektive Möglichkeit zum Integrieren der Identitätsverwaltung in ihre Anwendungen dar. Die folgenden Artikel enthalten Übersichten über die Implementierung sowie wichtige Features von Azure Active Directory. Es wird empfohlen, dass Sie in der Reihenfolge zu lesen, oder wechseln Sie zu [Getting started](#getting-started) Wenn Sie informieren möchten.


1. [Die Vorteile der Integration von Azure Active Directory](active-directory-how-to-integrate.md): ermitteln, warum die Integration mit Azure Active Directory die beste Lösung für die sichere Anmeldung und Autorisierung ist.

1. [Active Directory-Authentifizierungsszenarien](active-directory-authentication-scenarios.md): vereinfachte Authentifizierung in Azure Active Directory Anmelden an Ihre Anwendung nutzen.

1. [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md): erfahren Sie, wie hinzufügen, aktualisieren und Entfernen von Azure Active Directory und zu den Brandingrichtlinien für integrierte apps.

1. [Azure Active Directory Graph-API](active-directory-graph-api.md): Verwenden der Azure Active Directory Graph-API programmgesteuert auf Azure Active Directory über REST-API-Endpunkte zugreifen. Beachten Sie, dass Azure AD Graph-API auch über [Microsoft Graph](https://graph.microsoft.io/), eine einheitliche API, die Zugriff auf mehrere Microsoft Cloud-Dienst-APIs über einen einzelnen Endpunkt für den REST-API und mit einem einzelnen Token ermöglicht.

1. [Azure Active Directory-authentifizierungsbibliotheken](active-directory-authentication-libraries.md): einfache Authentifizierung von Benutzern Zugriffstoken mithilfe der Azure-authentifizierungsbibliotheken zu erhalten.


## Erste Schritte

Diese Tutorials sind auf verschiedene Plattformen ausgelegt und ermöglichen Ihnen einen schnellen Einstieg in die Entwicklung mit Azure Active Directory. Voraussetzung ist, müssen Sie [Azure Active Directory-Mandanten](active-directory-howto-tenant.md).

### Mobile Anwendung und PC-Anwendung – Schnellstartanleitungen

|[![iBETRIEBSSYSTEM](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![ANdroid](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![WWindows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![WWindows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![XAmarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![COrdova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Webanwendung – Schnellstartanleitungen

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![JAVA](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JAvascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![NODE.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[JavaScript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Web-API – Schnellstartanleitungen

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![NODE.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Abfragen des Verzeichnisses – Schnellstartanleitung

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph-API](active-directory-graph-api-quickstart.md)|

## Vorgehensweisen

In diesen Artikeln wird beschrieben, wie Sie mit Azure Active Directory bestimmte Aufgaben ausführen:

- [Abrufen eines Azure Active Directory-Mandanten](active-directory-howto-tenant.md)
- [Auflisten Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](active-directory-app-gallery-listing.md)
- [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](active-directory-application-manifest.md)
- [Erstellen einer App mit Office 365-APIs](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Übertragen von Web-Apps für Office 365 an das Verkäuferdashboard (in englischer Sprache)](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Vorschau: Erstellen von Apps, bei denen sich Benutzer sowohl mit persönlichen als auch mit Arbeits- oder Schulkonten anmelden können](active-directory-appmodel-v2-overview.md)
- [Vorschau: Erstellen von Apps, die Kunden registrieren und anmelden](active-directory-b2c-overview.md)


## Referenz

Diese Artikel enthalten grundlegende Referenzen zu REST- und Authentifizierungsbibliothek-APIs, Protokollen, Fehlern, Codebeispielen und Endpunkten.  

###  Support
- [Markiert die Fragen](http://stackoverflow.com/questions/tagged/azure-active-directory): finden Sie durch Suchen nach den Tags Azure Active Directory-Lösungen bei Stack Overflow [Azure Active Directory](http://stackoverflow.com/questions/tagged/azure-active-directory) und [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- [Azure Active Directory-Open-Source-Bibliotheken](http://github.com/AzureAD): die einfachste Möglichkeit, die Quelle einer Bibliothek zu finden ist, in unserem [Bibliotheksliste](active-directory-authentication-libraries.md).

- [Azure Active Directory – Beispiele](http://github.com/AzureADSamples): die einfachste Möglichkeit zum Navigieren in der Liste der Beispiele ist die Verwendung der [Index der Codebeispiele](active-directory-code-samples.md).


### Graph-API

- [Graph-API-Referenz](https://msdn.microsoft.com/library/azure/hh974476.aspx): REST-Referenz für die Azure Active Directory Graph-API. [Zeigen Sie die interaktive Umgebung für die Graph-API-Referenz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph-API-berechtigungsbereiche](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): OAuth 2.0-berechtigungsbereiche, die verwendet werden, um den Zugriff zu steuern, die eine app auf Verzeichnisdaten in einem Mandanten hat.


### Authentifizierungsprotokolle

- [SAML 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn195591.aspx): dem SAML 2.0-Protokoll kann Anwendungen für einmaliges Anmelden für die Benutzer bereitstellen.


- [OAuth 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645545.aspx): können Sie das OAuth 2.0-Protokoll zum Autorisieren des Zugriffs auf Webanwendungen und web-APIs in Azure Active Directory-Mandanten.


- [OpenID Connect 1.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645541.aspx): dem OpenID Connect 1.0-Protokoll wird OAuth 2.0 für die Verwendung als Authentifizierungsprotokoll erweitert.


- [WS-Federation 1.2-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn903702.aspx): das WS-Federation 1.2-Protokoll in der Spezifikation Web Services Federation Version 1.2 angegeben ist.

- [Unterstützte Token und Anspruch Typen](active-directory-token-and-claims.md): Verwenden Sie dieses Handbuch zu verstehen und Auswerten der Ansprüche im Token SAML 2.0- und JSON Web Token (JWT).

## Videos

### Build 2015

In diesen Übersichtspräsentationen zur Entwicklung von Apps mithilfe von Azure Active Directory kommen Mitglieder des Entwicklungsteams zu Wort. In den Präsentationen werden grundlegende Themen abgedeckt, darunter IDMaaS, Authentifizierung, Identitätsverbund und einmaliges Anmelden.

- [Azure Active Directory: Identitätsverwaltung als Dienst für moderne Anwendungen](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Entwickeln von modernen Webanwendungen mit Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Entwickeln von modernen systemeigenen Anwendungen mit Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) ist eine Freitag 1:1-Videoreihe für die Interviews Short (10 bis 15 Minuten) mit Experten auf einer Vielzahl von Azure-Themen festgelegt ist.  Verwenden Sie die Dienstfilterfunktion auf der Seite, um alle Azure Active Directory-Videos anzuzeigen.

- [Azure-Identität 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure-Identität 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure-Identität 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Soziale Netzwerke

- [Active Directory-Team-Blog](http://blogs.technet.com/b/ad/): die neuesten Entwicklungen in der Welt von Azure Active Directory.

- [Azure Active Directory Graph-Teamblog](http://blogs.msdn.com/b/aadgraphteam): Azure Active Directory-Informationen, die für die Graph-API spezifisch ist.

- [Cloud-Identität](http://www.cloudidentity.net): Gedanken zur identitätsverwaltung als Dienst aus einem Prinzipal Azure Active Directory-PM.  

- [Azure Active Directory auf Twitter](https://twitter.com/azuread): Azure Active Directory Ankündigungen in maximal 140 Zeichen.



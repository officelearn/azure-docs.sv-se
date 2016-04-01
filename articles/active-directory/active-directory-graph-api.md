<properties
   pageTitle="Azure Active Directory Graph-API | Microsoft Azure"
   description="Eine Übersicht und eine Schnellstartanleitung für die Graph-API, die den programmgesteuerten Zugriff auf Azure AD über REST-API-Endpunkte ermöglicht."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin" />

# Azure Active Directory Graph-API

> [AZURE.IMPORTANT] Azure AD Graph-API-Funktion ist auch verfügbar durch [Microsoft Graph](https://graph.microsoft.io/), eine einheitliche API, die APIs von anderen Microsoft-Diensten, wie z. B. Outlook, OneDrive, OneNote, Planer und Office-Diagramm und der Zugriff über einen einzelnen Endpunkt und mit einem einzelnen Token enthält.

Die Azure Active Directory Graph-API ermöglicht programmgesteuerten Zugriff auf Azure AD über REST-API-Endpunkte. Anwendungen können die Graph-API verwenden, um CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) für Verzeichnisdaten und Objekte auszuführen. Die Graph-API unterstützt beispielsweise die folgenden allgemeinen Vorgänge für ein Benutzerobjekt:

- Erstellen eines neuen Benutzers in einem Verzeichnis

- Abrufen der detaillierten Eigenschaften eines Benutzers, z. B. seiner Gruppen

- Aktualisieren der Eigenschaften eines Benutzers, z. B. seines Standorts und seiner Telefonnummer oder das Ändern seines Kennworts

- Überprüfen der Gruppenmitgliedschaft eines Benutzers für den rollenbasierten Zugriff

- Deaktivieren oder vollständiges Löschen des Kontos eines Benutzers

Ähnliche Vorgänge wie für Benutzerobjekte können Sie auch für andere Objekte wie Gruppen oder Anwendungen ausführen. Damit die Graph-API in einem Verzeichnis aufgerufen werden kann, muss die Anwendung bei Azure AD registriert und so konfiguriert sein, dass ein Zugriff auf das Verzeichnis zulässig ist. Dies wird in der Regel durch einen Benutzer- oder Administrator-Zustimmungsdatenfluss erreicht.

Um zu den Azure Active Directory Graph-API verwenden, finden Sie unter der [Graph-API-Schnellstart-Handbuch](active-directory-graph-api-quickstart.md), oder Anzeigen der [interaktiven Graph-API-Referenzdokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## Merkmale

Die Graph-API bietet die folgenden Features:

- **REST-API-Endpunkte**: der Graph-API ist eine RESTful-Dienst besteht aus Endpunkten, die mithilfe von HTTP zugegriffen werden. Die Graph-API unterstützt XML- oder JSON (Javascript Object Notation)-Inhaltstypen für Anforderungen und Antworten. Weitere Informationen finden Sie unter [Azure AD-Graph-REST-API-Referenz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Authentifizierung mit Azure AD**: jede Anforderung an die Graph-API muss durch Anfügen eines JSON-Token (JWT) im Autorisierungsheader der Anforderung authentifiziert werden. Dieses Token wird durch eine Anforderung an den Token-Endpunkt von Azure AD und die Bereitstellung gültiger Anmeldeinformationen abgerufen. Sie können den OAuth 2.0-Datenfluss für Clientanmeldeinformationen oder den Datenfluss für die Autorisierungscodegewährung verwenden, um ein Token für den Graph-Aufruf abzurufen. Weitere Informationen [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Rollenbasierte Autorisierung (RBAC)**: Sicherheitsgruppen werden zum Ausführen von RBAC in der Graph-API verwendet. Beispielsweise möchten Sie bestimmen, ob ein Benutzer Zugriff auf eine bestimmte Ressource verfügt, kann die Anwendung Aufrufen der [Überprüfen der Gruppenmitgliedschaft (transitiv)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) Vorgang, der True oder False zurückgibt.

- **Differenzielle Abfragen**: Wenn Sie Änderungen in einem Verzeichnis zwischen zwei Zeiträume ohne müssen häufige Abfragen der Graph-API überprüfen möchten, können Sie eine differenzielle abfrageanforderung vornehmen. Dieser Anforderungstyp gibt nur die Änderungen zurück, die zwischen der vorherigen differenziellen Abfrageanforderung und der aktuellen Anforderung erfolgt sind. Weitere Informationen finden Sie unter [Azure AD Graph-API differenzielle Abfrage](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Verzeichniserweiterungen**: Wenn Sie eine Anwendung, die zum Lesen oder Schreiben eindeutige Eigenschaften für Verzeichnisobjekte benötigt entwickeln, können Sie registrieren und erweiterungswerte mithilfe der Graph-API verwenden. Wenn Ihre Anwendung beispielsweise eine Skype-ID-Eigenschaft für jeden Benutzer erfordert, können Sie die neue Eigenschaft im Verzeichnis registrieren. Sie steht dann für jedes Benutzerobjekt zur Verfügung. Weitere Informationen finden Sie unter [Azure AD Graph-API-Verzeichnisschemaerweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

## Szenarios

Die Graph-API ermöglicht eine Vielzahl von Anwendungsszenarios. Die gängigsten Szenarios sind die folgenden:

- **LOB-Anwendung (einzelner Mandant)**: In diesem Szenario arbeitet ein Unternehmensentwickler für eine Organisation mit Office 365-Abonnement. Der Entwickler erstellt eine Webanwendung, die mit Azure AD interagiert, um Aufgaben wie das Zuweisen einer Lizenz zu einem Benutzer auszuführen. Für diese Aufgabe ist der Zugriff auf die Graph-API erforderlich. Der Entwickler registriert daher die Einzelinstanzanwendung in Azure AD und konfiguriert Lese- und Schreibberechtigungen für die Graph-API. Anschließend wird die Anwendung für die Verwendung eigener Anmeldeinformationen oder der Anmeldeinformationen des aktuell angemeldeten Benutzers konfiguriert, um ein Token zum Aufrufen der Graph-API abzurufen.

- **Software as a Service-Anwendung (mehrinstanzenfähigen)**: In diesem Szenario entwickelt ein unabhängiger Softwarehersteller (ISV) gehostete mehrinstanzenfähige Web-Anwendung, die Benutzerverwaltungsfunktionen für andere Organisationen bereitstellt, die Azure AD verwenden. Da diese Features Zugriff auf Verzeichnisobjekte erfordern, muss die Anwendung die Graph-API aufrufen. Der Entwickler registriert die Anwendung in Azure AD und konfiguriert sie so, dass Lese- und Schreibberechtigungen für die Graph-API erforderlich sind. Anschließend aktiviert er den externen Zugriff, damit andere Organisationen der Verwendung der Anwendung in ihrem Verzeichnis zustimmen können. Wenn sich ein Benutzer in einer anderen Organisation erstmals bei der Anwendung authentifiziert, wird ein Zustimmungsdialogfeld mit den Berechtigungen angezeigt, die die Anwendung anfordert.  Durch die Zustimmung erhält die Anwendung dann die angeforderten Berechtigungen für die Graph-API im Verzeichnis des Benutzers. Weitere Informationen zum Consent Framework finden Sie unter [Überblick über das Consent Framework](active-directory-integrating-applications.md).

## Siehe auch

[Schnellstartanleitung für die Azure AD Graph-API](active-directory-graph-api-quickstart.md)

[AD Graph-REST-Dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md)



<properties
   pageTitle="Grundlegendes zum Azure Active Directory-Anwendungsmanifest | Microsoft Azure"
   description="Bietet ausführliche Informationen zum Azure AD-Anwendungsmanifest, das Bestandteil jeder Anwendungskonfiguration in einem Azure AD-Mandanten ist."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="dkershaw;bryanla"/>

# Grundlegendes zum Azure Active Directory-Anwendungsmanifest

Anwendungen, die in Azure Active Directory (AD) integriert werden, müssen bei einem Azure AD-Mandanten registriert werden, um eine dauerhafte Identitätskonfiguration für die Anwendung bereitzustellen. Diese Konfiguration wird zur Laufzeit geprüft und ermöglicht so Szenarien, in denen eine Anwendung die Authentifizierung/Autorisierung über Azure AD abwickelt/vermittelt. Weitere Informationen zu den Azure AD-Anwendungsmodell, finden Sie unter der [Hinzufügen, aktualisieren und Entfernen einer Anwendung][ADD-UPD-RMV-APP] Artikel.

## Aktualisieren der Identitätskonfiguration für eine Anwendung

Es gibt verschiedene Optionen zum Aktualisieren der Eigenschaften für die Identitätskonfiguration einer Anwendung, die sich im Hinblick auf Funktionen und Komplexität unterscheiden. Es stehen u. a. folgende Optionen zur Verfügung:

- Die **[klassischen Azure-Portal des][AZURE-CLASSIC-PORTAL] Webbenutzeroberfläche** können Sie die am häufigsten verwendeten Eigenschaften einer Anwendung zu aktualisieren. Dies ist die schnellste und am wenigsten fehleranfällige Möglichkeit zum Aktualisieren der Anwendungseigenschaften, sie bietet jedoch im Gegensatz zu den zwei weiteren Methoden keinen vollständigen Zugriff auf alle Eigenschaften.
- Für erweiterte Szenarien müssen Sie zum Aktualisieren der Eigenschaften, die nicht im klassischen Azure-Portal bereitgestellt werden, können Sie ändern die **Anwendungsmanifest**. Dies ist der Schwerpunkt des vorliegenden Artikels, ausführliche Informationen erhalten Sie ab dem nächsten Abschnitt.
- Es ist auch möglich, **Schreiben eine Anwendung, verwendet die [Graph-API][GRAPH-API]** Aktualisierung Ihrer Anwendung, die den größten Aufwand erforderlich ist. Dies kann eine attraktive Option sein, wenn Sie Verwaltungssoftware schreiben oder die Anwendungseigenschaften regelmäßig und in automatisierter Weise aktualisieren müssen.

## Verwenden des Anwendungsmanifests zum Aktualisieren der Identitätskonfiguration einer Anwendung
Über die [klassischen Azure-Portal][AZURE-CLASSIC-PORTAL], Verwaltung der Konfiguration Ihrer Anwendung Identität, können Sie herunterladen und Hochladen von JSON-Datei Darstellung, die ein Anwendungsmanifest aufgerufen wird. Im Verzeichnis wird keine wirkliche Datei gespeichert. Das Anwendungsmanifest ist lediglich ein für die Azure AD-Graph-API-Anwendungsentität ausgeführter HTTP-GET-Vorgang, und der Upload ist ein HTTP-PATCH-Vorgang für die Anwendungsentität.

Daher müssen Sie um zu erfahren, des Formats und der Eigenschaften des Anwendungsmanifests, verweisen die Graph-API [Anwendungsserver-Entität][APPLICATION-ENTITY] Dokumentation. Beispiele für Updates, die sein können jedoch manifest Hochladen der Anwendung ausgeführt.
:

- Deklarieren von Berechtigungsbereichen (oauth2Permissions), die von Ihrer Web-API verfügbar gemacht werden. Finden Sie im Thema "Verfügbarmachen von Web-APIs für andere Anwendungen" [Integration von Anwendungen mit Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] Informationen zum Implementieren von Benutzeridentitätswechsel mithilfe der oauth2Permissions Berechtigungsbereich delegiert. Wie bereits erwähnt, werden alle Eigenschaften der Anwendungsserver-Entität in dokumentiert die Graph-API [Entitäts- und komplexen Verweis][APPLICATION-ENTITY] Artikel, einschließlich des oauth2Permissions Members, eine Auflistung vom Typ [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- Deklarieren von Anwendungsrollen (appRoles), die von Ihrer App verfügbar gemacht werden. Die Anwendungsserver-Entität AppRole-Member ist eine Auflistung vom Typ [AppRole][APPLICATION-ENTITY-APP-ROLE]. Finden Sie unter der [Rollenbasierte Zugriffskontrolle in Cloudanwendungen mit Azure AD][RBAC-CLOUD-APPS-AZUREAD] Artikel ein Beispiel für die Implementierung.
- Deklarieren bekannter Clientanwendungen.
- Fordern Sie bei Azure AD die Ausgabe eines Anspruchs für die Gruppenmitgliedschaften des angemeldeten Benutzers an.  HINWEIS: Durch eine zusätzliche Konfiguration kann ein Anspruch für die Verzeichnisrollenmitgliedschaften des Benutzers ausgegeben werden. Finden Sie unter der [Autorisierung in Cloudanwendungen mit AD-Gruppen][AAD-GROUPS-FOR-AUTHORIZATION] Artikel ein Beispiel für die Implementierung.
- Ermöglichen Sie die Unterstützung von impliziten OAuth 2.0-Code Grant-Datenflüssen (für eingebettete JavaScript-Webseiten oder SPAs [Single Page Applications]) durch Ihre Anwendung.
- Aktivieren Sie die Verwendung von X509-Zertifikaten als geheimer Schlüssel. Finden Sie unter der [-Dienst und der Daemon-apps in Office 365 erstellen][O365-SERVICE-DAEMON-APPS] und [Entwicklerhandbuch für die Authentifizierung mit Azure-Ressourcen-Manager-API ][DEV-GUIDE-TO-AUTH-WITH-ARM] Artikel für Beispiele für die Implementierung.

Das Anwendungsmanifest bietet außerdem eine gute Möglichkeit, den Status Ihrer Anwendungsregistrierung nachzuverfolgen. Da dieser im JSON-Format verfügbar ist, kann die Dateidarstellung in Ihrer Quellcodeverwaltung geprüft werden, gemeinsam mit dem Quellcode der Anwendung.

### Schritt-für-Schritt-Beispiel
Gehen wir nun die erforderlichen Schritte zum Aktualisieren der Identitätskonfiguration Ihrer Anwendung über das Anwendungsmanifest durch:

1. Navigieren Sie zu der [klassischen Azure-Portal][AZURE-CLASSIC-PORTAL] und melden Sie sich mit einem Konto, das Service-Administrator oder Co-Administrator-Berechtigungen verfügt.


2. Scrollen Sie nach der Authentifizierung nach unten, und wählen Sie im linken Navigationsbereich (1) die Azure-Erweiterung "Active Directory" aus. Klicken Sie dann auf den Azure AD-Mandanten, bei dem Ihre Anwendung registriert ist (2).

    ![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-TENANT]


3. Wenn die Verzeichnisseite angezeigt wird, klicken Sie im oberen Bereich der Seite auf "Anwendungen" (1), um eine Liste der beim Mandanten registrierten Anwendungen anzuzeigen. Suchen Sie anschließend in der Liste nach der Anwendung, die Sie aktualisieren möchten, und klicken Sie darauf (2).

    ![Auswählen des Azure AD-Mandanten][SELECT-AZURE-AD-APP]


4. Nachdem Sie die Hauptseite der Anwendung ausgewählt haben, beachten Sie das Feature "Manifest verwalten" unten auf der Seite (1). Wenn Sie auf diesen Link klicken, werden Sie entweder zum Herunterladen oder zum Hochladen der JSON-Manifestdatei aufgefordert. Klicken Sie auf "Manifest herunterladen" (2). Es wird daraufhin ein Dialogfeld zum Bestätigen des Downloads angezeigt. Bestätigen Sie den Download durch Klicken auf "Manifest herunterladen" (3), und öffnen Sie dann entweder die Datei, oder speichern Sie sie lokal (4).

    ![Verwalten des Manifests, Option zum Herunterladen][MANAGE-MANIFEST-DOWNLOAD]

    ![Herunterladen des Manifests][DOWNLOAD-MANIFEST]


5. In diesem Beispiel wurde die Datei lokal gespeichert, sodass die JSON-Datei in einem Editor geöffnet, geändert und anschließend gespeichert werden kann. Nachfolgend sehen Sie, wie die JSON-Struktur im Visual Studio-JSON-Editor aussieht. Beachten Sie, dass sie das Schema für folgt die [Anwendungsserver-Entität][APPLICATION-ENTITY] wie bereits erwähnt:

    ![Aktualisieren der JSON-Manifestdatei][UPDATE-MANIFEST]


6. Wenn Sie die Aktualisierung des Manifests abgeschlossen haben, gehen Sie zur Azure AD-Anwendungsseite im Azure-Portal zurück, klicken Sie erneut auf "Manifest verwalten" (1), wählen Sie diese Mal jedoch die Option "Manifest hochladen" (2). Ähnlich wie beim Download wird ein zweites Dialogfeld angezeigt, indem Sie zur Eingabe des Speicherorts der JSON-Datei aufgefordert werden. Klicken Sie auf "Nach..." (3), dann verwenden Sie das Dialogfeld "Wählen Sie Datei zum Hochladen", wählen Sie die JSON-Datei (4), und klicken Sie auf "Öffnen". Sobald das Dialogfeld geschlossen wurde, klicken Sie auf das OK-Symbol (ein Häkchen) (5), und Ihr Manifest wird hochgeladen.  

    ![Verwalten des Manifests, Option zum Hochladen][MANAGE-MANIFEST-UPLOAD]

    ![Hochladen der JSON-Manifestdatei][UPLOAD-MANIFEST]

    ![Hochladen der JSON-Manifestdatei – Bestätigung][UPLOAD-MANIFEST-CONFIRM]

Das ist alles. Jetzt kann Ihre Anwendung mit der neuen Anwendungskonfiguration ausgeführt werden, basierend auf den Änderungen, die Sie im Manifest durchgeführt haben.

## Nächste Schritte
Verwenden Sie den unten angezeigten DISQUS-Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.

<!--Image references-->
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/



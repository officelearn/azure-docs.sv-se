<properties
   pageTitle="Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschungen | Microsoft Azure"
   description="In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/16/2015"
   ms.author="andkjell"/>

# Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen
In diesem Thema wird die Funktion zum Verhindern von versehentlichen Löschungen (Verhindern versehentlicher Löschvorgänge) in Azure AD Connect beschrieben.

Bei der Installation von Azure AD Connect wird die Funktion zum Schutz vor unbeabsichtigtem Löschen standardmäßig aktiviert und so konfiguriert, das Exporte mit mehr als 500 Löschungen unterbunden werden. Diese Funktion dient zum Schutz vor unbeabsichtigten Konfigurationsänderungen und Änderungen an Ihrem lokalen Verzeichnis, die sich auf eine große Anzahl von Benutzern auswirken würden.

Der Standardwert von 500 Objekten kann mit PowerShell mithilfe von `Enable-ADSyncExportDeletionThreshold` geändert werden. Sie sollten diesen Wert entsprechend der Größe Ihres Unternehmens konfigurieren. Da die Synchronisierungsplanung alle 3 Stunden ausgeführt wird, entspricht der Wert der Anzahl von Löschvorgängen, die innerhalb von 3 Stunden erfolgen.

Wenn diese Funktion aktiviert ist und zu viele Löschungen in Azure AD exportiert werden sollen, wird der Export nicht fortgesetzt, und Sie erhalten Sie die folgende E-Mail-Nachricht:

![Hallo, der Dienst zur Identitätssynchronisierung hat erkannt, dass die Anzahl der Löschungen den konfigurierten Grenzwert für fabrikam.com überschritten hat. Während dieses Identitätssynchronisierungslaufs wurden insgesamt 1.234 Objekte zur Löschung vorgesehen. Hierbei wurde der konfigurierte Grenzwert von 500 Objekten erreicht oder überschritten. Bitte erteilen Sie uns eine entsprechende Bestätigung, dass diese Löschungen bearbeitet werden sollen, bevor wir die Freigabe erteilen. Weitere Informationen über die in dieser E-Mail genannten Fehler finden Sie in der Dokumentation zum Vermeiden versehentlicher Löschungen.](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

Wenn Sie diese Nachricht unerwartet erhalten haben, untersuchen Sie die Grunde dafür, und ergreifen Sie die nötigen Maßnahmen, um das Problem zu beheben. Führen Sie die folgenden Schritte aus, um zu ermitteln, welche Objekte gelöscht werden sollen:

1. Starten Sie **Synchronisierungsdienst** aus dem Startmenü.
2. Wechseln Sie zu **Connectors**.
3. Wählen Sie den Connector mit dem Typ **Azure Active Directory**.
4. Unter **Aktionen** Wählen Sie auf der rechten Seite **Suche Connectorbereich**.
5. Im Popupfenster unter **Bereich** wählen **getrennt, da** und wählen Sie einen Zeitpunkt in der Vergangenheit. Klicken Sie auf **Suche**. Dadurch wird eine Übersicht über alle zu löschenden Objekte angezeigt. Klicken Sie auf die einzelnen Objekte, um zusätzliche Informationen dazu zu erhalten. Sie können auch auf klicken **Einstellung Spalte** Hinzufügen von zusätzlichen Attributen, um im Raster angezeigt werden.

![Connectorbereich durchsuchen](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Wenn alle Löschvorgänge gewünscht sind, gehen Sie folgendermaßen vor:

1. Um den Schutz vorübergehend zu deaktivieren und diese Löschvorgänge zuzulassen, führen Sie folgendes PowerShell-Cmdlet aus: `Disable-ADSyncExportDeletionThreshold`. Wenn nach Anmeldeinformationen gefragt werden, geben Sie ein globaler Administrator mit Azure AD-Konto und Kennwort.
![Anmeldeinformationen](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Mit der Azure Active Directory Connector weiterhin ausgewählt haben, wählen Sie die Aktion **Ausführen** und wählen Sie **exportieren**.
3. Führen Sie zum erneuten Aktivieren des Schutzes folgendes PowerShell-Cmdlet aus: `Enable-ADSyncExportDeletionThreshold`.

## Nächste Schritte
Erfahren Sie mehr über die [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md) Konfiguration.

Erfahren Sie mehr über [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).



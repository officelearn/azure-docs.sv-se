<properties
    pageTitle="Azure AD Connect Health-Vorgänge"
    description="In diesem Artikel werden zusätzliche Vorgänge beschrieben, die nach der Bereitstellung von Azure AD Connect Health ausgeführt werden können."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2015"
    ms.author="billmath"/>

# Azure AD Connect Health-Vorgänge

Das folgende Thema beschreibt die verschiedenen Vorgänge, die mithilfe von Azure AD Connect Health ausgeführt werden können.

## Aktivieren von E-Mail-Benachrichtigungen
Sie können konfigurieren, dass die Azure AD Connect Health-Dienst zum Senden von e-Mail-Benachrichtigungen, wenn Warnungen generiert werden, dass die Identity-Infrastruktur nicht fehlerfrei ist. Benachrichtigungen werden gesendet, wenn eine Warnung generiert und wenn sie als gelöst markiert wird. Folgen Sie den unten stehenden Anweisungen, um E-Mail-Benachrichtigungen zu konfigurieren.
>[AZURE.NOTE] E-Mail-Benachrichtigungen sind standardmäßig deaktiviert.


### So aktivieren Sie Azure AD Connect Health-E-Mail-Benachrichtigungen

1. Öffnen Sie das Blatt "Warnungen" für den Dienst, für den Sie E-Mail-Benachrichtigungen empfangen möchten.
2. Klicken Sie in der Aktionsleiste auf die Schaltfläche "Benachrichtigungseinstellungen".
3. Aktivieren Sie die Option "E-Mail-Benachrichtigung".
4. Aktivieren Sie das Kontrollkästchen, um alle globalen Administratoren zum Empfangen von E-Mail-Benachrichtigungen zu konfigurieren.
5. Wenn Sie E-Mail-Benachrichtigungen an andere E-Mail-Adressen senden lassen möchten, können Sie diese im Feld "Weitere E-Mail-Empfänger" angeben. Um eine E-Mail-Adresse aus dieser Liste zu entfernen, klicken Sie mit der rechten Maustaste auf den Eintrag, und wählen Sie "Löschen".
6. Klicken Sie zum Abschließen der Änderungen auf "Speichern". Alle Änderungen werden erst nach der Auswahl von "Speichern" wirksam.

## Löschen eines Servers oder einer Serverinstanz

### Löschen eines Servers aus einem Azure AD Connect Health-Dienst
In einigen Fällen möchten Sie möglicherweise einen Server aus der Überwachung entfernen. Folgen Sie den unten stehenden Anweisungen, um einen Server aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen eines Servers sind folgende Punkte zu beachten:

- Diese Aktion BEENDET das Erfassen von jeglichen Daten von diesem Server. Dieser Server wird aus dem Überwachungsdienst entfernt. Nach dieser Aktion können Sie keine neuen Warnungen oder Überwachungs- bzw. Nutzungsanalysedaten für diesen Server anzeigen.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT vom Server. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Diese Aktion löscht NICHT die Daten, die von diesem Server bereits erfasst wurden. Diese Daten werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


#### So löschen Sie einen Server aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Server", indem Sie auf dem Blatt "Serverliste" den Namen des Servers auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie die Aktion zum Löschen des Servers, indem Sie den Namen des Servers in das Bestätigungsfeld eingeben.
4. Klicken Sie auf die Schaltfläche "Löschen".


### Löschen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise eine Dienstinstanz entfernen. Folgen Sie den unten stehenden Anweisungen, um eine Dienstinstanz aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen einer Dienstinstanz sind folgende Punkte zu beachten:

- Diese Aktion entfernt die aktuelle Dienstinstanz aus dem Überwachungsdienst.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT von einem der Server, die im Rahmen dieser Dienstinstanz überwacht wurden. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server bzw. den Servern möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Alle Daten aus dieser Dienstinstanz werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung dieses Diensts erneut starten möchten, deinstallieren Sie den Connect Health-Agent von allen zu überwachenden Servern, und installieren Sie ihn anschließend erneut. Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


#### So löschen Sie eine Dienstinstanz aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Dienst", indem Sie auf dem Blatt "Dienstliste" die ID des Diensts (Name der Farm) auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie den Dienstnamen, indem Sie es in das Bestätigungsfeld eingeben. (zum Beispiel: sts.contoso.com)
4. Klicken Sie auf die Schaltfläche "Löschen".
<br><br>


[//]: # (Start of RBAC section)
## Verwalten des Zugriffs mit rollenbasierter Zugriffssteuerung
### Übersicht
[Role-Based Access Control](role-based-access-control-configure.md) für Azure AD Connect Health Azure AD Connect Health-Dienst auf Benutzer und/oder Gruppen außerhalb von globalen Administratoren zugreifen können. Dies wird erreicht, indem Sie den entsprechenden Benutzern und/oder Gruppen Rollen zuweisen, und bietet einen Mechanismus, um die globalen Administratoren in Ihrem Verzeichnis einzugrenzen.

#### Roles (Rollen)
Azure AD Connect Health unterstützt die folgenden integrierten Rollen.

| Rolle | Berechtigungen |
| ----------- | ---------- |
| Besitzer | Besitzer können ***Verwalten des Zugriffs*** (z. B. Zuweisen von Rollen für eine Benutzergruppe) ***alle Informationen anzeigen*** (z. B. Warnungen anzeigen) aus dem Portal und ***Ändern Sie die Einstellung*** (z. B. e-Mail-Benachrichtigungen) in Azure AD Connect Health. <br>In der Standardeinstellung globalen Azure AD-Administratoren werden dieser Rolle zugewiesen und kann nicht geändert werden.  |
|Mitwirkender|  Contributors können ***alle Informationen anzeigen*** (z. B. Warnungen anzeigen) aus dem Portal und ***Ändern Sie die Einstellung*** (z. B. e-Mail-Benachrichtigungen) in Azure AD Connect Health.|
|Leser| Leser können ***alle Informationen anzeigen*** (z. B. Warnungen anzeigen) aus dem Portal in Azure AD Connect Health.|

Alle anderen Rollen (z. B. "User Access Administrators" oder "DevTest Lab Users") haben keine Auswirkung auf den Zugriff innerhalb von Azure AD Connect Health, auch wenn sie in der Portalumgebung verfügbar sind.

#### Zugriffsbereich

Azure AD Connect unterstützt Zugriffsverwaltung auf zwei Ebenen:

- ***Alle Instanzen des***: Dies ist die empfohlene Vorgehensweise für die meisten Kunden und steuert den Zugriff für alle Dienstinstanzen (z. B. ein AD FS-Farm) für alle Arten von Rollen, die von Azure AD Connect Health überwacht werden.

- ***Dienstinstanz***: In einigen Fällen müssen möglicherweise den Zugriff basierend auf Typen oder von einer Dienstinstanz zu trennen. In diesem Fall können Sie den Zugriff auf Dienstinstanzebene verwalten.  

Berechtigungen werden erteilt, wenn ein Benutzer Zugriff auf Verzeichnisebene oder Dienstinstanzebene hat.


### So erteilen Sie Benutzern oder Gruppen Zugriff auf Azure AD Connect Health
#### Schritt 1: Auswählen des entsprechenden Zugriffsbereichs
Ermöglicht einem Benutzer den Zugriff auf die *Alle Dienstinstanzen* innerhalb von Azure AD Connect Health level, öffnen Sie das Hauptfenster in Azure AD Connect Health.<br>
#### Schritt 2: Hinzufügen von Benutzern und Gruppen sowie Zuweisen von Rollen
1. Klicken Sie im Bereich "Benutzer" im Abschnitt konfigurieren.<br>
![Azure AD Connect Health – RBAC Hauptblatt](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Wählen Sie "Hinzufügen" aus.
3. Wählen Sie die "Rolle" z. B. "Owner"<br>
![Azure AD Connect Health – RBAC Benutzer hinzufügen ](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Geben Sie den Namen oder die Kennung des entsprechenden Benutzers oder der Gruppe ein. Sie können einen oder mehrere Benutzer oder Gruppen gleichzeitig auswählen. Klicken Sie auf "auswählen".
![Azure AD Connect Health – RBAC Benutzer auswählen](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Wählen Sie "Ok".<br>

6. Sobald die rollenzuweisung abgeschlossen ist, werden der Benutzer und/oder Gruppen in der Liste angezeigt.<br>
![Azure AD Connect Health – RBAC Benutzerliste](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Diese Schritte ermöglichen den aufgeführten Benutzern und der Gruppe Zugriff basierend auf den zugewiesenen Rollen.
>[AZURE.NOTE]
- Globale Administratoren verfügen immer über Vollzugriff auf alle Vorgänge jedoch globaler Administrator-Konten werden nicht in der obigen Liste vorhanden sein.
- Funktion "Benutzer einladen" wird nicht in Azure AD Connect Health unterstützt.

#### Schritt 3: Freigeben des Blattspeicherorts für Benutzer oder Gruppen
1. Nach dem Zuweisen von Berechtigungen kann Benutzer Azure AD Connect Health zugreifen, indem Sie auf [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Einmal kann auf dem Blatt der Benutzer das Blatt oder verschiedene Teile zum Dashboard anheften einfach durch Klicken auf "An Dashboard anheften"<br>
![Azure AD Connect Health – RBAC Blatt anhaften](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Ein Benutzer mit der Rolle "Leser" zugewiesen werden nicht zum Ausführen des Vorgangs "erstellen", um Azure AD Connect Health-Erweiterung aus dem Azure Marketplace erhalten können. Dieser Benutzer kann weiterhin über den oben aufgeführten Link auf das Blatt zugreifen. Für nachfolgende Verwendungen kann der Benutzer das Blatt an das Dashboard anheften.

### Entfernen von Benutzern und/oder Gruppen
Entfernen Sie einen Benutzer oder eine Gruppe in Azure AD Connect Health rollenbasierten Zugriffsteuerung Teil hinzugefügt werden, indem Sie mit der rechten Maustaste, und Entfernen auswählen.<br>
![Azure AD Connect Health – RBAC Benutzer entfernen](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installieren des Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)



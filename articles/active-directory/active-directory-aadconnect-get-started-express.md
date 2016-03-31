<properties
    pageTitle="Azure AD Connect: Erste Schritte mit Expresseinstellungen | Microsoft Azure"
    description="Informationen zum Herunterladen, Installieren und Ausführen des Setup-Assistenten für Azure AD Connect."
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
    ms.date="10/13/2015"
    ms.author="billmath;andkjell"/>

# Erste Schritte mit Azure AD Connect mit Expresseinstellungen
Die folgende Dokumentation hilft Ihnen beim Einstieg mit Azure Active Directory Connect. In dieser Dokumentation wird die Verwendung der Expressinstallation für Azure AD Connect behandelt.  

## Verwandte Dokumentation
Wenn Sie die Dokumentation auf nicht lesen [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md), die folgende Tabelle enthält Links zu verwandten Themen. Sie müssen die ersten beiden, fett hervorgehobenen Themen gelesen haben, bevor Sie mit der Installation beginnen.

| Thema |  |
| --------- | --------- |
| **Azure AD Connect herunterladen** | [Azure AD Connect herunterladen](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware und Voraussetzungen** | [Azure AD Connect: Hardware und Voraussetzungen](active-directory-aadconnect-prerequisites.md) |
| Installation mit benutzerdefinierten Einstellungen | [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Nach der Installation | [Überprüfen der Installation und Zuweisen von Lizenzen ](active-directory-aadconnect-whats-next.md) |
| Für die Installation verwendete Konten | [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-accounts-permissions.md) |


## Expressinstallation von Azure AD Connect
Die Auswahl der Expresseinstellungen ist die Standardoption und eines der häufigsten Szenarios. Auf diese Weise stellt Azure AD Connect die Synchronisierung mit der Kennwort-Synchronisierungsoption bereit. Dies gilt nur für eine einzige Gesamtstruktur und ermöglicht den Benutzern die Verwendung ihres lokalen Kennworts beim Anmelden in der Cloud. Bei Verwendung der Expresseinstellungen wird nach Abschluss der Installation automatisch eine Synchronisierung gestartet (dies können Sie jedoch auch deaktivieren). Mit dieser Option können Sie mit nur wenigen Klicks Ihr lokales Verzeichnis auf die Cloud erweitern.

![Willkommen bei Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)

### So installieren Sie Azure AD Connect mit Expresseinstellungen

1. Melden Sie sich als lokaler Administrator an dem Server an, auf dem Sie Azure AD Connect installieren möchten.  Dies sollte der Server sein, der als Synchronisierungsserver verwendet werden soll.
2. Navigieren Sie zu AzureADConnect.msi und doppelklicken Sie darauf
3. Klicken Sie auf der Willkommensseite aktivieren Sie das zustimmen zu den Lizenzbedingungen, und klicken Sie auf **Weiter**.
4. Klicken Sie auf dem Bildschirm "Express-Einstellungen" auf **express Einstellungen verwenden**.
![Willkommen bei Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)
5. Geben Sie im Bildschirm "Mit Azure AD verbinden" den Benutzernamen und das Kennwort eines globalen Azure-Administrators für Azure AD ein. Klicken Sie auf **Weiter**.
6. Geben Sie im Bildschirm "Mit AD DS verbinden" den Benutzernamen und das Kennwort für ein Enterprise-Administratorkonto ein.  Klicken Sie auf **Weiter**.
![Willkommen bei Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)
7. Klicken Sie auf "bereit zur Bildschirm konfigurieren" auf **Installieren**.
    - Optional können Sie auf der Seite bereit zum Konfigurieren, deaktivieren Sie die "**den Synchronisierungsvorgang starten, sobald die Konfiguration abgeschlossen ist**" das Kontrollkästchen.  Der Assistent konfiguriert dann zwar die Synchronisierung, die Aufgabe bleibt jedoch deaktiviert, sodass sie erst ausgeführt wird, wenn Sie sie in der Aufgabenplanung manuell aktivieren.  Sobald die Aufgabe aktiviert ist, wird die Synchronisierung alle drei Stunden ausgeführt.
    - Sie können optional auch auswählen, Konfiguration von Synchronisierungsdiensten für **Exchange-hybridbereitstellung** durch Aktivieren des entsprechenden Kontrollkästchens.  Wenn Sie Exchange-Postfächer nicht gleichzeitig lokal und in der Cloud bereitstellen möchten, müssen Sie diese Option nicht aktivieren.
![Willkommen bei Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)<br>
8. Klicken Sie nach Abschluss der Installation auf **Beenden**.
9. Melden Sie sich nach Abschluss der Installation ab und wieder an, bevor Sie den Synchronisierungsdienst-Manager oder den Synchronisierungsregel-Editor verwenden.

<br>
<br>

Im Folgenden können Sie sich ein Video zur Expressinstallation ansehen:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>


## Nächste Schritte
Nachdem Sie Azure AD Connect installiert haben können Sie [Überprüfen Sie die Installation und Zuweisen von Lizenzen](active-directory-aadconnect-whats-next.md).

Erfahren Sie mehr über [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).



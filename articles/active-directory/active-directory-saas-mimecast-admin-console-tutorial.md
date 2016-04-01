<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Mimecast Admin Console | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Mimecast Admin Console mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit Mimecast Admin Console
  
In diesem Tutorial wird die Integration von Azure und Mimecast Admin Console erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Mimecast Admin Console-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Mimecast Admin Console zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Mimecast Admin Console-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Mimecast Admin Console
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")
##Aktivieren der Anwendungsintegration für Mimecast Admin Console
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Mimecast Admin Console aktivieren.

###So aktivieren Sie die Anwendungsintegration für Mimecast Admin Console

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Mimecast Admin Console**.

    ![Anwendungskatalog](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Mimecast Admin Console**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Mimecast Admin Console zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Mimecast Admin Console** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configure Single Sign-On")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei Mimecast Admin Console** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Mimecast Admin Console-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer Anwendung Mimecast Admin Console anmelden (z. B.: "https://webmail-uk.mimecast.com" oder "https://webmail-us.mimecast.com"), und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE] Die Anmelde-URL ist regionsspezifisch.

    ![App-URL konfigurieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für Mimecast Admin Console** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei Mimecast Admin Console als Administrator an.

6.  Wechseln Sie zu **Services \ > Anwendung**.

    ![Dienste](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")

7.  Klicken Sie auf **Authentifizierungsprofile**.

    ![Authentifizierungsprofile](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles")

8.  Klicken Sie auf **Neues Authentifizierungsprofil**.

    ![Neue Authentifizierungsprofile](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profiles")

9.  In der **Authentifizierungsprofil** führen die folgenden Schritte aus:

    ![Authentifizierungsprofil](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile")

    1.  In der **Beschreibung** Textfeld Geben Sie einen Namen für Ihre Konfiguration.
    2.  Wählen Sie **Enforce SAML Authentication for Mimecast Admin Console**.
    3.  Als **Anbieter**, Option **Azure Active Directory**.
    4.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für Mimecast Admin Console** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller-URL** Textfeld.
    5.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für Mimecast Admin Console** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    6.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für Mimecast Admin Console** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.  

        >[AZURE.NOTE]Anmelde-URL und den Abmelde-URL sind für Mimecast Admin Console identisch.

    7.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    8.  Öffnen Sie das Base64-codierte Zertifikat in Editor, entfernen Sie die erste Zeile ("*–*") und die letzte Zeile ("*–*"), den restlichen Inhalt in die Zwischenablage kopieren und fügen Sie ihn an die **Identity Provider Certificate (Metadata)** Textfeld.
    9.  Wählen Sie **Allow Single Sign On**.
    10. Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Mimecast Admin Console anmelden können, müssen Sie in Mimecast Personal Portal bereitgestellt werden.  
Im Fall von Mimecast Admin Console ist die Bereitstellung eine manuelle Aufgabe.
  
Sie müssen eine Domäne registrieren, bevor Sie Benutzer erstellen können.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich auf Ihre **Mimecast Admin Console** als Administrator.

2.  Wechseln Sie zu **Verzeichnisse \ > interne**.

    ![Verzeichnisse](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories")

3.  Klicken Sie auf **Registrieren einer neuen Domäne**.

    ![Neue Domäne registrieren](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain")

4.  Nachdem die neue Domäne erstellt wurde, klicken Sie auf **neue Adresse**.

    ![Neue Adresse](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address")

5.  Führen Sie im Dialogfeld „Neue Adresse“ die folgenden Schritte aus:

    ![Speichern](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")

    1.  Typ der **e-Mail-Adresse**, **globalen Namen**, **Kennwort** und **Kennwort bestätigen** Attribute eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Sie können eine beliebige andere Mimecast Admin Console-Benutzerkonten oder APIs von Mimecast Admin Console Bereitstellung AAD-Benutzerkonten verwenden.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Mimecast Admin Console Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Mimecast Admin Console ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


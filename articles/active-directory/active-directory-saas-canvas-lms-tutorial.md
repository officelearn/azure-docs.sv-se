<properties
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Canvas LMS | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Canvas LMS mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Canvas LMS

In diesem Lernprogramm wird die Integration von Azure und Canvas erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Canvas-Mandanten

Nach Abschluss dieses Lernprogramms Canvas zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Canvas-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Canvas
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")
##Aktivieren der Anwendungsintegration für Canvas

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Canvas aktivieren.

###So aktivieren Sie die Anwendungsintegration für Canvas

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Canvas**.

    ![Anwendungskatalog](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Canvas**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Canvas zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Canvas müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Canvas** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Canvas anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Canvas-Anmelde-URL** Textfeld Geben Sie die URL mithilfe des folgenden Musters `https://<tenant-name>.instructure.com`, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Canvas konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Canvas-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Kurse \ > verwaltete Konten \ > Microsoft**.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

7.  Wählen Sie im Navigationsbereich auf der linken Seite **Authentifizierung**, und klicken Sie dann auf **neue SAML-Konfiguration hinzufügen**.

    ![Authentifizierung](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")

8.  Führen Sie auf der Seite "Current Integration" die folgenden Schritte aus.

    ![Aktuelle Integration](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Canvas konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **IdP-Entitäts-ID** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Canvas konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Anmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Canvas konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Canvas konfigurieren** Kopieren der **Kennwort-URL ändern** -Wert aus, und fügen Sie ihn in die **Link zum Ändern des Kennworts** Textfeld.
    5.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    6.  Aus der **Anmeldeattribut** Liste **NameID**.
    7.  Aus der **Bezeichnerformat** Liste **EmailAddress**.
    8.  Klicken Sie auf **Authentifizierungseinstellungen speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Canvas anmelden können, müssen sie in Canvas bereitgestellt werden.  
Im Fall von Canvas ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Canvas** Mandanten.

2.  Wechseln Sie zu **Kurse \ > verwaltete Konten \ > Microsoft**.

    ![Zeichenbereich](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

3.  Klicken Sie auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")

4.  Klicken Sie auf **Hinzufügen neuen Benutzer**.

    ![Benutzer](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")

5.  Führen Sie auf der Dialogseite "Add a New User" die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")

    1.  In der **Vollständiger Name** Textfeld Geben Sie den Namen des Benutzers.
    2.  In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse des Benutzers.
    3.  In der **Anmeldung** Textfeld Geben Sie Azure AD-e-Mail-Adresse des Benutzers.
    4.  Wählen Sie **E-Mail den Benutzer zum Erstellen von diesem Konto**.
    5.  Klicken Sie auf **Benutzer hinzufügen**.

>[AZURE.NOTE] Können Sie alle anderen Canvas Benutzerkonten oder APIs von Canvas Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Canvas Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Bereich ** AHA klicken Sie auf **Zuweisen von Benutzern**.

    ![Zuweisen von Benutzern](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



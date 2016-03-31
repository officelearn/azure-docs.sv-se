<properties 
    pageTitle="Lernprogramm: Azure AD-Integration mit BlueJeans | Microsoft Azure" 
    description="Erfahren Sie, wie Sie BlueJeans mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure AD-Integration mit BlueJeans

In diesem Lernprogramm wird die Integration von Azure und BlueJeans erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein BlueJeans-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms BlueJeans zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer BlueJeans-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für BlueJeans
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")
##Aktivieren der Anwendungsintegration für BlueJeans

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für BlueJeans aktivieren.

###So aktivieren Sie die Anwendungsintegration für BlueJeans

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **BlueJeans**.

    ![Anwendungskatalog](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **BlueJeans**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei BlueJeans zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **BlueJeans** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei BlueJeans** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **BlueJeans Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.BlueJeans.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei BlueJeans konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")

5.  Melden Sie sich in einem Webbrowserfenster Ihre **BlueJeans** -Unternehmenswebsite als Administrator.

6.  Wechseln Sie zu **ADMIN \ > Group Settings \ > Sicherheit**.

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  In der **Sicherheit** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden für SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")

    1.  Wählen Sie **SAML Single Sign On**.
    2.  Wählen Sie **Automatische Bereitstellung aktivieren**.

8.  Führen Sie die folgenden Schritte aus:

    ![Zertifikatpfad](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")

    1.  Klicken Sie auf **Choose File**, und klicken Sie dann das heruntergeladene Zertifikat hochzuladen.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei BlueJeans konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei BlueJeans konfigurieren** Kopieren der **Kennwort-URL ändern** -Wert aus, und fügen Sie ihn in die **Kennworts** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei BlueJeans konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.

9.  Führen Sie die folgenden Schritte aus:

    ![Änderungen speichern](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")

    1.  In der **Benutzer-Id** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  In der **E-Mail** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei BlueJeans anmelden können, müssen sie in BlueJeans bereitgestellt werden.  
Im Fall von BlueJeans ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **BlueJeans** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **ADMIN \ > Manage Users \ > Benutzer hinzufügen**.

    ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")

    >[AZURE.IMPORTANT] Die **Add User** Registerkarte ist nur verfügbar wenn ist, in der **Registerkarte "Sicherheit"**, **Automatische Bereitstellung aktivieren** deaktiviert ist.

3.  In der **Benutzer hinzufügen** führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")

    1.  Geben Sie einen **BlueJeans-Benutzernamen**, ein **e-Mail-Adresse**,  **BlueJeans-Besprechungs-ID**,  **Moderatorenkennwort**, ein **Vollständiger Name**, die **Unternehmen** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Benutzer hinzufügen**.

>[AZURE.NOTE] Können Sie alle anderen BlueJeans Benutzerkonten oder APIs von BlueJeans Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie BlueJeans Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** BlueJeans ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



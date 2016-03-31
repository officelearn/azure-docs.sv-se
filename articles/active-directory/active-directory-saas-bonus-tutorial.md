<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Bonus.ly | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Bonus.ly mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Bonus.ly

In diesem Lernprogramm wird die Integration von Azure und Bonus.ly veranschaulicht. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Testkonto bei Bonus.ly

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Bonus.ly
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")
##Aktivieren der Anwendungsintegration für Bonus.ly

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Bonus.ly aktivieren.

###So aktivieren Sie die Anwendungsintegration für Bonus.ly

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Bonus.ly**.

    ![Anwendungskatalog](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Bonus.ly**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Bonus.ly zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Bonus.ly müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Bonus.ly** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Bonus.ly anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Bonus.ly-Mandanten-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Bonus.ly*", und klicken Sie dann auf **Weiter**: 

    ![App-URL konfigurieren](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")

4.  Auf der **einmaliges Anmelden bei Bonus.ly konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Bonusly.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")

5.  In einem anderen Browser-Fenster, melden Sie sich bei Ihrem **Bonus.ly** Mandanten.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**, und wählen Sie dann **Integrationen und apps**.

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Unter **Single Sign-On**, Option **SAML**.

8.  Auf der **SAML** Dialogfeld führen die folgenden Schritte aus:

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Bonus.ly konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP SSO Target URL** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Bonus.ly konfigurieren** Kopieren der **Aussteller-ID** -Wert aus, und fügen Sie ihn in die **IdP-Aussteller** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Bonus.ly konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP-Anmelde-URL** Textfeld.
    4.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Cert Fingerprint** Textfeld.

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

9.  Klicken Sie auf **Speichern**.

10. Klicken Sie im Microsoft Azure AD-Portal konfigurationsbestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Bonus.ly anmelden können, müssen sie in Bonus.ly bereitgestellt werden.  
Im Fall von Bonus.ly ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich in einem Browserfenster mit Ihrem Bonus.ly-Mandanten an.

2.  Klicken Sie auf **Einstellungen**

    ![Einstellungen](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")

3.  Klicken Sie auf die **Benutzer und Boni** Registerkarte.

    ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")

4.  Klicken Sie auf **Verwalten von Benutzern**.

    ![Benutzer verwalten](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")

5.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")

6.  Auf der **Add User** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer hinzufügen](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")

    1.  Typ der "**E-Mail**, **First Name**, **Nachname**" eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE] Der AAD-Kontoinhaber erhält eine e-Mail, die einen zum Bestätigen des Kontos Link, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Bonus.ly Benutzerkonten oder APIs von Bonus.ly Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Bonus.ly Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der anwendungsintegrationsseite für Bonus.ly auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



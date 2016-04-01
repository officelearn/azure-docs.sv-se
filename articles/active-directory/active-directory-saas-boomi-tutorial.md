<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Boomi | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Boomi mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Lernprogramm: Azure Active Directory-Integration mit Boomi

In diesem Lernprogramm wird die Integration von Azure und Boomi erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Boomi-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Boomi zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Boomi-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Boomi
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")
##Aktivieren der Anwendungsintegration für Boomi

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Boomi aktivieren.

###So aktivieren Sie die Anwendungsintegration für Boomi

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Boomi**.

    ![Anwendungskatalog](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Boomi**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Boomi zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Boomi** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Boomi anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Boomi-Antwort-URL** geben Ihre **Boomi AtomSphere-Anmelde-URL** (z. B.: "*https://platform.boomi.com/sso/AccountName/saml*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Boomi** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Boomi-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf den Namen Ihres Unternehmens, und klicken Sie dann **Setup**.

    ![Einrichtung](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")

7.  Klicken Sie auf **SSO-Optionen**.

    ![SSO Options](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")

8.  In der **Optionen für einmaliges Anmelden** führen die folgenden Schritte aus:

    ![Single Sign-On Options](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")

    1.  Wählen Sie **Enable SAML-Single-Sign-On**.
    2.  Klicken Sie auf **Import**, um das heruntergeladene Zertifikat hochzuladen.
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Boomi** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Textfeld.
    4.  Als **Speicherort der Verbund-Id**, Option **Verbund-Id befindet sich im NameID-Element des Betreffs**.
    5.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Boomi anmelden können, müssen sie in Boomi bereitgestellt werden.  
Im Fall von Boomi ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Boomi** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **User Management \ > Benutzer**.

    ![Benutzer](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")

3.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")

4.  Auf der **Benutzerrollen hinzufügen** Dialogfeld führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")

    1.  Typ der **Vorname**, **Nachname** und **E-Mail** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf OK.

>[AZURE.NOTE] Können Sie alle anderen Boomi Benutzerkonten oder APIs von Boomi Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Boomi Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Boomi ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



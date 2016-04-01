<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Syncplicity | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Syncplicity mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Syncplicity
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden zwischen Azure Active Directory (Azure AD) und Syncplicity eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Syncplicity-Mandant
  
Nach Abschluss dieses Tutorials können sich die Syncplicity zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Syncplicity-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen.

1.  Aktivieren der Anwendungsintegration für Syncplicity
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scenario")

##Aktivieren der Anwendungsintegration für Syncplicity
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Syncplicity aktivieren.

###So aktivieren Sie die Anwendungsintegration für Syncplicity:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Syncplicity**.

    ![Syncplicity-Anwendungskatalog](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity application gallery")

7.  Wählen Sie im Ergebnisbereich **Syncplicity**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Syncplicity zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Syncplicity** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Syncplicity anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Syncplicity-Anmelde-URL** Textfeld die URL-Benutzer zum Anmelden bei Ihrer Syncplicity-Anwendung verwenden und klicken Sie auf **Weiter**. 

    Die app-URL ist Ihre Syncplicity-Mandanten-URL (z. B.: *http://company.Syncplicity.com*):

    ![App-URL konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configure app URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für syncplicity** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatdatei dann lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configure single sign-on")

5.  Melden Sie sich bei Ihrem **Syncplicity** Mandanten.

6.  Klicken Sie im Menü am oberen Rand **Admin**, Option **Einstellungen**, und klicken Sie dann auf **benutzerdefinierte Domäne und einmaliges Anmelden für**.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  Auf der **einmaliges Anmelden (SSO)** Dialogfeld führen die folgenden Schritte aus:

    ![Single Sign-On \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \(SSO\)")

    1.  In der **benutzerdefinierte Domäne** Textfeld Geben Sie den Namen Ihrer Domäne.
    2.  Wählen Sie **aktiviert** als **Single Sign-On Status**.
    3.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für syncplicity** Seite, und kopieren Sie die **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **Entitäts-Id** Textfeld.
    4.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für syncplicity** Seite, und kopieren Sie die **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Anmelden Seiten-URL** Textfeld.
    5.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für syncplicity** Seite, und kopieren Sie die **Abmelde-URL** -Wert aus, und fügen Sie ihn in die **URL der Abmeldeseite** Textfeld.
    6.  In **Identitätsanbieterzertifikat**, klicken Sie auf **Choose File**, Zertifikat, und klicken Sie dann laden Sie aus Microsoft Azure-Portal heruntergeladen haben.
    7.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Bestätigung](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der Syncplicity-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Syncplicity AAD-Benutzerkonten erstellt werden.

###Führen Sie zum Bereitstellen von Benutzerkonten in Syncplicity die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **Syncplicity** Mandanten (z. B.: *https://company.Syncplicity.com*).

2.  Klicken Sie auf **Admin** und wählen Sie **Benutzerkonten**.

3.  Klicken Sie auf **Hinzufügen eines Benutzers**.

    ![Benutzer verwalten](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Manage Users")

4.  Typ der **e-Mail-Adresse** eines AAD-Kontos, das Sie bereitstellen möchten, wählen Sie **Benutzer** als **Rolle**, und klicken Sie dann auf **Weiter**.

    ![Azure-Kontoinformationen](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Account Information")

    >[AZURE.NOTE] Der AAD-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen und aktivieren das Konto.

5.  Wählen Sie eine Gruppe in Ihrem Unternehmen, die Ihre neue Benutzer ein Mitglied, und klicken Sie dann auf **Weiter**.

    ![Gruppenmitgliedschaft](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Group Membership")

    >[AZURE.NOTE] Klicken Sie auf, wenn keine Gruppen vorhanden, aufgelistet werden, nur sind **Weiter**.

6.  Wählen Sie die Ordner, auf dem Computer des Benutzers von Syncplicity steuern, und klicken Sie dann auf sollen **Weiter**.

    ![Syncplicity-Ordner](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity Folders")

>[AZURE.NOTE] Können Sie alle anderen Syncplicity Benutzerkonten oder APIs von Syncplicity Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Syncplicity Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Syncplicity** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).




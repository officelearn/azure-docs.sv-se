<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ThousandEyes | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ThousandEyes mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit ThousandEyes
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory (Azure AD) und ThousandEyes eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ThousandEyes-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die AAD-Benutzer, denen Sie den Zugriff auf ThousandEyes zugewiesen haben, mittels einmaliger Anmeldung auf der ThousandEyes-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe „Einführung in den AAD-Zugriffsbereich“).

1.  Aktivieren der Anwendungsintegration für ThousandEyes
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

##Aktivieren der Anwendungsintegration für ThousandEyes
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ThousandEyes aktivieren.

###So aktivieren Sie die Anwendungsintegration für ThousandEyes:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ThousandEyes**.

    ![Anwendungskatalog](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **ThousandEyes**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei ThousandEyes zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ThousandEyes** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei ThousandEyes** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")

3.  Auf der **App-URL konfigurieren** auf der Seite der **ThousandEyes-Anmelde-URL** Textfeld die URL-Benutzer zum Anmelden bei Ihrer ThousandEyes-Anwendung verwenden (z. B.: "*https://app.thousandeyes.com/login/sso*"), und klicken Sie dann auf **Weiter**. 

    ![App-URL konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um ThousandEyes** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatdatei dann lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")

5.  Melden Sie sich in einem Webbrowserfenster auf Ihrer **ThousandEyes** -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7.  Klicken Sie auf **Konto**

    ![Konto](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

8.  Klicken Sie auf die **Sicherheit und Authentifizierung** Registerkarte.

    ![Sicherheit und Authentifizierung](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")

9.  In der **Setup Single Sign-On** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden einrichten](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")

    1.  Wählen Sie **Enable Single Sign-On**.
    2.  Im Microsoft Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ThousandEyes** Seite, und kopieren Sie die **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **URL für die Anmeldeseite** Textfeld.
    3.  Im Microsoft Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ThousandEyes** Seite, und kopieren Sie die **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **URL der Abmeldeseite** Textfeld.
    4.  Im Microsoft Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ThousandEyes** Seite, und kopieren Sie die **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter Aussteller** Textfeld.
    5.  In **Identitätsanbieterzertifikat**, klicken Sie auf **Choose File**, Zertifikat, und klicken Sie dann laden Sie aus Microsoft Azure-Portal heruntergeladen haben.
    6.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei ThousandEyes anmelden können, müssen sie in ThousandEyes bereitgestellt werden.  
Im Fall von ThousandEyes ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in ThousandEyes die folgenden Schritte aus:

1.  Melden Sie sich bei der ThousandEyes-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3.  Klicken Sie auf **Konto**.

    ![Konto](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4.  Klicken Sie auf die **Konten und Benutzer** Registerkarte.

    ![Konten und Benutzer](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")

5.  In der **Hinzufügen von Benutzern und Konten** führen die folgenden Schritte aus:

    ![Benutzerkonten hinzufügen](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")

    1.  Typ der **Namen**, **E-Mail** und andere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Hinzufügen neuen Benutzer zu Konto**.

        >[AZURE.NOTE] Der AAD-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen und aktivieren das Konto.

>[AZURE.NOTE] Können Sie alle anderen ThousandEyes Benutzerkonten oder APIs von ThousandEyes Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ThousandEyes Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **ThousandEyes** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



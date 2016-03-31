<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SpringCM | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Spring CM mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit SpringCM
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory und SpringCM eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SpringCM-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure Active Directory-Benutzer, die Sie SpringCM zugewiesen haben, mittels einmaliger Anmeldung über den AAD-Zugriffsbereich anmelden.

1.  Aktivieren der Anwendungsintegration für SpringCM
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario")

##Aktivieren der Anwendungsintegration für SpringCM
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SpringCM aktivieren.

###So aktivieren Sie die Anwendungsintegration für SpringCM:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **SpringCM**.

    ![Anwendungskatalog](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **SpringCM**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei SpringCM zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SpringCM** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configure single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei SpringCM anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configure single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **SpringCM-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern, melden Sie sich auf Ihre SpringCM-Anwendung, und klicken Sie dann auf **Weiter**. 

    Die app-URL ist Ihre SpringCM-Mandanten-URL (z. B.: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![App-URL konfigurieren](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei SpringCM** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatdatei dann lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configure Single SignOn")

5.  Melden Sie sich in einem Webbrowserfenster auf Ihrer **SpringCM** -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **GEHE zu**, klicken Sie auf **Voreinstellungen**, und klicken Sie dann auf die **Kontoeinstellungen** auf **SAML SSO**.

    ![SAML-SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  Führen Sie im Abschnitt „Identitätsanbieterkonfiguration“ die folgenden Schritte aus:

    ![Identitätsanbieterkonfiguration](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Identity Provider Configuration")

    1.  Um Ihre heruntergeladene Azure Active Directory-Zertifikat hochzuladen, klicken Sie auf **Select Issuer Certificate** oder **Change Issuer Certificate**.
    2.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei SpringCM** Seite, und kopieren Sie die **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    3.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei SpringCM** Seite, und kopieren Sie die **einmaliges Anmelden für Dienst-URL** -Wert aus, und fügen Sie ihn in die **Service Provider (SP) initiierter Endpunkt** Textfeld.
    4.  Als **SAML aktiviert**, Option **aktivieren**.
    5.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configure Single SignOn")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure Active Directory-Benutzer bei SpringCM anmelden können, müssen sie in SpringCM bereitgestellt werden.  
Im Fall von SpringCM ist die Bereitstellung eine manuelle Aufgabe.

>[AZURE.NOTE] Weitere Informationen finden Sie unter [Erstellen und Bearbeiten von Benutzern SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###Führen Sie zum Bereitstellen von Benutzerkonten in SpringCM die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **SpringCM** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **GOTO**, und klicken Sie dann auf **Adressbuch**.

    ![Benutzer erstellen](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Create User")

3.  Klicken Sie auf **erstellen Benutzer**.

4.  Wählen Sie eine **Benutzerrolle**.

5.  Wählen Sie **Send Activation Email**.

6.  Geben Sie die erforderlichen Angaben in die Textfelder „Vorname“, „Nachname“ und „E-Mail-Adresse“ eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.

7.  Fügen Sie den Benutzer zu einer **Sicherheitsgruppe**.

8.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen SpringCM Benutzerkonten oder APIs von SpringCM Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SpringCM Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **SpringCM** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).







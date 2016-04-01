<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Replicon | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Replicon mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Replicon
  
In diesem Tutorial wird die Integration von Azure und Replicon erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Replicon-Mandant
  
Nach Abschluss dieses Lernprogramms Replicon zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Replicon-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Replicon
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")
##Aktivieren der Anwendungsintegration für Replicon
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Replicon aktivieren.

###So aktivieren Sie die Anwendungsintegration für Replicon:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-replicon-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-replicon-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-replicon-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Replicon**.

    ![Anwendungskatalog](./media/active-directory-saas-replicon-tutorial/IC777799.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Replicon**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Replicon zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Replicon** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Replicon anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configure app URL")

    1.  In der **Replicon-Anmelde-URL** Textfeld Geben Sie Ihre Replicon-Mandanten-URL (z. B.: *https://na2.replicon.com/company/saml2/sp-sso/post*).
    2.  In der **Replicon-Antwort-URL** Textfeld Ihre replicon- **AssertionConsumerService** URL (z. B.: *https://global.replicon.com/! / saml2/Unternehmen/Sso/Post*).  

        >[AZURE.NOTE] Sie können die URL aus den Replicon-Metadaten abrufen:
        **https://Global.replicon.com/! / saml2/\< YourCompanyKey\ >**.

    3.  Klicken Sie auf **Weiter**

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für replicon** Seite, um Ihre Metadaten herunterzuladen, klicken Sie **Metadaten**, und speichern Sie die Metadaten dann auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

6.  Um SAML 2.0 zu konfigurieren, führen Sie die folgenden Schritte aus:

    ![Klicken Sie auf „SAML-Authentifizierung aktivieren“.](./media/active-directory-saas-replicon-tutorial/IC777805.png "Enable SAML authentication")

    1.  Zum Anzeigen der **EnableSAML Authentication2** Dialogfeld Schlüssel Ihres Unternehmens die folgende URL anhängen:  
        **/Services/SecurityService1.svc/Help/Test/EnableSAMLAuthentication2**  
        Das folgende Beispiel zeigt die vollständige URL-Schema:  
        **https://Na2.replicon.com/ \<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  Klicken Sie auf die **+** zum Erweitern der **v20Configuration** Abschnitt.
    3.  Klicken Sie auf die **+** zum Erweitern der **MetaDataConfiguration** Abschnitt.
    4.  Klicken Sie auf **Choose File**, um Ihre Identität Anbieter Metadaten-XML-Datei auswählen, und klicken Sie auf **Senden**.

7.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Replicon anmelden können, müssen sie in Replicon bereitgestellt werden.  
Im Fall von Replicon ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich in einem Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Verwaltung \ > Benutzer**.

    ![Benutzer](./media/active-directory-saas-replicon-tutorial/IC777806.png "Users")

3.  Klicken Sie auf **+ Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-replicon-tutorial/IC777807.png "Add User")

4.  In der **Benutzerprofil** führen die folgenden Schritte aus:

    ![Benutzerprofil](./media/active-directory-saas-replicon-tutorial/IC777808.png "User profile")

    1.  In der **Anmeldename** Textfeld Typ des Azure AD e-Mail-Adresse der Azure AD-Benutzer, die Sie bereitstellen möchten.
    2.  Als **Authentifizierungstyp**, Option **SSO**.
    3.  In der **Abteilung** Textfeld Geben Sie die Abteilung des Benutzers.
    4.  Als **Mitarbeitertyp**, Option **Administrator**.
    5.  Klicken Sie auf **Benutzerprofil speichern**.

>[AZURE.NOTE]Können Sie alle anderen Replicon Benutzerkonten oder APIs von Replicon Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Replicon Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Replicon ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-replicon-tutorial/IC777809.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-replicon-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


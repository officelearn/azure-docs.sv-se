<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zscaler | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zscaler mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zscaler
  
In diesem Tutorial wird die Integration von Azure und Zscaler erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Mandant in Zscaler
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zscaler
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren von Proxyeinstellungen
4.  Konfigurieren der Benutzerbereitstellung
5.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

##Aktivieren der Anwendungsintegration für Zscaler
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zscaler aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zscaler:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Zscaler**.

    ![Anwendungskatalog](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Zscaler**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zscaler zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Zertifikat auf Zscaler hochladen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Zscaler** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer bei zscaler anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Zscaler-Anmelde-URL** Textfeld Geben Sie Ihren Benutzernamen in der URL, die Sie von Zscaler erhalten haben, und klicken Sie dann auf **Weiter**: 

    >[AZURE.NOTE] Wenden Sie sich an das Supportteam von Zscaler, wenn Sie Ihre Anmelde-URL nicht kennen.

    ![App-URL konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für zscaler** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")

    1.  Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Zscaler.cer**.
    2.  Kopieren der **Authentifizierungsanforderungs-URL** in die Zwischenablage.

5.  Melden Sie sich bei Ihrem Zscaler-Mandanten an.

6.  Klicken Sie im Menü am oberen Rand **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7.  Unter **verwalten Administratoren und Rollen**, klicken Sie auf **Verwalten Benutzer und Authentifizierung**.

    ![Administratoren und Rollen verwalten](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8.  In der **Choose Authentication Option für Ihre Organisation** führen die folgenden Schritte aus:

    ![Authentifizierungsoptionen wählen](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")

    1.  Wählen Sie **Authentifizieren mit SAML Single Sign-On**.
    2.  Klicken Sie auf **Konfigurieren Sie einmaliges Anmelden für Parameter SAML**.

9.  Auf der **SAML einmaliges Anmelden für Parameter konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Fertig**:

    ![Hochladen des Zertifikats](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")

    1.  In der **URL des SAML-Portals, an die Benutzer, für die Authentifizierung gesendet werden** Textbox, fügen Sie den Wert von der **Authentifizierungsanforderungs-URL** Feld aus dem Azure-Portal.
    2.  In der **Attribute containing Login Name** Textfeld **NameID**.
    3.  In der **Öffentliche SSL-Zertifikat hochladen** Feld, das Hochladen des Zertifikats, das Sie aus dem Azure-Portal heruntergeladen haben.
    4.  Wählen Sie **Automatische SAML-Bereitstellung aktivieren**.

10. Auf der **Benutzerauthentifizierung konfigurieren** Dialogfeld führen die folgenden Schritte aus:

    ![Benutzerauthentifizierung wählen](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")

    1.  Klicken Sie auf **Speichern**.
    2.  Klicken Sie auf **jetzt aktivieren**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")

##Konfigurieren von Proxyeinstellungen

###So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1.  Starten Sie **InternetExplorer**.

2.  Wählen Sie **Internetoptionen** aus der **Tools** Öffnen das Kontextmenü der **Internetoptionen** Dialogfeld.

    !["Internetoptionen",](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3.  Klicken Sie auf die **Verbindungen** Registerkarte.

    ![Verbindungen](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4.  Klicken Sie auf **LAN-Einstellungen** zum Öffnen der **LAN-Einstellungen** Dialogfeld.

5.  Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:

    ![Proxyserver](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")

    1.  Wählen Sie "Proxyserver für LAN verwenden" aus.
    2.  Geben Sie im Textfeld Adresse **gateway.zscalertwo.net**.
    3.  Geben Sie im Textfeld Port **80**.
    4.  Wählen Sie **Proxyserver für lokale Adressen umgehen**.
    5.  Klicken Sie auf **OK** Schließen die **Einstellungen Local Area Network (LAN)** Dialogfeld.

6.  Klicken Sie auf **OK** Schließen die **Internetoptionen** Dialogfeld.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Zscaler anmelden können, müssen sie in Zscaler bereitgestellt werden.  
Im Fall von Zscaler ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Zscaler** Mandanten.

2.  Klicken Sie auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3.  Klicken Sie auf **User Management**.

    ![Benutzerverwaltung](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4.  In der **Benutzer** auf **Hinzufügen**.

    ![Hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5.  Führen Sie im Abschnitt "Benutzer hinzufügen" die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")

    1.  Typ der **UserID**, **Anzeigename des Benutzers**, **Kennwort**, **Kennwort bestätigen**, und wählen Sie dann **Gruppen** und **Abteilung** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen Zscaler Benutzer Account Creation-Tool oder APIs von Zscaler Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zscaler Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Zscaler** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



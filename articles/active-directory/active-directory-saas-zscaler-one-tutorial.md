<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ZScaler One | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie ZScaler One mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zscaler One

In diesem Tutorial wird die Integration von Azure und Zscaler One erläutert.  
 Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:  

-   Ein gültiges Azure-Abonnement
-   Ein Zscaler One-Abonnement, für das einmaliges Anmelden aktiviert ist  

Nach Abschluss dieses Lernprogramms ZScaler One zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer ZScaler One-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).  

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:  

1.  Aktivieren der Anwendungsintegration für Zscaler One
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren von Proxyeinstellungen
4.  Konfigurieren der Benutzerbereitstellung
5.  Zuweisen von Benutzern  

![Szenario](./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Scenario")  

##Aktivieren der Anwendungsintegration für Zscaler One

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zscaler One aktivieren.  

###So aktivieren Sie die Anwendungsintegration für Zscaler One:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.  

    ![Active Directory](./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")  

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.  

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.  

    ![Anwendungen](./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Applications")  

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.  

    ![Anwendung hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Add application")  

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.  

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Add an application from gallerry")  

6.  In der **Suchfeld**, Typ **ZScaler One**.  

    ![Anwendungskatalog](./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Application Gallery")  

7.  Wählen Sie im Ergebnisbereich **ZScaler One**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.  

    ![ZScaler One](./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "ZScaler One")  

##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zscaler One zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Zscaler One-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)  

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ZScaler One** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.  

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Configure Single Sign-On")  

2.  Auf der **wie sollen sich Benutzer bei ZScaler One anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.  

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Configure Single Sign-On")  

3.  Auf der **App-URL konfigurieren** auf der Seite der **ZScaler eine Anmelde-URL** Textfeld Geben Sie die URL, die Ihre Benutzer für die Anmeldung bei Ihrem ZScaler One-Anwendung, und klicken Sie dann auf **Weiter**.  

    ![App-URL konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "Configure App URL")  

    >[AZURE.NOTE]Sie können den tatsächlichen Wert für Ihre Umgebung von Ihrem ZScaler One-Supportteam erhalten, wenn Sie ihn benötigen.  

4.  Auf der **einmaliges Anmelden konfigurieren, um ZScaler One** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.  

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Configure Single Sign-On")  

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zscaler One-Unternehmenswebsite als Administrator an.  

6.  Klicken Sie im Menü am oberen Rand **Verwaltung**.  

    ![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Administration")  

7.  Unter **verwalten Administratoren und Rollen**, klicken Sie auf **Benutzer verwalten und Authentifizierung**.  

    ![Benutzer & Authentifizierung verwalten](./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Manage Users & Authentication")  

8.  In der **Auswählen von Authentifizierungsoptionen für Ihr Unternehmen** führen die folgenden Schritte aus:  

    ![Authentifizierung](./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Authentication")  

    1.  Wählen Sie **Authentifizieren mit SAML Single Sign-On**.  
    2.  Klicken Sie auf **Konfigurieren Sie einmaliges Anmelden für Parameter SAML**.  

9.  Auf der **SAML einmaliges Anmelden für Parameter konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Fertig**:  

    ![Einmaliges Anmelden](./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Single Sign-On")  

    1.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ZScaler One** Kopieren der **Authentifizierungsanforderungs-URL** -Wert aus, und fügen Sie ihn in die **URL des SAML-Portals, an die Benutzer, für die Authentifizierung gesendet werden** Textfeld.  
    2.  In der **Attribute containing Login Name** Textfeld **NameID**.  
    3.  Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Zscaler Pem**.  
    4.  Wählen Sie **Automatische SAML-Bereitstellung aktivieren**.  

10. Auf der **Benutzerauthentifizierung konfigurieren** Dialogfeld führen die folgenden Schritte aus:  

    ![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Administration")  

    1.  Klicken Sie auf **Speichern**.  
    2.  Klicken Sie auf **jetzt aktivieren**.  

11. In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ZScaler One** Dialogfeld auf der Seite Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **vollständig**.  

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Configure Single Sign-On")  

##Konfigurieren von Proxyeinstellungen

###So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1.  Starten Sie **InternetExplorer**.  

2.  Wählen Sie **Internetoptionen** aus der **Tools** Öffnen das Kontextmenü der **Internetoptionen** Dialogfeld.  

    !["Internetoptionen",](./media/active-directory-saas-zscaler-one-tutorial/IC769492.png "Internet Options")  

3.  Klicken Sie auf die **Verbindungen** Registerkarte.  

    ![Verbindungen](./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Connections")  

4.  Klicken Sie auf **LAN-Einstellungen** zum Öffnen der **LAN-Einstellungen** Dialogfeld.  

5.  Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:  

    ![Proxyserver](./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Proxy server")  

    1.  Wählen Sie "Proxyserver für LAN verwenden" aus.  
    2.  Geben Sie im Textfeld Adresse **gateway.zscalerone.net**.  
    3.  Geben Sie im Textfeld Port **80**.  
    4.  Wählen Sie **Proxyserver für lokale Adressen umgehen**.  
    5.  Klicken Sie auf **OK** Schließen die **Einstellungen Local Area Network (LAN)** Dialogfeld.  

6.  Klicken Sie auf **OK** Schließen die **Internetoptionen** Dialogfeld.  

##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ZScaler One anmelden können, müssen sie in ZScaler One bereitgestellt werden.  
 Im Fall von ZScaler One ist die Bereitstellung eine manuelle Aufgabe.  

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Zscaler One** Mandanten.  

2.  Klicken Sie auf **Verwaltung**.  

    ![Verwaltung](./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Administration")  

3.  Klicken Sie auf **User Management**.  

    ![Hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Add")  

4.  In der **Benutzer** auf **Hinzufügen**.  

    ![Hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Add")  

5.  Führen Sie im Abschnitt "Benutzer hinzufügen" die folgenden Schritte aus:  

    ![Benutzer hinzufügen](./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Add User")  

    1.  Typ der **UserID**, **Anzeigename des Benutzers**, **Kennwort**, **Kennwort bestätigen**, und wählen Sie dann **Gruppen** und **Abteilung** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.  
    2.  Klicken Sie auf **Speichern**.  

>[AZURE.NOTE]Können Sie alle anderen ZScaler One Benutzerkonten oder APIs von ZScaler One Bereitstellung AAD-Benutzerkonten.  

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.  

###So weisen Sie ZScaler One Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.  

2.  Auf der **ZScaler One** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.  

    ![Benutzer zuweisen](./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Assign Users")  

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.  

    ![Ja](./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Yes")  

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).  



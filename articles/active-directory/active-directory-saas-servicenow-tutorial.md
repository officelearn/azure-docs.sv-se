<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ServiceNow | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ServiceNow mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="11/02/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit ServiceNow
  
In diesem Tutorial wird die Integration von Azure und ServiceNow erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in ServiceNow
  
Nach Abschluss dieses Lernprogramms ServiceNow zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer ServiceNow-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ServiceNow
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scenario")
##Aktivieren der Anwendungsintegration für ServiceNow
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ServiceNow aktivieren.

###So aktivieren Sie die Anwendungsintegration für ServiceNow

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ServiceNow**.

    ![Anwendungskatalog](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **ServiceNow**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ServiceNow zu authentifizieren.

Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat in Ihren Dropbox für Unternehmen-Mandanten hochladen. Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ServiceNow** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden für ServiceNow** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3.  Auf der **App-Einstellungen konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")

    a. in der **ServiceNow-Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei Ihrer ServiceNow-Anwendung (z. B.: *https://\<InstanceName\>.service-now.com*).

    b. In der **Aussteller-URL** Textfeld verwendete URL von Ihren Benutzern das Anmelden Ihrer ServiceNow-Anwendung (z. B.: *https://\<InstanceName\>.service-now.com*).

    c. Klicken Sie auf **Weiter**

4.  Auf der **automatisch konfigurieren Sie einmaliges Anmelden für** auf **manuell konfigurieren der Anwendung für einmaliges Anmelden für**, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")



4.  Auf der **einmaliges Anmelden für ServiceNow konfigurieren** auf **Zertifikat herunterladen**, speichern Sie die Datei lokal auf Ihrem Computer, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Eigenschaften**.  

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configure app URL")


3. Auf der **mehrere Eigenschaften des SSO-** im Dialogfeld die folgenden Schritte ausführen:

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")

    a. Als **Aktivieren mehrerer Anbieter SSO**, Option **Ja**.

    b. Als **Debugprotokollierung haben Sie mehrere Anbieter SSO-Integration aktivieren**, Option **Ja**.

    c. In **Feld für den Benutzer Tabelle mit...** Textfeld **User_name**.

    d. Klicken Sie auf **Speichern**.



1. Klicken Sie im Navigationsbereich auf der linken Seite auf **X509 Zertifikate**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")


1. Auf der **x. 509-Zertifikate** Dialogfeld klicken Sie auf **Neu**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")


1. Auf der **x. 509-Zertifikate** im Dialogfeld die folgenden Schritte ausführen:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")

    a. Klicken Sie auf **neue**.

    b. In den **Namen** Geben einen Namen für die Konfiguration (z. B.: **TestSAML2.0**).

    c. Wählen Sie **Active**.

    d. Als **Format**, Option **PEM**.

    e. Als **Typ**, Option **Vertrauenswürdigkeit Store Cert**.

    f. Erstellen Sie eine Base64-codierte Datei aus Ihrem heruntergeladene Zertifikat.
    > [AZURE.NOTE] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).
    
    g. Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **PEM-Zertifikat** Textfeld.

    h. Klicken Sie auf **Update**.


1. Klicken Sie im Navigationsbereich auf der linken Seite auf **Identitätsanbieter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")

1. Auf der **Identitätsanbieter** Dialogfeld klicken Sie auf **Neu**:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")


1. Auf der **Identitätsanbieter** Dialogfeld klicken Sie auf **SAML2 Update1?**:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")


1. Führen Sie im Dialogfeld „SAML2 Update1 Properties“ folgende Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")


    a. in the **Name** textbox, type a name for your configuration (e.g.: **SAML 2.0**).

    b. In the **User Field** textbox, type **email**. 

    c. In the Azure AD portal, copy the **Identity Provider ID** value, and then paste it into the **Identity Provider URL** textbox.

    d. In the Azure AD portal, copy the **Authentication Request URL** value, and then paste it into the **Identity Provider's AuthnRequest** textbox.

    e. In the Azure AD portal, copy the **Single Sign-Out Service URL** value, and then paste it into the **Identity Provider's SingleLogoutRequest** textbox.

    f. In the **ServiceNow Homepage** textbox, type the URL of your ServiceNow instance homepage.

    > [AZURE.NOTE] The ServiceNow instance homepage is a concatenation of your **ServieNow tenant URL** and **/navpage.do** (e.g.: *https://fabrikam.service-now.com/navpage.do*).
 

    g. In the **Entity ID / Issuer** textbox, type the URL of your ServiceNow tenant.

    h. In the **Audience URL** textbox, type the URL of your ServiceNow tenant. 

    i. In the **Protocol Binding for the IDP's SingleLogoutRequest** textbox, type **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. In the NameID Policy textbox, type **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Deselect **Create an AuthnContextClass**.

    l. In the **AuthnContextClassRef Method**, type **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. In **Clock Skew** textbox, type **60**.

    n. As **Single Sign On Script**, select **MultiSSO_SAML2_Update1**.

    o. As **x509 Certificate**, select the certificate you have created in the previous step.

    p. Click **Submit**. 



6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.
 
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")



##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für ServiceNow aktivieren.


### So konfigurieren Sie die Benutzerbereitstellung

1. In der Azure-Verwaltungsportal auf die **ServiceNow** anwendungsintegrationsseite klicken Sie auf **Benutzerbereitstellung**. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-servicenow-tutorial/IC769498.png "User provisioning")


2. Auf der **ServiceNow-Anmeldeinformationen zum Aktivieren automatischer benutzerbereitstellung eingeben** Seite, geben Sie die folgenden Konfigurationseinstellungen:
Benutzerbereitstellung konfigurieren 

     2.1. In der **ServiceNow-Instanzname** Textfeld Geben Sie den Namen des ServiceNow-Instanz.

     2.2. In der **ServiceNow-Administratorbenutzername** Textfeld Geben Sie den Namen des ServiceNow-Administratorkontos ein.

     2.3. In der **ServiceNow-Administratorkennwort** Textfeld Geben Sie das Kennwort für dieses Konto.

     2.4. Klicken Sie auf **überprüfen** Ihrer Konfiguration.

     2.5. Klicken Sie auf die **Weiter** Schaltfläche, um die **Nächste Schritte** Seite.

     2.6. Wenn Sie alle Benutzer für diese Anwendung bereitstellen möchten, wählen Sie "**automatisch alle Benutzerkonten im Verzeichnis für diese Anwendung bereitstellen**". <br><br> ![Nächste Schritte](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Next Steps")

     2.7. Auf der **Nächste Schritte** auf **abgeschlossen** um Ihre Konfiguration zu speichern.











##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ServiceNow Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ServiceNow ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


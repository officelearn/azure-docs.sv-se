<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Workday | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Workday mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Workday
  
In diesem Tutorial wird die Integration von Azure und Workday erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in Workday
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Workday
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Konfigurieren der Benutzerbereitstellung

![Szenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

##Aktivieren der Anwendungsintegration für Workday
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4.  Zum Öffnen der **Anwendungskatalog**, klicken Sie auf **App hinzufügen**, und klicken Sie dann auf **Hinzufügen einer Anwendung für meine Organisation**.

    ![Was möchten Sie tun?](./media/active-directory-saas-workday-tutorial/IC700995.png "What do you want to do?")

5.  In der **Suchfeld**, Typ **Workday**.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  Wählen Sie im Ergebnisbereich **Workday**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Workday zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Base64-codiertes Zertifikat erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  Auf der **Workday** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782920.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Workday anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782921.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-workday-tutorial/IC782957.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer bei Workday anmelden (z. B.: *https://impl.workday.com/ \<tenant\>/login-saml2.htmld*)
    2.  In der **Workday-Antwort-URL** Textfeld Geben Sie die Workday-Antwort-URL (z. B.: *https://impl.workday.com/ \<tenant\>/login-saml.htmld*).

        >[AZURE.NOTE] Ihre Antwort-URL müssen eine Unterdomäne (z. B.: Www, wd2, wd3, wd3-Impl, wd5, wd5-Impl). 
        >Beispielsweise "*http://www.myworkday.com*" funktioniert jedoch "*http://myworkday.com*" nicht. 
 
4.  Auf der **einmaliges Anmelden für Workday konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782922.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der Workday-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **im Menü \ > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Wechseln Sie zu **Kontoverwaltung**.

    ![Kontoverwaltung](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8.  Wechseln Sie zu **Edit Tenant Setup – Security**.

    ![Mandanten-Sicherheit bearbeiten](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9.  In der **Redirection URLs** führen die folgenden Schritte aus:

    ![Umleitungs-URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")

     9.1. Klicken Sie auf **Zeile hinzufügen**.

     9.2. In der **Login Redirect URL** Textbox und **Mobile Redirect URL** Geben die **Workday-Mandanten-URL** Sie eingegeben haben, auf die **App-URL konfigurieren** Azure-Portal auf der Seite.
    
     9.3. In Azure-Portal auf der **einmaliges Anmelden für Workday konfigurieren** Kopieren der **-Dienst-URL für einmalige Abmeldung**, und fügen Sie ihn in die **Logout Redirect URL** Textfeld.

     9.4.  In **Umgebung** Textfeld Geben Sie den Umgebungsnamen.  


       >[AZURE.NOTE] Der Wert des Attributs Umgebung ist auf den Wert der Mandanten-URL gebunden:
        >
        >-   Wenn der Domänenname der Workday-Mandanten URL mit "Impl" beginnt (z. B.: *https://impl.workday.com/ \<tenant\>/login-saml2.htmld*), wird die **Umgebung** -Attribut muss Implementierung festgelegt werden.
        >-   Wenn der Domänenname mit etwas anderem beginnt, müssen Sie Workday wenden, um den entsprechenden **Umgebung** Wert.

10. In der **SAML-Setup** führen die folgenden Schritte aus:

    ![SAML Setup](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")

     10.1.  Wählen Sie **SAML-Authentifizierung aktivieren**.

     10.2.  Klicken Sie auf **Zeile hinzufügen**.

11. Führen Sie im Abschnitt „SAML-Identitätsanbieter“ die folgenden Schritte aus:

    ![SAML-Identitätsanbieter](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")

     11.1.  Geben Sie im Textfeld Name des Identitätsanbieters einen Anbieternamen (z. B.: *SPInitiatedSSO*).

     11.2.  In Azure-Portal auf der **einmaliges Anmelden für Workday konfigurieren** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.

     11.3.  Wählen Sie **Aktivieren Workday Initialted Logout**.

     11.4. In Azure-Portal auf der **einmaliges Anmelden für Workday konfigurieren** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **Request-Abmelde-URL** Textfeld.


     11.3.  Click **Identity Provider Public Key Certificate**, and then click **Create**. <br><br>
        ![Create](./media/active-directory-saas-workday-tutorial/IC782928.png "Create")

     11.4.  Click **Create x509 Public Key**. <br><br>
        ![Create](./media/active-directory-saas-workday-tutorial/IC782929.png "Create")

     11.5.  In the **View x509 Public Key** section, perform the following steps: <br><br>
        ![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key") <br>

      1.  In the **Name** textbox, type a name for your certificate (e.g.: *PPE\_SP*).
      2.  In the **Valid From** textbox, type the valid from attribute value of your certificate.
      3.  In the **Valid To** textbox, type the valid to attribute value of your certificate.
        
           >[AZURE.NOTE] You can get the valid from date and the valid to date from the downloaded certificate by double-clicking it. The dates are listed under the **Details** tab.

      4.  Create a **Base-64 encoded** file from your downloaded certificate.  

        >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)

      5.  Open your base-64 encoded certificate in notepad, and then copy the content of it.
      6.  In the **Certificate** textbox, paste the content of your clipboard.
      7.  Click **OK**.

12.  Führen Sie die folgenden Schritte aus: <br><br>
        ![SSO-Konfiguration](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")

     12.1.  Aktivieren der **X509 Private Key Pair**.

     12.2.  In der **Dienstanbieter-ID** Textfeld **http://www.workday.com**.

     12.3.  Wählen Sie **SP-initiierte SAML-Authentifizierung**.

     12.4.  In Azure-Portal auf der **einmaliges Anmelden für Workday konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **IdP SSO Service URL** Textfeld.
     
     12,5 select **SP-initiierte Authentifizierungsanforderung keinen Deflate**.

     12.6. Als **Signaturmethode**, Option **SHA256**. <br><br>
        ![Signaturmethode](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method") <br><br>
 
     12,7 Klicken Sie auf **OK**. <br><br>
        ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. In Azure AD-Portal auf der **einmaliges Anmelden für Workday konfigurieren** auf **Weiter**. <br><br>

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782934.png "Configure single sign-on")

13. Auf der **Single Sign-On Bestätigung** auf **vollständig**. <br><br>

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configure single sign-on")



##Konfigurieren der Benutzerbereitstellung
  
Um einen in Workday bereitgestellten Testbenutzer zu bekommen, müssen Sie sich an das Workday-Supportteam wenden.  
Das Workday-Supportteam erstellt den Benutzer für Sie.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Workday Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Workday ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-workday-tutorial/IC782935.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-workday-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


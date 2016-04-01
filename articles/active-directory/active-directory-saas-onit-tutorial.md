<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Onit | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Onit mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Onit
  
In diesem Tutorial wird die Integration von Azure und Onit erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Onit-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Onit zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Onit-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Onit
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario")
##Aktivieren der Anwendungsintegration für Onit
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Onit aktivieren.

###So aktivieren Sie die Anwendungsintegration für Onit

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-onit-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Onit**.

    ![Anwendungskatalog](./media/active-directory-saas-onit-tutorial/IC791167.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Onit**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Onit zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Onit müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).
  
Ihre Onit-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Einmaliges Anmelden](./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Onit** anwendungsintegrationsseite im Menü am oberen Rand, klicken Sie auf **Attribute** Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-onit-tutorial/IC791169.png "Attributes")

2.  So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    
  	|Attribute Name|Attribute Value|
  	|---|---|
  	|name|User.userprincipalname|
  	|email|User.mail|

    1.  For each data row in the table above, click **add user attribute**.
    2.  In the **Attribute Name** textbox, type the attribute name shown for that row.
    3.  From the **Attribute Value** list, select the attribute value shown for that row.
    4.  Click **Complete**.

3.  Klicken Sie auf **Ändern**.

4.  Klicken Sie in Ihrem Browser auf **wieder** zum Öffnen der **Schnellstart** erneut zu öffnen.

5.  Klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791170.png "Configure Single Sign-On")

6.  Auf der **wie sollen sich Benutzer bei Onit anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")

7.  Auf der **App-URL konfigurieren** auf der Seite der **Onit-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer Ihre Onit-Anwendung anmelden (z. B.: "*https://ms-sso-test.onit.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-onit-tutorial/IC791172.png "Configure App URL")

8.  Auf der **einmaliges Anmelden bei Onit konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")

9.  Melden Sie sich in einem anderen Webbrowserfenster bei der Onit-Unternehmenswebsite als Administrator an.

10. Klicken Sie im Menü am oberen Rand **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")

11. Klicken Sie auf **Edit Corporation**.

    ![Unternehmen bearbeiten](./media/active-directory-saas-onit-tutorial/IC791175.png "Edit Corporation")

12. Klicken Sie auf die **Sicherheit** Registerkarte.

    ![Unternehmensinformationen bearbeiten](./media/active-directory-saas-onit-tutorial/IC791176.png "Edit Company Information")

13. Auf der **Sicherheit** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")

    1.  Als **Authentifizierungsstrategie**, Option **einmaliges Anmelden und das Kennwort**.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Onit konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Idp-Ziel-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Onit konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Idp-Abmelde-URL** Textfeld.
    4.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Idp Cert Fingerprint (SHA1)** Textfeld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    5.  Als **SSO Type**, Option **SAML**.
    6.  In der **SSO Login Button-Text** Textfeld Geben Sie eine Schaltfläche gewünscht.
    7.  Wählen Sie **Login with SSO: für die folgenden Domänen/Benutzer erforderlichen**, geben Sie die e-Mail-Adresse eines Testbenutzers in das entsprechende Textfeld ein, und klicken Sie dann auf **Update**.
        ![Bearbeiten des Unternehmens](./media/active-directory-saas-onit-tutorial/IC791178.png "Edit Corporation")

14. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Onit anmelden können, müssen sie in Onit bereitgestellt werden.  
Im Fall von Onit ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich auf Ihre **Onit** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Verwaltung](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")

3.  Auf der **Add User** Dialogfeld führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-onit-tutorial/IC791181.png "Add User")

    1.  Typ der **Namen** und **e-Mail-Adresse** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Erstellen**.  

        >[AZURE.NOTE] Der Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Onit Benutzerkonten oder APIs von Onit Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Onit Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Onit ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-onit-tutorial/IC791182.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-onit-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


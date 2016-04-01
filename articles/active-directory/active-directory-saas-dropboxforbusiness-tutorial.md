<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Dropbox für Unternehmen | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Dropbox für Unternehmen mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Dropbox für Unternehmen
  
In diesem Tutorial wird die Integration von Azure und Dropbox für Unternehmen erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Testmandanten in Dropbox für Unternehmen
  
Nach Abschluss dieses Lernprogramms Azure AD-Benutzer zugewiesene Dropbox für eine Unternehmen können sich in der Anwendung auf Ihrem Dropbox for Business-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Dropbox für Unternehmen
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")



##Aktivieren der Anwendungsintegration für Dropbox für Unternehmen
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Dropbox für Unternehmen aktivieren.

###So aktivieren Sie die Anwendungsintegration für Dropbox für Unternehmen

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Dropbox for Business**.

    ![Anwendungskatalog](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Dropbox for Business**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Dropbox für Unternehmen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Dropbox für Unternehmen zu authentifizieren.

Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat in Ihren Dropbox für Unternehmen-Mandanten hochladen. Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Dropbox for Business** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei Dropbox for Business** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

     3.1. Anmeldung bei Ihrem Dropbox for Business-Mandanten. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")

     3.2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Admin Console**. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")

     3.3. Auf der **Admin Console**, klicken Sie auf **Authentifizierung** im linken Navigationsbereich. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")

     3.4. In der **einmaliges Anmelden** Abschnitt **einmaliges Anmelden aktivieren**, und klicken Sie dann auf **Weitere** diesen Abschnitt zu erweitern.  <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")

     3.5. Kopieren Sie die URL neben **Benutzer können sich durch Eingabe ihrer e-Mail-Adresse anmelden oder gelangen direkt zu**. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")

     3.6. Auf Azure-Portal in die **DropBox for Business anmelden** Textfeld URL die URL. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  



4. Auf der **einmaliges Anmelden bei Dropbox for Business konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.  <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")


5. Für Ihren Dropbox for Business-Mandanten in der **des einmaligen Anmeldens** Teil der **Authentifizierung** führen die folgenden Schritte aus: <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")

     5.1. Klicken Sie auf **erforderliche**.

     5.2. In Azure-Portal auf der **einmaliges Anmelden bei Dropbox for Business konfigurieren** Kopieren der **-in-Seiten-URL** -Wert aus, und fügen Sie ihn in die **Anmelde-URL** Textfeld.


     5.3. Create a **Base-64 encoded** file from your downloaded certificate. > [AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).


     5.4. Click **Choose certificate**, and then browse to your **base-64 encoded certificate file**.


     5.5. Click **Save changes** to complete the configuration on your DropBox for Business tenant.


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld. <br><br>  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")





##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Dropbox für Unternehmen aktivieren.


### So konfigurieren Sie die Benutzerbereitstellung

1. In der Azure-Verwaltungsportal auf die **Dropbox for Business** AHA klicken Sie auf **Benutzerbereitstellung** zum Öffnen der **Benutzerbereitstellung konfigurieren** Dialogfeld.

2. Klicken Sie auf die benutzerbereitstellung aktivieren für DropBox für Unternehmen-Seite, auf benutzerbereitstellung aktivieren, um das Anmelden bei Dropbox zur Verknüpfung mit Azure AD-Dialogfeld zu öffnen.  <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")

3. Auf der **Anmelden bei Dropbox zur Verknüpfung mit Azure AD** Dialogfeld, melden Sie sich bei Ihrem Dropbox for Business-Mandanten. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")



4. Klicken Sie auf **Zulassen** Azure AD den Zugriff auf Dropbox zu gewähren. <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")



5. Um die Konfiguration abzuschließen, klicken Sie auf die **vollständig** Schaltfläche.  <br><br> ![Benutzerbereitstellung](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")




##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Dropbox für Unternehmen Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Dropbox for Business ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")
  


Nach 10 Minuten können Sie überprüfen, ob das Konto mit Citrix GoToMeeting synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie auf **Dashboard** in die **Dropbox for Business** Integration Anwendungsseite im Azure-Verwaltungsportal

<br><br>  ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")


Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt.

<br><br>  ![Benutzer zuweisen](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")


Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).




## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


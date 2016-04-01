<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Box | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Box mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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




#Tutorial: Azure Active Directory-Integration mit Box


  
In diesem Tutorial wird die Integration von Azure und Box erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Testmandant bei Box
  
Nach Abschluss dieses Lernprogramms Feld zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Feld-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Box
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")



##Aktivieren der Anwendungsintegration für Box
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Box aktivieren.

###So aktivieren Sie die Anwendungsintegration für Box

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Feld**.

    ![Anwendungskatalog](./media/active-directory-saas-box-tutorial/IC701023.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Feld**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Box zu authentifizieren. <br>
Als Teil dieses Verfahrens müssen Sie Metadaten in Box.com hochladen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Feld** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Box anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **im Mandanten-URL** Textfeld Geben Sie die URL Ihres Box-Mandanten (z. B.: https://<mydomainname>. box.com), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für Box konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und klicken Sie dann die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")

5.  Leiten Sie die Metadatendatei an das Supportteam von Box weiter. Das Supportteam muss einmaliges Anmelden für Sie konfigurieren.

6.  Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Box aktivieren.

###So konfigurieren Sie einmaliges Anmelden

1. In der Azure-Verwaltungsportal auf die **Feld** AHA klicken Sie auf **Benutzerbereitstellung** Öffnen der **Benutzerbereitstellung konfigurieren** Dialogfeld. <br> <br> ![Automatische benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")

2. Auf die **benutzerbereitstellung für Box aktivieren** Seite, klicken Sie auf **Benutzerbereitstellung**. <br><br>  ![Automatische benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")

3. Auf der **anmelden, um Zugriff auf Box zu gewähren** Seite, die erforderlichen Anmeldeinformationen bereitstellen, und klicken Sie dann auf **Autorisieren**. <br><br> ![Automatische benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")


4. Klicken Sie auf **Gewähren von Zugriff auf ein** diesen Vorgang zu autorisieren und zum Azure-Verwaltungsportal zurückzukehren. <br><br> ![Automatische benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")

5. Klicken Sie auf die Schaltfläche Fertig stellen, um die Konfiguration abzuschließen. <br><br> ![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")



##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Box Benutzer zu

1. Erstellen Sie im Azure AD-Portal ein Testkonto.

2. Auf der ** Feld ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**. <br><br> ![Zuweisen von Benutzern](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen. <br><br> ![Ja](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")
  

Nach 10 Minuten können Sie überprüfen, ob das Konto mit Box synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für "Box" auf "Dashboard" klicken.

<br><br> ![Dashboard](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:

<br><br> ![Integrationsstatus](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")


In Ihrem Box-Mandanten werden synchronisierte Benutzer unter aufgelistet **Managed Users** in die **Admin Console**.

<br><br> ![Integrationsstatus](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


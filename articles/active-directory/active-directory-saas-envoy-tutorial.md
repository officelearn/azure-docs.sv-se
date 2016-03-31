<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Envoy | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Envoy mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Envoy
  
In diesem Tutorial wird die Integration von Azure und Envoy erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Envoy-Mandanten
  
Nach Abschluss dieses Lernprogramms Envoy zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Envoy-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Envoy
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")
##Aktivieren der Anwendungsintegration für Envoy
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Envoy aktivieren.

###So aktivieren Sie die Anwendungsintegration für Envoy

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Envoy**.

    ![Anwendungskatalog](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Envoy**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Envoy zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Envoy müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Envoy** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Envoy anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Envoy-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Envoy.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für Envoy konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Envoy.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Envoy-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Klicken Sie auf **Unternehmen**.

    ![Unternehmen](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")

8.  Klicken Sie auf **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  In der **SAML-Authentifizierung** Konfiguration führen die folgenden Schritte aus:

    ![SAML-Authentifizierung](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")

    >[AZURE.NOTE] Der Wert für die Hauptstandort-ID wird automatisch von der Anwendung generiert.

    1.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Fingerabdruck** Textfeld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    2.  In Azure-Portal auf der **einmaliges Anmelden für Envoy konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider HTTP SAML URL** Textfeld.
    3.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Envoy steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Envoy anzumelden, überprüft Envoy, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Envoy automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Envoy Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Envoy ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


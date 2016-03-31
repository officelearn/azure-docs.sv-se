<properties 
    pageTitle="Tutorial: Azure AD-Integration mit Sciforma | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Sciforma mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure AD-Integration mit Sciforma
  
In diesem Tutorial wird die Integration von Azure und Sciforma erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Sciforma-Mandant
  
Nach Abschluss dieses Lernprogramms können sich sciforma zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Sciforma-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Sciforma
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")
##Aktivieren der Anwendungsintegration für Sciforma
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sciforma aktivieren.

###So aktivieren Sie die Anwendungsintegration für Sciforma:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Sciforma**.

    ![Anwendungskatalog](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Sciforma**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sciforma zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Sciforma** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Sciforma anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Sciforma-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Sciforma.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für sciforma konfigurieren** Seite, um Ihre Metadaten herunterzuladen, klicken Sie **Metadaten**, und klicken Sie dann die Datendatei lokal als **c:\\SciformaMetaData.xml**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")

5.  Leiten Sie die Metadatendatei an das Supportteam von Sciforma weiter. Das Supportteam muss einmaliges Anmelden für Sie konfigurieren.

6.  Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Sciforma steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Sciforma anzumelden, überprüft Sciforma, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Sciforma automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Sciforma Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Sciforma ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


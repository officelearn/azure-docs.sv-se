<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Overdrive Books | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Overdrive Books mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Overdrive Books
  
In diesem Tutorial wird die Integration von Azure und OverDrive erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein OverDrive-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms OverDrive zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer OverDrive-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für OverDrive
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")
##Aktivieren der Anwendungsintegration für OverDrive
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für OverDrive aktivieren.

###So aktivieren Sie die Anwendungsintegration für OverDrive

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **OverDrive**.

    ![Anwendungskatalog](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **OverDrive**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei OverDrive zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **OverDrive** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer bei OverDrive anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **OverDrive-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://mslibrarytest.libraryreserve.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei OverDrive konfigurieren** Blättern, um die Metadatendatei herunter, und senden Sie sie an das OverDrive-Supportteam.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configure single sign-on")

    >[AZURE.NOTE]Das OverDrive-Supportteam einmaliges Anmelden für Sie konfiguriert und sendet Ihnen eine Benachrichtigung, wenn die Konfiguration abgeschlossen ist.

5.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in OverDrive steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich bei OverDrive anzumelden, wird ggf. automatisch ein OverDrive-Konto erstellt.

>[AZURE.NOTE]Können Sie alle anderen OverDrive Benutzerkonten oder APIs von OverDrive Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie OverDrive Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** OverDrive ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


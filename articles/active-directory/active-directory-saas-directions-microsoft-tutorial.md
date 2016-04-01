<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Directions on Microsoft | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Directions on Microsoft mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Directions on Microsoft

In diesem Tutorial wird die Integration von Azure Active Directory und Directions on Microsoft erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Directions on Microsoft-Abonnement

Wenn Sie auf der Microsoft-Abonnement noch ein Directions haben, per e-Mail eine Anforderung an "*service@DirectionsOnMicrosoft.com*".

Nach Abschluss dieses Tutorials können sich die Azure Active Directory-Benutzer, die Sie Directions on Microsoft zugewiesen haben, mittels einmaliger Anmeldung bei der Anwendung anmelden.

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Directions on Microsoft
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario")
##Aktivieren der Anwendungsintegration für Directions on Microsoft

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Directions on Microsoft aktivieren.

###So aktivieren Sie die Anwendungsintegration für Directions on Microsoft

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Directions on Microsoft**.

    ![Anwendungskatalog](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Directions on Microsoft**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Szenario](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Directions on Microsoft zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Directions on Microsoft** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Aktivieren der einmaligen Anmeldung](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Enable Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Directions on Microsoft** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")

3.  Auf der **App-URL konfigurieren** Seite in das Textfeld Anmelde-URL **https://www.directionsonmicrosoft.com/user/login**, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Directions on Microsoft konfigurieren** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configure Single Sign-On")

5.  Senden Sie die Metadatendatei an das Directions on Microsoft-Support-Team (*service@DirectionsOnMicrosoft.com*). Geben Sie in der E-Mail Ihre Unternehmensinformationen an, damit das Supportteam von Directions on Microsoft Ihre Verbundwebsitemitgliedschaft ausfindig machen kann.

    >[AZURE.NOTE] Einmaliges Anmelden für Directions on Microsoft muss aktiviert werden, indem die Directions on Microsoft-Support-Team.
    Sie erhalten eine Benachrichtigung, wenn einmaliges Anmelden aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Für das Konfigurieren der Benutzerbereitstellung in Directions on Microsoft steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Directions on Microsoft anzumelden, überprüft Directions on Microsoft, ob der Benutzer vorhanden ist. Ist noch kein Benutzerkonto verfügbar, wird es von Directions on Microsoft automatisch erstellt.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Directions on Microsoft Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Directions on Microsoft ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Yes")



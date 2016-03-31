<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Brightspace von Desire2Learn | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Brightspace von Desire2Learn mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Brightspace von Desire2Learn

In diesem Lernprogramm wird die Integration von Azure und Brightspace von Desire2Learn erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Abonnement von Brightspace von Desire2Learn mit aktiviertem einmaligen Anmelden

Nach Abschluss dieses Lernprogramms Brightspace by Desire2Learn zugewiesene Azure AD-Benutzer werden bei der Anwendung auf Ihrer Brightspace by Desire2Learn-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Brightspace von Desire2Learn
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")
##Aktivieren der Anwendungsintegration für Brightspace von Desire2Learn

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Brightspace von Desire2Learn aktivieren.

###So aktivieren Sie die Anwendungsintegration für Brightspace von Desire2Learn

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Brightspace by Desire2Learn**.

    ![Anwendungskatalog](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")

7.  Wählen Sie im Ergebnisbereich **Brightspace by Desire2Learn**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Brightspace von Desire2Learn zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Brightspace by Desire2Learn** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Brightspace by Desire2Learn anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zum Anmelden bei Ihrem **Brightspace by Desire2Learn** (z. B.: **).
    2.  Klicken Sie auf **Weiter**

4.  Auf der **bei Brightspace by Desire2Learn einmaliges Anmelden konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadaten dann auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an Ihr Support-Team für Brightspace von Desire2Learn.

    >[AZURE.NOTE] Ihr Brightspace by Desire2Learn-Supportteam muss die tatsächliche SSO-Konfiguration zu übernehmen.
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Um Azure AD-Benutzern die Anmeldung bei Brightspace von Desire2Learn zu ermöglichen, müssen sie in Brightspace von Desire2Learn bereitgestellt werden.  
Im Fall von Brightspace von Desire2Learn müssen die Benutzerkonten durch Ihr Support-Team für Brightspace Desire2Learn erstellt werden.

>[AZURE.NOTE] Können alle anderen Brightspace Desire2Learn Benutzerkonten oder APIs von Brightspace by Desire2Learn für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Brightspace von Desire2Learn Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Brightspace by Desire2Learn ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



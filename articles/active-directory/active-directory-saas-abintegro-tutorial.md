<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Abintegro | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Abintegro mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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

#Lernprogramm: Azure Active Directory-Integration mit Abintegro

In diesem Lernprogramm wird die Integration von Azure und Abintegro erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Abintegro-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Abintegro zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Abintegro-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Abintegro
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenario")
##Aktivieren der Anwendungsintegration für Abintegro

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Abintegro aktivieren.

###So aktivieren Sie die Anwendungsintegration für Abintegro

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Abintegro**.

    ![Anwendungskatalog](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Abintegro**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Abintegro zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Abintegro** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configure Single SignOn")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei Abintegro** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configure Single SignOn")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Abintegro-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern zur Anmeldung bei Abintegro (z. B.: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden, um Abintegro** auf **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configure Single SignOn")

5.  Senden Sie die Metadatendatei an das Supportteam von Abintegro.

    >[AZURE.NOTE] Konfiguration die einmaligen Anmeldung muss durch das Supportteam von Abintegro erfolgen. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configure Single SignOn")
##Konfigurieren der Benutzerbereitstellung

Für das Konfigurieren der Benutzerbereitstellung in Abintegro steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Abintegro anzumelden, überprüft Abintegro, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Abintegro automatisch erstellt.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Abintegro Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Abintegro ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



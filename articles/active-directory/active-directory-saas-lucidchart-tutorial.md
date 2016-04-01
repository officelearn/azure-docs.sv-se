<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Lucidchart | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Lucidchart mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit Lucidchart
  
In diesem Tutorial wird die Integration von Azure und Lucidchart erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Lucidchart-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Lernprogramms Lucidchart zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Lucidchart-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Lucidchart
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")
##Aktivieren der Anwendungsintegration für Lucidchart
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Lucidchart aktivieren.

###So aktivieren Sie die Anwendungsintegration für Lucidchart

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Lucidchart**.

    ![Anwendungskatalog](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Lucidchart**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Lucidchart zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Lucidchart** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Lucidchart anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Lucidchart-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer Anwendung Lucidchart anmelden (z. B.: "*https://chart2.office.lucidchart.com/saml/sso/azure*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Lucidchart** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Lucidchart-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Team**.

    ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  Klicken Sie auf **Anwendung \ > Manage SAML**.

    ![SAML verwalten](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")

8.  Auf der **SAML Authentication Settings** Dialogfeld führen die folgenden Schritte aus:

    1.  Wählen Sie **Enable SAML Authentication**, und klicken Sie dann auf **Optional**.
        ![SAML-Authentifizierungseinstellungen](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
    2.  In der **Domäne** Textfeld Geben Sie Ihre Domäne, und klicken Sie dann auf **Zertifikat ändern**.
        ![Zertifikat ändern](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
    3.  Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt und fügen Sie ihn in die **Upload Metadata** Textfeld.
        ![Hochladen von Metadaten](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
    4.  Wählen Sie **automatisch Hinzufügen neuer Benutzer an das Team**, und klicken Sie dann auf **Speichern**.
        ![Änderungen speichern](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")

9.  Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Lucidchart steht kein Aktionselement zur Verfügung.  
Wenn sich ein zugewiesener Benutzer über den Zugriffsbereich bei Lucidchart anmelden möchte, überprüft Lucidchart, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Lucidchart automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Lucidchart Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Lucidchart ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


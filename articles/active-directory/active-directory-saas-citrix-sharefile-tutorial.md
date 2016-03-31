<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Citrix ShareFile | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Citrix ShareFile mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Citrix-ShareFile

In diesem Lernprogramm wird die Integration von Azure und Citrix ShareFile erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Citrix ShareFile-Mandanten

Nach Abschluss dieses Lernprogramms Citrix ShareFile zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Citrix ShareFile-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Citrix ShareFile
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")
##Aktivieren der Anwendungsintegration für Citrix ShareFile

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Citrix ShareFile aktivieren.

###So aktivieren Sie die Anwendungsintegration für Citrix ShareFile

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Citrix ShareFile**.

    ![Anwendungskatalog](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Citrix ShareFile**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Citrix ShareFile zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Citrix ShareFile** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei Citrix ShareFile** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Citrix ShareFile-Anmelde-URL** Textfeld Geben Sie die URL mithilfe des folgenden Musters `https://<tenant-name>.shareFile.com`, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei Citrix ShareFile** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")

5.  Melden Sie sich in einem Webbrowserfenster Ihrer **Citrix ShareFile** -Unternehmenswebsite als Administrator.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Admin**.

7.  Wählen Sie im linken Navigationsbereich **Konfigurieren Sie einmaliges Anmelden**.

    ![Kontoverwaltung](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")

8.  Auf der **einmaliges Anmelden / SAML 2.0-Konfiguration** Dialogseite **Grundeinstellungen**, gehen Sie folgendermaßen vor:

    ![Einmaliges Anmelden](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")

    1.  Klicken Sie auf **SAML aktivieren**.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Citrix ShareFile konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **Ihre IDP-Aussteller / Entitäts-ID** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Citrix ShareFile konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    4.  In Azure-Portal auf **die einmaliges Anmelden konfigurieren um Citrix ShareFile** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    5.  Klicken Sie auf **Ändern** neben dem **x. 509-Zertifikat** Feld Zertifikat, und klicken Sie dann laden Sie aus Azure AD-Portal heruntergeladen haben.
        ![Grundlegende Einstellungen](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")

9.  Klicken Sie auf **Speichern** im Citrix ShareFile-Verwaltungsportal.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Citrix ShareFile anmelden können, müssen sie in Citrix ShareFile bereitgestellt werden.  
Im Fall von Citrix ShareFile ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Citrix ShareFile** Mandanten.

2.  Klicken Sie auf **Verwalten von Benutzern \ > Manage Users Home \ > + Create Employee**.

    ![Create Employee](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")

3.  Geben Sie die **E-Mail**, **First Name** und **Nachname** eines gültigen Azure AD-Konto, die Sie bereitstellen möchten.

    ![Basic Information](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")

4.  Klicken Sie auf **Benutzer hinzufügen**.

    >[AZURE.NOTE] AAD-Kontoinhaber erhält eine e-Mail und folgt einem Link, um ihr Konto zu bestätigen, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Citrix ShareFile Benutzerkonten oder APIs von Citrix ShareFile Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Citrix ShareFile Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Citrix ShareFile ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



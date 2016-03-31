<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit New Relic | Microsoft Azure" 
    description="Erfahren Sie, wie Sie New Relic mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit New Relic
  
In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory und New Relic eingerichtet wird.
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein New Relic-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure Active Directory-Benutzer, die Sie New Relic zugewiesen haben, mittels einmaliger Anmeldung über den AAD-Zugriffsbereich anmelden.

1.  Aktivieren der Anwendungsintegration für New Relic
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")
##Aktivieren der Anwendungsintegration für New Relic
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für New Relic aktivieren.

###So aktivieren Sie die Anwendungsintegration für New Relic

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **New Relic**.

    ![Anwendungskatalog](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **New Relic**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei New Relic zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **New Relic** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei New Relic** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **neue Relic-Anmelde-URL** Textfeld die URL ein, von den Benutzern der Anwendung New Relic anmelden, und klicken Sie auf **Weiter**. 

    Die app-URL ist Ihre New Relic-Mandanten-URL (z. B.: *https://rpm.newrelic.com*):

    ![App-URL konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei New Relic** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatdatei dann lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")

5.  Melden Sie sich in einem Webbrowserfenster auf Ihrer **New Relic** -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **Konteneinstellungen**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")

7.  Klicken Sie auf die **Sicherheit und Authentifizierung** Registerkarte, und klicken Sie dann auf die **einmaliges Anmelden** Registerkarte.

    ![Einmaliges Anmelden](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")

8.  Führen Sie auf der Dialogfeldseite für SAML die folgenden Schritte aus:

    ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Klicken Sie auf **Choose File** zum Hochladen des heruntergeladenen Azure Active Directory-Zertifikats.
    2.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei New Relic** Seite, und kopieren Sie die **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Anmelde-URL** Textfeld.
    3.  Im Microsoft Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei New Relic** Seite, und kopieren Sie die **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmeldeziel-URL** Textfeld.
    4.  Klicken Sie auf **Änderungen speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure Active Directory-Benutzer bei New Relic anmelden können, müssen sie in New Relic bereitgestellt werden.  
Im Fall von New Relic ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten in New Relic die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **New Relic** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Konteneinstellungen**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")

3.  In der **Konto** auf der linken Seite, klicken Sie dann auf **Zusammenfassung**, und klicken Sie dann auf **Add User**.

    ![Kontoeinstellungen](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")

4.  Auf der **aktive Benutzer** im Dialogfeld die folgenden Schritte ausführen:

    ![Aktive Benutzer](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")

    1.  In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse eines gültigen Azure Active Directory-Benutzers, die Sie bereitstellen möchten.
    2.  Als **Rolle** wählen **Benutzer**.
    3.  Klicken Sie auf **Fügen Sie diesen Benutzer**.

>[AZURE.NOTE]Können Sie alle anderen New Relic Benutzerkonten oder APIs von New Relic Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie New Relic Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **New Relic** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).







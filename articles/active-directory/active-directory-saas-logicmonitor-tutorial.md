<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit LogicMonitor | Microsoft Azure" 
    description="Erfahren Sie, wie Sie LogicMonitor mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit LogicMonitor
  
In diesem Lernprogramm wird die Integration von Azure und LogicMonitor erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen LogicMonitor-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für LogicMonitor
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")
##Aktivieren der Anwendungsintegration für LogicMonitor
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für LogicMonitor aktivieren.

###So aktivieren Sie die Anwendungsintegration für LogicMonitor

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Logicmonitor**.

    ![Anwendungskatalog](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **LogicMonitor**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei LogicMonitor zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der ** LogicMonitor ** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei LogicMonitor** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zur Anmeldung bei LogicMonitor \ (e, g: "*http://company.logicmonitor.com*" \), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um LogicMonitor** auf **Metadaten**, und klicken Sie dann auf Ihrem Computer speichern.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")

5.  Melden Sie sich bei Ihrem **LogicMonitor** -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")

7.  Klicken Sie im Navigationsbereich auf der linken Seite, auf **einmaliges Anmelden**

    ![Einmaliges Anmelden](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  In der **Einstellungen für einmaliges Anmelden (SSO)** führen die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")

    1.  Wählen Sie **Enable Single Sign-on**.
    2.  Als **Standard-Rollenzuweisung**, auf **Readonly**.
    3.  Öffnen Sie die heruntergeladene Metadatendatei im Editor, und fügen Sie den Inhalt der Datei in der **Identitätsanbietermetadaten** Textfeld.
    4.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der LogicMonitor-Anwendung unter Verwendung ihrer Azure Active Directory-Benutzernamen bereitgestellt werden.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der LogicMonitor-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im Menü am oberen Rand **Einstellungen**, und klicken Sie dann auf **Rollen und Benutzer**.

    ![Rollen und Benutzer](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")

3.  Klicken Sie auf **Hinzufügen**.

4.  In der **Hinzufügen eines Kontos** führen die folgenden Schritte aus:

    ![Konto hinzufügen](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")

    1.  Typ der **Benutzername**, **E-Mail**, **Kennwort** und **Geben Sie das Kennwort** Werte von der Azure Active Directory-Benutzer, die Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie **Rollen**, **Anzeigeberechtigungen** und **Status**.
    3.  Klicken Sie auf **übermitteln**.

>[AZURE.NOTE]Können Sie alle anderen LogicMonitor Benutzerkonten oder APIs von LogicMonitor für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie LogicMonitor Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **LogicMonitor** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).







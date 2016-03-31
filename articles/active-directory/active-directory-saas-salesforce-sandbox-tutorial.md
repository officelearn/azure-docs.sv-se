<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Salesforce Sandbox | Microsoft Azure" description="Erfahren Sie, wie Sie Salesforce Sandbox mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und mehr zu aktivieren!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="10/07/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Salesforce Sandbox
>[AZURE.TIP]Um Feedback zu erhalten, klicken Sie auf [hier](http://go.microsoft.com/fwlink/?LinkId=521878).
  
In diesem Tutorial wird die Integration von Azure und Salesforce Sandbox erläutert.  
Sandboxes bieten Ihnen die Möglichkeit, mehrere Kopien Ihres Unternehmens in separaten Umgebungen für eine Vielzahl von Zwecken, wie z. B. Entwicklung, Tests und Schulungen, ohne eine Beeinträchtigung der Daten und Anwendungen in Ihrer Vertriebsproduktions-Organisation zu erstellen.  
Weitere Informationen finden Sie unter [Sandkästen – Übersicht](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Eine Sandbox unter Salesforce.com
  
Wenn Sie noch nicht über eine gültige Sandbox unter Salesforce.com verfügen, müssen Sie sich an Salesforce wenden.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Salesforce Sandbox
2.  Konfigurieren der einmaligen Anmeldung
3.  Aktivieren Ihrer Domäne
4.  Konfigurieren der Benutzerbereitstellung
5.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")
##Aktivieren der Anwendungsintegration für Salesforce Sandbox
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für  Salesforce Sandbox aktivieren.

###So aktivieren Sie die Anwendungsintegration für  Salesforce Sandbox:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")

4.  Zum Öffnen der **Anwendungskatalog**, klicken Sie auf **App hinzufügen**, und klicken Sie dann auf **Hinzufügen einer Anwendung für meine Organisation**.

    ![Was möchten Sie tun?](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "What do you want to do?")

5.  In der **Suchfeld**, Typ **Salesforce-Sandkasten**.

    ![Anwendungskatalog](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Application Gallery")

6.  Wählen Sie im Ergebnisbereich **Salesforce-Sandkasten**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei  Salesforce Sandbox zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Salesforce-Sandkasten** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Salesforce Sandbox anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Anmelde-URL** Textfeld Geben Sie die URL mithilfe des folgenden Musters `http://company.my.salesforce.com`, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configure App URL")

4. Wenn Sie bereits einmaliges Anmelden für eine andere Instanz von Salesforce-Sandkasten in Ihrem Verzeichnis konfiguriert haben, dann müssen Sie auch konfigurieren die **Bezeichner** haben den gleichen Wert wie die **Anmelde-URL**. Die **Bezeichner** Feld finden Sie durch Überprüfen der **Erweiterte Einstellungen anzeigen** auf das Kontrollkästchen der **App-URL konfigurieren** Seite des Dialogfelds.

4.  Auf der **einmaliges Anmelden konfigurieren, um Salesforce Sandbox** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Salesforce Sandbox-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Setup**.

    ![Einrichtung](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Setup")

7.  Klicken Sie im Navigationsbereich auf der linken Seite auf **Sicherheitskontrollen**, und klicken Sie dann auf **Einstellungen für einmaliges Anmelden für**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On Settings")

8.  Führen Sie im Abschnitt „Einstellungen für einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On Settings")

    1.  Wählen Sie **SAML aktiviert**.
    2.  Klicken Sie auf **neue**.

9.  Führen Sie im Abschnitt „Einstellungen für einmalige Anmelden für SAML“ die folgenden Schritte aus:

    ![SAML-Einstellungen für einmaliges Anmelden](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML Single Sign-On Settings")

    1.  Geben Sie in das Textfeld Name den Namen der Konfiguration (z. B.: *SPSSOWAAD\_Test*).
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Salesforce Sandbox** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    3.  In der **Entitäts-Id** Textfeld **https://test.salesforce.com** ist dies die erste Salesforce-Sandkasten-Instanz, die Sie zu Ihrem Verzeichnis hinzufügen. Wenn Sie dann noch eine Instanz der Salesforce-Sandkasten für hinzugefügt haben die **Entitäts-ID** Geben Sie in der **-Anmelde-URL**, der in diesem Format sein sollte: `http://company.my.salesforce.com`
    4.  Klicken Sie auf **Durchsuchen** um das heruntergeladene Zertifikat hochzuladen.
    5.  Als **SAML-Identitätstyp**, Option **Assertion enthält die Verbund-ID des Benutzerobjekts**.
    6.  Als **SAML Identity Location**, Option **Identität ist das Namenskennzeichner-Element der betreffanweisung**.
    7.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Salesforce Sandbox** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Textfeld.
    8.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Salesforce Sandbox** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Logout URL** Textfeld.
    9.  Als **Service Provider Initiated Request Binding**, Option **HTTP POST**.
    10. Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configure Single Sign-On")

##Aktivieren Ihrer Domäne
  
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits eine Domäne erstellt haben.  
Weitere Informationen finden Sie unter [Definieren des Domänennamens](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###Führen Sie zum Aktivieren Ihrer Domäne die folgenden Schritte aus:

1.  Klicken Sie im linken Navigationsbereich auf **Domain Management**, und klicken Sie dann auf **Meine Domäne.**

    ![Meine Domäne](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "My Domain")

    >[AZURE.NOTE]Stellen Sie sicher, dass Ihre Domäne richtig konfiguriert wurde.

2.  In der **Einstellungen einer Anmeldeseite** auf **Bearbeiten**, klicken Sie dann als **Authentifizierungsdienst**, wählen Sie den Namen der SAML einmaliges Anmelden Einstellung aus dem vorherigen Abschnitt, und klicken Sie anschließend auf **Speichern**.

    ![Meine Domäne](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "My Domain")
  
Sobald Sie eine Domäne konfiguriert haben, sollten Ihre Benutzer die Domänen-URL für die Anmeldung der Salesforce Sandbox verwenden.  
Wenn den Wert der URL abrufen möchten, klicken Sie auf das SSO-Profil, das Sie im vorherigen Abschnitt erstellt haben.
##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Salesforce Sandbox aktivieren.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Wählen Sie im Salesforce-Portal in der oberen Navigationsleiste Ihren Namen aus, um Ihr Benutzermenü zu erweitern:

    ![Meine Einstellungen](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "My Settings")

2.  Wählen Sie in Ihrem Benutzermenü **Meine Einstellungen** Öffnen Ihrer **Meine Einstellungen** Seite.

3.  Klicken Sie im linken Bereich auf **Persönliche** erweitern den Bereich, und klicken Sie dann auf **Mein Sicherheitstoken zurücksetzen**:

    ![Meine Einstellungen](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "My Settings")

4.  Auf der **Mein Sicherheitstoken zurücksetzen** auf **Sicherheitstoken zurücksetzen** eine e-Mail anfordern, die Ihrem Salesforce.com-Sicherheitstoken enthält.

    ![Neues Token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "New Token")

5.  Prüfen Sie Ihren Posteingang auf eine e-Mail von Salesforce.com mit "**sicherheitstokenbestätigung**" als Betreff.

6.  Öffnen Sie die E-Mail, und kopieren Sie das Sicherheitstoken.

7.  In der Azure-Verwaltungsportal auf die **Salesforce-Sandkasten** AHA klicken Sie auf **Benutzerbereitstellung** zum Öffnen der **Benutzerbereitstellung konfigurieren** Dialogfeld.

    ![Konfigurieren der Benutzerbereitstellung](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure user provisioning")

8.  Auf der **Geben Sie Ihre Salesforce-Sandkasten-Anmeldeinformationen, um automatische benutzerbereitstellung aktivieren** Seite, geben Sie die folgenden Konfigurationseinstellungen:

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    1.  In der **Salesforce Sandbox-Administratorbenutzername** eine Salesforce Sandbox-Kontonamen ein, geben die **Systemadministrator** zugewiesen.

    2.  In der **Salesforce Sandbox-Administratorkennwort** Textfeld Geben Sie das Kennwort für dieses Konto.

    3.  In der **Benutzersicherheitstoken** Textbox, fügen Sie den sicherheitstokenwert.

    4.  Klicken Sie auf **Validate** Ihrer Konfiguration.

    5.  Klicken Sie auf die **Weiter** Schaltfläche, um die **Bestätigung** Seite.

9.  Auf der **Bestätigung** auf **abgeschlossen** um Ihre Konfiguration zu speichern.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Salesforce Sandbox Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Salesforce-Sandkasten ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Yes")
  
Nach 10 Minuten können Sie überprüfen, ob das Konto mit Salesforce Sandbox synchronisiert wurde.
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).


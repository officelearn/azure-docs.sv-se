<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Slack | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Slack mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Slack
  
In diesem Tutorial wird die Integration von Azure und Slack erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Slack-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Slack zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Slack-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Slack
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-slack-tutorial/IC794980.png "Scenario")

##Aktivieren der Anwendungsintegration für Slack
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Slack aktivieren.

###So aktivieren Sie die Anwendungsintegration für Slack:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-slack-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-slack-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-slack-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Slack**.

    ![Anwendungskatalog](./media/active-directory-saas-slack-tutorial/IC794981.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Slack**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Szenario](./media/active-directory-saas-slack-tutorial/IC796925.png "Scenario")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Slack zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Slack** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794982.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Slack anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794983.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Slack-Anmelde-URL** Textfeld Geben Sie die URL Ihres Slack-Mandanten (z. B.: "*https://azuread.slack.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-slack-tutorial/IC794984.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei Slack** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794985.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Slack-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **in Microsoft Azure AD \ > Teameinstellungen**.

    ![Teameinstellungen](./media/active-directory-saas-slack-tutorial/IC794986.png "Team Settings")

7.  In der **Teameinstellungen** auf der **Authentifizierung** Registerkarte, und klicken Sie dann auf **Änderungseinstellungen**.

    ![Teameinstellungen](./media/active-directory-saas-slack-tutorial/IC794987.png "Team Settings")

8.  Auf der **SAML Authentication Settings** im Dialogfeld die folgenden Schritte ausführen:

    ![SAML-Einstellungen](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML Settings")

    1.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Slack** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **SAML 2.0-Endpunkt (HTTP)** Textfeld.
    2.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Slack** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter Aussteller** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
    
        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **öffentliches Zertifikat** Textfeld.
    5.  Deaktivieren Sie **können Benutzer ihre e-Mail-Adresse ändern**.
    6.  Wählen Sie **ermöglicht Benutzern die Auswahl von ihren eigenen Benutzernamen**.
    7.  Als **Authentifizierung für Ihr Team muss verwendet werden, indem**, auf **ist optional**.
    8.  Klicken Sie auf **Konfiguration speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-slack-tutorial/IC794989.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Slack anmelden können, müssen sie in Slack bereitgestellt werden.
  
Für das Konfigurieren der Benutzerbereitstellung in Slack steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich bei Slack anzumelden, wird ggf. automatisch ein Slack-Konto erstellt.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Slack Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Slack ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-slack-tutorial/IC794990.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-slack-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


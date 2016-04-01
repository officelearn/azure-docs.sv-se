<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Pagerduty | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Pagerduty mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Pagerduty
  
In diesem Tutorial wird die Integration von Azure und Pagerduty erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Pagerduty-Mandanten
  
Nach Abschluss dieses Lernprogramms Pagerduty zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Pagerduty-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Pagerduty
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")
##Aktivieren der Anwendungsintegration für Pagerduty
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Pagerduty aktivieren.

###So aktivieren Sie die Anwendungsintegration für Pagerduty

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Pagerduty**.

    ![Anwendungskatalog](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Pagerduty**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Pagerduty zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Pagerduty** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei Pagerduty** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Pagerduty-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Pagerduty.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")

4.  Auf der **einmaliges Anmelden bei Pagerduty konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Pagerduty-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Konteneinstellungen**.

    ![Kontoeinstellungen](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")

7.  Klicken Sie auf **einmaliges Anmelden**.

    ![Einmaliges Anmelden](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")

8.  Führen Sie auf der Seite „Einmaliges Anmelden aktivieren“ die folgenden Schritte aus:

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")

    1.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    2.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Pagerduty konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Pagerduty konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    5.  Wählen Sie **Single Sign-On aktivieren**.
    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Pagerduty anmelden können, müssen sie in Pagerduty bereitgestellt werden.  
Im Fall von Pagerduty ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Pagerduty** Mandanten.

2.  Klicken Sie im Menü am oberen Rand **Benutzer**.

3.  Klicken Sie auf **Hinzufügen von Benutzern**.

    ![Benutzer hinzufügen](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")

4.  Auf der **Ihr Team einladen** Dialogfelds die **vor- und Nachname** und die **E-Mail** Adresse der Azure AD-Benutzer, die Sie bereitstellen möchten, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Einladungen senden**.

    ![Team einladen](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")

    >[AZURE.NOTE] Alle hinzugefügten Benutzer erhalten eine Einladung zum Erstellen eines PagerDuty-Kontos.

>[AZURE.NOTE] Können Sie alle anderen Pagerduty Benutzerkonten oder APIs von Pagerduty Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Pagerduty Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Pagerduty ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


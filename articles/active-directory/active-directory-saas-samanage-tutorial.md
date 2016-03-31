<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Samanage | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Samanage mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Samanage
  
In diesem Tutorial wird die Integration von Azure und Samanage erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Samanage-Mandant
  
Nach Abschluss dieses Lernprogramms Samanage zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Samanage-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Samanage
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-samanage-tutorial/IC771705.png "Scenario")
##Aktivieren der Anwendungsintegration für Samanage
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Samanage aktivieren.

###So aktivieren Sie die Anwendungsintegration für Samanage:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-samanage-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-samanage-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-samanage-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Samanage**.

    ![Anwendungskatalog](./media/active-directory-saas-samanage-tutorial/IC771707.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Samanage**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Samanage zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Samanage** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771709.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Samanage anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-samanage-tutorial/IC771710.png "Microsoft Azure AD Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Samanage-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>.samanage.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771711.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für samanage** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Samanage-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Dashboard** und wählen Sie **Setup** Links im Navigationsbereich.

    ![Dashboard](./media/active-directory-saas-samanage-tutorial/IC771712.png "Dashboard")

7.  Klicken Sie auf **Single Sign-On**.

    ![Einmaliges Anmelden](./media/active-directory-saas-samanage-tutorial/IC771713.png "Single Sign-On")

8.  Auf der **mithilfe von SAML anmelden** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Speichern**:

    1.  Klicken Sie auf **einmaliges Anmelden mit SAML aktivieren**.
        ![Mithilfe von SAML anmelden](./media/active-directory-saas-samanage-tutorial/IC771719.png "Login using SAML")
    2.  In Azure-Portal auf der **einmaliges Anmelden für samanage konfigurieren** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die ** Identitätsanbieter-URL ** Textbox.
        ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configure Single Sign-On")
    3.  In Azure-Portal auf der **einmaliges Anmelden für samanage konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden für samanage konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    7.  Klicken Sie auf **Benutzer erstellen, wenn sie in Samanage nicht vorhanden sind**.
        ![Update](./media/active-directory-saas-samanage-tutorial/IC771722.png "Update")
    8.  Klicken Sie auf **Update**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Samanage anmelden können, müssen sie in Samanage bereitgestellt werden.  
Im Fall von Samanage ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Samanage** Mandanten.

2.  Wechseln Sie zu **Dashboard \ > Setup**.

    ![Einrichtung](./media/active-directory-saas-samanage-tutorial/IC771724.png "Setup")

3.  Klicken Sie auf die **Benutzer** Registerkarte

    ![Benutzer](./media/active-directory-saas-samanage-tutorial/IC771725.png "Users")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Neuer Benutzer](./media/active-directory-saas-samanage-tutorial/IC771726.png "New User")

5.  Typ der **e-Mail-Adresse** und **Namen** von Azure AD-Kontos Sie bereitstellen möchten und klicken Sie auf **Benutzer erstellen**.

    >[AZURE.NOTE]AAD-Kontoinhaber erhält eine e-Mail und folgt einem Link, um ihr Konto zu bestätigen, bevor es aktiviert wird.

    ![Benutzer erstellen](./media/active-directory-saas-samanage-tutorial/IC771727.png "Creat User")

>[AZURE.NOTE]Können Sie alle anderen Samanage Benutzerkonten oder APIs von Samanage Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Samanage Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Samanage ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-samanage-tutorial/IC771728.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-samanage-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SumoLogic | Microsoft Azure" 
    description="Erfahren Sie, wie Sie SumoLogic mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Tutorial: Azure Active Directory-Integration mit SumoLogic
  
In diesem Tutorial wird die Integration von Azure und SumoLogic erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SumoLogic-Mandant
  
Nach Abschluss dieses Lernprogramms Azure AD-Benutzer zugewiesene Sumologic werden mit Einmaliger Anmeldung bei der Anwendung auf Ihrer SumoLogic des Unternehmens (vom Dienstanbieter initiierte Anmeldung) oder mit der [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SumoLogic
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario")

##Aktivieren der Anwendungsintegration für SumoLogic
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SumoLogic aktivieren.

###So aktivieren Sie die Anwendungsintegration für SumoLogic:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Sumologic**.

    ![Anwendungskatalog](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **SumoLogic**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SumoLogic zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren SumoLogic-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SumoLogic** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei SumoLogic anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **SumoLogic-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. SumoLogic.com*", und klicken Sie dann auf **Weiter**.

    ![AOO-URL konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configure aoo URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um SumoLogic** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der SumoLogic-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Verwalten \ > Sicherheit**.

    ![Verwalten](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Manage")

7.  Klicken Sie auf **SAML**.

    ![Globale Sicherheitseinstellungen](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Global security settings")

8.  Aus der **Konfiguration auswählen oder erstellen Sie eine neue** aus **Azure AD**, und klicken Sie dann auf **konfigurieren**.

    ![SAML 2.0 konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configure SAML 2.0")

9.  Auf der **SAML 2.0 konfigurieren** im Dialogfeld die folgenden Schritte ausführen:

    ![SAML 2.0 konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configure SAML 2.0")

    1.  In der **Konfigurationsnamen** Textfeld **Azure AD**.
    2.  Wählen Sie **im Debugmodus**.
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um SumoLogic** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um SumoLogic** Kopieren der **Authentifizierungsanforderungs-URL** -Wert aus, und fügen Sie ihn in die **Authn Request URL** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie das gesamte Zertifikat in **x. 509-Zertifikat** Textfeld.
    7.  Als **e-Mail-Attribut**, auf **SAML-Antragsteller verwenden**.
    8.  Wählen Sie **SP-initiierte Anmeldekonfiguration**.
    9.  In der **Anmeldepfad** Textfeld **Azure**.
    10. Klicken Sie auf **Speichern**.

10. In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um SumoLogic** konfigurationsbestätigung konfigurationsbestätigung für einmaliges Anmelden, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configure single sign-on")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei SumoLogic anmelden können, müssen sie in SumoLogic bereitgestellt werden.  
Im Fall von SumoLogic ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **SumoLogic** Mandanten.

2.  Wechseln Sie zu **Verwalten \ > Benutzer**.

    ![Benutzer](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Users")

3.  Klicken Sie auf **Hinzufügen**.

    ![Benutzer](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Users")

4.  Auf der **neue Benutzer** im Dialogfeld die folgenden Schritte ausführen:

    ![Neuer Benutzer](./media/active-directory-saas-sumologic-tutorial/IC778563.png "New User")

    1.  Geben Sie die entsprechenden Informationen des Azure AD-Kontos, das Sie bereitstellen, in möchten der **Vorname**, **Nachname** und **E-Mail** Textfelder ein.
    2.  Wählen Sie eine Rolle aus.
    3.  Als **Status**, auf **Active**.
    4.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen SumoLogic Benutzerkonten oder APIs von SumoLogic Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SumoLogic Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **SumoLogic** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Sugar CRM | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Sugar CRM mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Sugar CRM
  
In diesem Lernprogramm wird die Integration von Azure und Sugar CRM  erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Sugar CRM-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Sugar CRM zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Sugar CRM-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Sugar CRM
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario")

##Aktivieren der Anwendungsintegration für Sugar CRM
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sugar CRM aktivieren.

###So aktivieren Sie die Anwendungsintegration für Sugar CRM:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Sugar CRM**.

    ![Anwendungskatalog](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Sugar CRM**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sugar CRM zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren Sugar CRM-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Sugar CRM** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Sugar CRM anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Sugar CRM-Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei Ihrer Sugar CRM-Anwendung (z. B.: "*http://company.sugarondemand.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei Sugar CRM** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der Sugar CRM-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

7.  In der **Verwaltung** auf **Password Management**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8.  Wählen Sie **SAML-Authentifizierung aktivieren**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9.  In der **SAML-Authentifizierung** führen die folgenden Schritte aus:

    ![SAML-Authentifizierung](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML Authentication")

    1.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Sugar CRM** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    2.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Sugar CRM** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **SLO URL** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie das gesamte Zertifikat in **x. 509-Zertifikat** Textfeld.
    5.  Klicken Sie auf **Speichern**.

10. In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Sugar CRM** Dialogfeld auf der Seite Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Sugar CRM anmelden können, müssen sie in Sugar CRM bereitgestellt werden.  
Im Fall von Sugar CRM ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Sugar CRM** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

3.  In der **Verwaltung** auf **User Management**.

    ![Verwaltung](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4.  Wechseln Sie zu **Benutzer \ > Erstellen Sie neue Benutzer**.

    ![Neuen Benutzer erstellen](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Create New User")

5.  Auf der **Benutzerprofil** führen die folgenden Schritte aus:

    ![Neuer Benutzer](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "New User")

    1.  Geben Sie die entsprechenden Informationen in die Textfelder „Benutzername“, „Nachname“ und „E-Mail-Adresse“ eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.

6.  Als **Status**, auf **Active**.

7.  Führen Sie auf der Registerkarte „Kennwort“ die folgenden Schritte aus:

    ![Neuer Benutzer](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "New User")

    1.  Geben Sie das Kennwort in das entsprechende Textfeld ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Sie können eine beliebige andere Sugar CRM Benutzerkonten verwenden oder APIs von Sugar CRM Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Sugar CRM Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Sugar CRM** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


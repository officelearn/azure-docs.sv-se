<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TalentLMS | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie TalentLMS mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit TalentLMS
  
In diesem Tutorial wird die Integration von Azure und  TalentLMS erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein  TalentLMS-Mandant
  
Nach Abschluss dieses Lernprogramms TalentLMS zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer TalentLMS-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TalentLMS
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

##Aktivieren der Anwendungsintegration für TalentLMS
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TalentLMS aktivieren.

###So aktivieren Sie die Anwendungsintegration für  TalentLMS:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **TalentLMS**.

    ![Anwendungskatalog](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **TalentLMS**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##Konfigurieren der einmaligen Anmeldung
  
Ziel dieses Abschnitts ist wie Benutzern bei TalentLMS mit ihrem Konto in Azure AD mithilfe von Verbund basierend auf dem SAML-Protokoll aktiviert werden kann. .  
Zum Konfigurieren des einmaligen Anmeldens für  TalentLMS müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **TalentLMS** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei TalentLMS anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **TalentLMS-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. TalentLMSapp.com*", und klicken Sie dann auf **Weiter**.

    ![Anmelde-URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Sign on URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um TalentLMS** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\TalentLMS.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der  TalentLMS-Unternehmenswebsite als Administrator an.

6.  In der **Konteneinstellungen** auf die **Benutzer** Registerkarte.

    ![Konto und Einstellungen](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account & Settings")

7.  Klicken Sie auf **einmaliges Anmelden (SSO)**,

8.  Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")

    1.  Aus der **SSO Integration Type** Liste **SAML 2.0**.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um TalentLMS** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter (IdP)** Textfeld.
    3.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um TalentLMS** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote Anmelde-URL** Textfeld.
    5.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um TalentLMS** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Abmelde-URL** Textfeld.
    6.  In der **TargetedID** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
    7.  In der **Vorname** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    8.  In der **Nachname** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    9.  In der **E-Mail** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
    10. Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TalentLMS anmelden können, müssen sie in TalentLMS bereitgestellt werden.  
Im Fall von TalentLMS ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **TalentLMS** Mandanten.

2.  Klicken Sie auf **Benutzer**, und klicken Sie dann auf **Benutzer hinzufügen**.

3.  Auf der **Add User** Dialogfeld führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Add User")

    1.  Geben Sie die zugehörigen Attributwerte des Azure AD-Benutzerkontos in die folgenden Textfelder ein: **Vorname**, **Nachname**, **e-Mail-Adresse**.
    2.  Klicken Sie auf **Benutzer hinzufügen**.

>[AZURE.NOTE] Können Sie alle anderen TalentLMS Benutzerkonten oder APIs von TalentLMS Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TalentLMS Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** TalentLMS ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zendesk | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zendesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zendesk
  
In diesem Tutorial wird die Integration von Azure und Zendesk erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zendesk-Mandanten
  
Nach Abschluss dieses Lernprogramms Zendesk zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Zendesk-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zendesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

##Aktivieren der Anwendungsintegration für Zendesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zendesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zendesk

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Zendesk**.

    ![Anwendungskatalog](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Zendesk**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zendesk zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Zendesk müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Zendesk** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Zendesk anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Zendesk-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>.zendesk.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für Zendesk konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\zendesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zendesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Admin**.

7.  Klicken Sie im linken Navigationsbereich auf **Einstellungen**, und klicken Sie dann auf **Security**.

    ![Sicherheit](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8.  Auf der **Sicherheit** Seite auf die **Admin & Agents** Registerkarte.

9.  Wählen Sie **einmaliges Anmelden (SSO) und SAML**, und wählen Sie dann **SAML**.

10. In Azure AD-Portal auf der **einmaliges Anmelden für Zendesk konfigurieren** Seite, und kopieren Sie die **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **SAML-SSO-URL** Textfeld.

11. In Azure AD-Portal auf der **einmaliges Anmelden für Zendesk konfigurieren** Seite, und kopieren Sie die **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Abmelde-URL** Textfeld.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.

    >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

13. Klicken Sie auf **Speichern**.

14. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

##Konfigurieren der Benutzerbereitstellung
  
Damit Azure AD-Benutzer anmelden können **Zendesk**, müssen sie in bereitgestellt werden **Zendesk**.  
Im Fall von **Zendesk**, erfolgt die Bereitstellung manuell.

###Führen Sie zum Bereitstellen von Benutzerkonten in Zendesk die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **Zendesk** Mandanten.

2.  Wählen Sie die **Kundenliste** Registerkarte.

3.  Wählen Sie die **Benutzer** Registerkarte, und klicken Sie auf **Hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")

4.  Geben Sie die e-Mail-Adresse eines vorhandenen Azure AD-Kontos Sie bereitstellen möchten, und klicken Sie dann auf **Speichern**.

    ![Neuer Benutzer](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

>[AZURE.NOTE] Können Sie alle anderen Zendesk Benutzerkonten oder APIs von Zendesk Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zendesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Zendesk ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


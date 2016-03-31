<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ScreenSteps | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ScreenSteps mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit ScreenSteps
  
In diesem Tutorial wird die Integration von Azure und ScreenSteps erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ScreenSteps-Mandant
  
Nach Abschluss dieses Lernprogramms ScreenSteps zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer ScreenSteps-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ScreenSteps
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")
##Aktivieren der Anwendungsintegration für ScreenSteps
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ScreenSteps aktivieren.

###So aktivieren Sie die Anwendungsintegration für ScreenSteps:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ScreenSteps**.

    ![Anwendungskatalog](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **ScreenSteps**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ScreenSteps zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ScreenSteps** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei ScreenSteps anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **ScreenSteps-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. ScreenSteps.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um ScreenSteps** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ScreenSteps-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Kontoverwaltung**.

    ![Kontenverwaltung](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

7.  Klicken Sie auf **Remoteauthentifizierung**.

    ![Remoteauthentifizierung](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

8.  Klicken Sie auf **Erstellen authentifizierungsendpunkts**.

    ![Remoteauthentifizierung](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

9.  In der **Erstellen eines Authentifizierungsendpunkts** führen die folgenden Schritte aus:

    ![Authentication Endpoint erstellen](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")

    1.  In der **Titel** Geben einen Titel.
    2.  Aus der **Modus** Liste **SAML**.
    3.  Klicken Sie auf **Erstellen**.

10. In der **Remoteauthentifizierungsendpunkt** führen die folgenden Schritte aus:

    ![Remote Authentication Endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1.  In Azure AD-Portal auf der **einmaliges Anmelden konfigurieren, um ScreenSteps** Seite, und kopieren Sie die **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Anmelde-URL** Textbox.
    2.  In Azure AD-Portal auf der **Konfigurieren Sie einmaliges Anmelden für screensteps** Seite, und kopieren Sie die **Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textbox.
    3.  Klicken Sie auf **Wählen Sie eine Datei**, und klicken Sie dann das heruntergeladene Zertifikat hochzuladen.
    4.  Klicken Sie auf **Update**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit Azure AD-Benutzer anmelden können **ScreenSteps**, müssen sie in bereitgestellt werden **ScreenSteps**.  
Im Fall von **ScreenSteps**, erfolgt die Bereitstellung manuell.

###Führen Sie zum Bereitstellen von Benutzerkonten in ScreenSteps die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **ScreenSteps** Mandanten.

2.  Klicken Sie auf **Kontoverwaltung**.

    ![Kontenverwaltung](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

3.  Klicken Sie auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Users")

4.  Klicken Sie auf **Erstellen Sie einen Benutzer**.

    ![Alle Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778545.png "All Users")

5.  Aus der **Benutzerrolle** Wählen Sie eine Rolle für den Benutzer.

6.  Geben Sie im Abschnitt Benutzerrolle die "**Vorname**, **Nachname**, **E-Mail**, **Anmeldung**, **Kennwort** und **Bestätigung des Kennworts**" eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.

    ![Neuer Benutzer](./media/active-directory-saas-screensteps-tutorial/IC778546.png "New user")

7.  Wählen Sie im Abschnitt Gruppen **Authentifizierungsgruppe (SAML)**, und klicken Sie dann auf **Create User**.

    ![Gruppen](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups")

>[AZURE.NOTE]Können Sie alle anderen ScreenSteps Benutzerkonten oder APIs von ScreenSteps Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ScreenSteps Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ScreenSteps ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Benutzer zuweisen](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


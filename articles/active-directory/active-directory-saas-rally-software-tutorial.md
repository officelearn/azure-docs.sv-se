<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Rally Software | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Rally Software mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Rally Software
  
In diesem Tutorial wird die Integration von Azure und Rally Software erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Rally Software-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Rally Software
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")
##Aktivieren der Anwendungsintegration für Rally Software
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Rally Software aktivieren.

###So aktivieren Sie die Anwendungsintegration für Rally Software

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Rally**.

    ![Anwendungskatalog](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Rally Software**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Rally Software zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der ** Rally Software ** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Rally anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Rally Software-Mandanten-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>.rally.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Rally** auf Metadaten herunterladen, und speichern Sie es auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")

5.  Melden Sie sich bei Ihrem **Rally Software** Mandanten.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Setup**, und wählen Sie dann **Abonnement**.

    ![Abonnement](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")

7.  Klicken Sie auf der **Aktion** Schaltfläche In der Symbolleiste am oberen Rand auf der rechten Seite, und wählen Sie dann **Abonnement bearbeiten**.

8.  Auf der **Abonnement** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Speichern und schließen**:

    ![Authentifizierung](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")

    1.  Wählen Sie **Rally- oder SSO-Authentifizierung** aus der Dropdownliste Authentifizierung
    2.  In Azure-Portal auf der **einmaliges Anmelden für Rally Software konfigurieren** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **-URL des Identitätsanbieters** Textbox
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Rally Software** Kopieren der **Remote-Abmelde-URL** Wert.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der Rally Software-Anwendung unter Verwendung ihrer Azure Active Directory-Benutzernamen bereitgestellt werden.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem Rally Software-Mandanten an.

2.  Wechseln Sie zu **Setup \ > Benutzer**, und klicken Sie dann auf **+ Add New**.

    ![Benutzer](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")

3.  Geben Sie den Namen in das Textfeld neuen Benutzer ein, und klicken Sie dann auf **Add with Details**.

4.  In der **Create User** führen die folgenden Schritte aus:

    ![Benutzer erstellen](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")

    1.  In der **Benutzername** Textfeld Geben Sie den Namen des Azure AD-Benutzers, die Sie bereitstellen möchten.
    2.  In der **e-Mail-Adresse** Textfeld Geben Sie die e-Mail-Adresse der Azure AD-Benutzer, die Sie bereitstellen möchten.
    3.  Klicken Sie auf **Speichern und schließen**.

>[AZURE.NOTE]Können Sie alle anderen Rally Software Benutzerkonten oder APIs von Rally Software Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Rally Software Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Rally Software** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).







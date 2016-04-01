<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Thoughtworks Mingle | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Thoughtworks Mingle mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Thoughtworks Mingle
  
In diesem Tutorial wird die Integration von Azure und Thoughtworks Mingle erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Thoughtworks Mingle-Mandant
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Thoughtworks Mingle
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario")

##Aktivieren der Anwendungsintegration für Thoughtworks Mingle
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Thoughtworks Mingle aktivieren.

###So aktivieren Sie die Anwendungsintegration für Thoughtworks Mingle:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Thoughtworks mingle**.

    ![Anwendungskatalog](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Thoughtworks Mingle**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Thoughtworks Mingle zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Zertifikat auf Thoughtworks Mingle hochladen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der ** Thoughtworks Mingle ** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Thoughtworks Mingle anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Thoughtworks Mingle-Mandanten-URL** Textfeld Ihre URL nach dem Muster "*http://company.mingle.thoughtworks.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei Thoughtworks Mingle** auf Metadaten herunterladen, und speichern Sie es auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configure single sign-on")

5.  Melden Sie sich bei Ihrem **Thoughtworks Mingle** -Unternehmenswebsite als Administrator.

6.  Klicken Sie auf die **Admin** Registerkarte, und klicken Sie dann auf **SSO Config**.

    ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")

7.  In der **SSO Config** führen die folgenden Schritte aus:

    ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")

    1.  Um die Metadatendatei hochzuladen, klicken Sie auf **Choose File**.
    2.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configure single sign-on")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie in der Thoughtworks Mingle-Anwendung unter Verwendung ihrer Azure Active Directory-Benutzernamen bereitgestellt werden.  
Im Fall von Thoughtworks Mingle ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der Thoughtworks Mingle-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf **Profil**.

    ![Ihr erstes Projekt](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")

3.  Klicken Sie auf die **Admin** Registerkarte, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Users")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Neuer Benutzer](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")

5.  Auf der **neue Benutzer** Dialogfeld führen die folgenden Schritte aus:

    ![Neuer Benutzer](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")

    1.  Typ der **Anmeldename**, **Anzeigenamen**, **Wählen Sie das Kennwort**, **Kennwort bestätigen** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Als **Benutzertyp**, Option **vollständigen**.
    3.  Klicken Sie auf **Create This Profile**.

>[AZURE.NOTE] Können Sie alle anderen Thoughtworks Mingle Benutzerkonten oder APIs von Thoughtworks Mingle Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Thoughtworks Mingle Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Thoughtworks Mingle** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



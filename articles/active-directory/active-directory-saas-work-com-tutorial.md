<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Work.com | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Work.com mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Work.com
  
In diesem Tutorial wird die Integration von Azure und Work.com erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Work.com-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms, weisen Sie AAD-Benutzer, denen Sie haben, Work.com-Zugriff mit Einmaliger Anmeldung bei der Anwendung auf Ihrer Work.com-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Work.com
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

##Aktivieren der Anwendungsintegration für Work.com
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Work.com aktivieren.

###So aktivieren Sie die Anwendungsintegration für Work.com

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Work.com**.

    ![Anwendungskatalog](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Work.com**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Work.com zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Zertifikat auf Work.com hochladen.

>[AZURE.NOTE] Um einmaliges Anmelden konfigurieren zu können, müssen Sie noch einen benutzerdefinierten Work.com-Domänenname eingerichtet. Sie müssen mindestens einen Domänennamen definieren, testen und dann in Ihrer gesamten Organisation bereitstellen.

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei Ihrem Work.com-Mandanten als Administrator an.

2.  Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3.  Im linken Navigationsbereich in die **verwalten** auf **Domain Management** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Meine Domäne** zum Öffnen der **Meine Domäne** Seite. 

    ![Meine Domäne](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")

4.  Um sicherzustellen, dass Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass dieses "**Schritt 4 für Benutzer bereitgestellt**", und überprüfen Sie Ihre "**Meine Domäneneinstellungen**".

    ![Domäne für Benutzer bereitgestellt](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")

5.  Melden Sie sich in einem anderen Webbrowserfenster beim Azure-Portal an.

6.  Auf der **Work.com ** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")

7.  Auf der **wie sollen sich Benutzer bei Work.com anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")

8.  Auf der **App-URL konfigurieren** auf der Seite der **Work.com-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern zur Anmeldung bei Ihrem Work.com-Anwendung (z. B.: " *http://company.my.salesforce.com*"), und klicken Sie dann auf **Weiter**: 

    ![App-URL konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")

9.  Auf der **einmaliges Anmelden bei Work.com konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")

10. Melden Sie sich bei Ihrem Work.com-Mandanten an.

11. Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

12. Erweitern Sie die **Sicherheitskontrollen** Menü, und klicken Sie dann auf **Einstellungen für einmaliges Anmelden für**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")

13. Auf der **Single Sign-On Settings** Dialogfeld führen die folgenden Schritte aus:

    ![SAML aktiviert](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")

    1.  Wählen Sie **SAML aktiviert**.
    2.  Klicken Sie auf **neue**.

14. In der **SAML Single Sign-On Settings** führen die folgenden Schritte aus:

    ![SAML-Einstellung für einmaliges Anmelden](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")

    1.  In den **Namen** Geben einen Namen für Ihre Konfiguration.  

        >[AZURE.NOTE] Wenn ein Wert für **Namen** automatisch aufgefüllt der **API-Name** Textbox.

    2.  In Azure-Portal auf der **einmaliges Anmelden bei Work.com konfigurieren** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    3.  Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Durchsuchen**.
    4.  In der **Entitäts-Id** Textfeld **https://salesforce-work.com**.
    5.  Als **SAML-Identitätstyp**, Option **Assertion enthält die Verbund-ID des Benutzerobjekts**.
    6.  Als **SAML Identity Location**, Option **Identität ist in the NameIdentifier Element der betreffanweisung**.
    7.  In Azure-Portal auf der **einmaliges Anmelden bei Work.com konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Textfeld.
    8.  In Azure-Portal auf der **einmaliges Anmelden bei Work.com konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Logout URL** Textfeld.
    9.  Als **Service Provider Initiated Request Binding**, Option **HTTP Post**.
    10. Klicken Sie auf **Speichern**.

15. Klicken Sie in Ihrem Work.com-Portal im linken Navigationsbereich auf **Domain Management** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Meine Domäne** zum Öffnen der **Meine Domäne** Seite. 

    ![Meine Domäne](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")

16. Auf der **Meine Domäne** auf der Seite der **Branding der Anmeldeseite** auf **Bearbeiten**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")

17. Auf der **Branding der Anmeldeseite** auf der Seite der **Authentifizierungsdienst** der Name des Ihrer **SAML-SSO-Einstellungen** wird angezeigt. Wählen Sie ihn aus, und klicken Sie dann auf **Speichern**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")

18. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure Active Directory-Benutzer anmelden können, müssen sie in Work.com bereitgestellt werden.  
Im Fall von Work.com ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der Work.com-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3.  Wechseln Sie zu **Verwalten von Benutzern \ > Benutzer**.

    ![Benutzer verwalten](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Alle Benutzer](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5.  Führen Sie im Abschnitt „Benutzer bearbeiten“ die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")

    1.  Typ der **Last Name**, **Alias**, **E-Mail**, **Benutzername** und **Spitznamen** Attribute eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie **Rolle**, **Benutzerlizenz** und **Profil**.
    3.  Klicken Sie auf **Speichern**.  

        >[AZURE.NOTE] Die Azure Active Directory-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Work.com Benutzerkonten oder APIs von Work.com Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Work.com Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der anwendungsintegrationsseite für Work.com auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")
  
Nach 10 Minuten können Sie überprüfen, ob das Konto mit Work.com synchronisiert wurde.
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Jobscience | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Jobscience mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Jobscience
  
In diesem Tutorial wird die Integration von Azure und Jobscience erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Jobscience-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Jobscience zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Jobscience-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jobscience
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")
##Aktivieren der Anwendungsintegration für Jobscience
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jobscience aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jobscience

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Jobscience**.

    ![Anwendungskatalog](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Jobscience**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Jobscience zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Jobscience müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei der Jobscience-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")

3.  Im linken Navigationsbereich in die **verwalten** auf **Domain Management** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Meine Domäne** zum Öffnen der **Meine Domäne** Seite. 

    ![Meine Domäne](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

4.  Um sicherzustellen, dass Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass dieses "**Schritt 4 für Benutzer bereitgestellt**", und überprüfen Sie Ihre "**Meine Domäneneinstellungen**".

    ![Domäne für Benutzer bereitgestellt](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")

5.  Melden Sie sich in einem anderen Webbrowserfenster beim Azure-Portal an.

6.  Auf der **Jobscience** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")

7.  Auf der **wie sollen sich Benutzer bei Jobscience anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")

8.  Auf der **App-URL konfigurieren** auf der Seite der **Jobscience-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.my.salesforce.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configure App URL")

9.  Auf der **einmaliges Anmelden bei Jobscience konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")

10. Klicken Sie auf der Jobscience-Unternehmenswebsite auf **Sicherheitskontrollen**, und klicken Sie dann auf **Einstellungen für einmaliges Anmelden für**.

    ![Sicherheitskontrollen](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")

11. In der **Single Sign-On Settings** führen die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")

    1.  Wählen Sie **SAML aktiviert**.
    2.  Klicken Sie auf **neue**.

12. Auf der **SAML Einstellung für einmaliges Anmelden bearbeiten** im Dialogfeld die folgenden Schritte ausführen:

    ![SAML-Einstellung für einmaliges Anmelden](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")

    1.  In den **Namen** Geben einen Namen für Ihre Konfiguration.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Jobscience konfigurieren** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textbox
    3.  In der **Entitäts-Id** Textfeld **https://salesforce-jobscience.com**
    4.  Klicken Sie auf **Durchsuchen** Ihrer Azure AD-Zertifikat hochladen.
    5.  Als **SAML-Identitätstyp**, Option **Assertion enthält die Verbund-ID des Benutzerobjekts**.
    6.  Als **SAML Identity Location**, Option **Identität ist in the NameIdentifier Element der betreffanweisung**.
    7.  In Azure-Portal auf der **einmaliges Anmelden bei Jobscience konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Textbox
    8.  In Azure-Portal auf der **einmaliges Anmelden bei Jobscience konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Logout URL** Textbox
    9.  Klicken Sie auf **Speichern**.

13. Im linken Navigationsbereich in die **verwalten** auf **Domain Management** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Meine Domäne** zum Öffnen der **Meine Domäne** Seite. 

    ![Meine Domäne](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

14. Auf der **Meine Domäne** auf der Seite der **Branding der Anmeldeseite** auf **Bearbeiten**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")

15. Auf der **Branding der Anmeldeseite** auf der Seite der **Authentifizierungsdienst** der Name des Ihrer **SAML-SSO-Einstellungen** wird angezeigt. Wählen Sie ihn aus, und klicken Sie dann auf **Speichern**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")

16. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")
  
Zum Abrufen des SP initiated Single Sign-Anmelde-URL auf auf die **Single Sign On Settings** in die **Sicherheitskontrollen** Abschnitt des Kontextmenüs.

![Sicherheitskontrollen](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")
  
Klicken Sie auf das SSO-Profil, das Sie im obigen Schritt erstellt haben.  
Auf dieser Seite wird die einmalige URL für Ihr Unternehmen (z. B. *https://companyname.my.salesforce.com?so=companyid*).
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Jobscience anmelden können, müssen sie in Jobscience bereitgestellt werden.  
Im Fall von Jobscience ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Jobscience** -Unternehmenswebsite als Administrator.

2.  Navigieren Sie zu „Einrichtung“.

    ![Einrichtung](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")

3.  Wechseln Sie zu **Verwalten von Benutzern \ > Benutzer**.

    ![Benutzer](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Alle Benutzer](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")

5.  Auf der **Benutzer bearbeiten** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer bearbeiten](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")

    1.  Geben Sie den Vornamen, den Nachnamen, den Alias, die E-Mail-Adresse, den Benutzernamen und den Spitznamen des Azure AD-Benutzers, den Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE] Der Azure AD-Kontoinhaber erhält eine e-Mail, die einen Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Jobscience Benutzerkonten oder APIs von Jobscience Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Jobscience Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Jobscience ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


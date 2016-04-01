<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Coupa | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Coupa mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Coupa

In diesem Tutorial wird die Integration von Azure und Coupa erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Coupa-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Coupa zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Coupa
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")
##Aktivieren der Anwendungsintegration für Coupa

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Coupa aktivieren.

###So aktivieren Sie die Anwendungsintegration für Coupa

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-coupa-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-coupa-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Coupa**.

    ![Anwendungskatalog](./media/active-directory-saas-coupa-tutorial/IC791898.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Coupa**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Coupa zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Coupa müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei der Coupa-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Setup \ > Sicherheitskontrolle**.

    ![Sicherheitskontrollen](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")

3.  Um die Coupa-Metadatendatei auf Ihren Computer herunterladen möchten, klicken Sie auf **Download und Import SP Metadata**.

    ![Coupa-SP-Metadaten](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")

4.  Melden Sie sich in einem anderen Browserfenster beim Azure Active Directory-Portal an.

5.  Auf der **Coupa** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configure Single Sign-On")

6.  Auf der **wie sollen sich Benutzer bei Coupa anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configure Single Sign-On")

7.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld Geben Sie den von den Benutzern zur Anmeldung bei Ihrer Coupa-Anwendung verwendete URL (z. B.: "*http://company.Coupa.com*").
    2.  Öffnen Sie die heruntergeladene Coupa-Metadatendatei, und kopieren Sie die **AssertionConsumerService Index/URL**.
    3.  In der **Coupa Reply URL** Textfeld einfügen der **AssertionConsumerService Index/URL** Wert.
    4.  Klicken Sie auf **Weiter**.

8.  Auf der **einmaliges Anmelden konfigurieren, um Coupa** Seite zum Herunterladen der Metadatendatei, klicken Sie auf **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configure Single Sign-On")

9.  Wechseln Sie auf der Coupa-Unternehmenswebsite zu **Setup \ > Sicherheitskontrolle**.

    ![Sicherheitskontrollen](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")

10. In der **mit Coupa-Anmeldeinformationen anmelden** führen die folgenden Schritte aus:

    ![Mit Coupa-Anmeldeinformationen anmelden](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials")

    1.  Wählen Sie **mithilfe von SAML anmelden**.
    2.  Klicken Sie auf **Durchsuchen** Ihre heruntergeladene Azure Active-Metadatendatei hochladen.
    3.  Klicken Sie auf **Speichern**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Coupa anmelden können, müssen sie in Coupa bereitgestellt werden.  
Im Fall von Coupa ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Coupa** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Setup**, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-coupa-tutorial/IC791908.png "Users")

3.  Klicken Sie auf **Erstellen**.

    ![Benutzer erstellen](./media/active-directory-saas-coupa-tutorial/IC791909.png "Create Users")

4.  In der **Benutzer erstellen** führen die folgenden Schritte aus:

    ![Benutzerdetails](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")

    1.  Typ der **Anmeldung**, **First Name**, **Nachname**, **Single Sign-On ID**, **E-Mail** Attribute eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Erstellen**.

    >[AZURE.NOTE] Die Azure Active Directory-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Coupa Benutzerkonten oder APIs von Coupa Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Coupa Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Coupa ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-coupa-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



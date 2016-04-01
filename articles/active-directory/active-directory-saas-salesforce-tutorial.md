<properties
   pageTitle="Tutorial: Integrieren von Salesforce in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie Salesforce mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und mehr zu aktivieren!"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="liviodlc"/>

#Tutorial: Integrieren von Salesforce in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Salesforce-Umgebung mit Ihrem Azure Active Directory verbinden. Sie erfahren, wie Sie die einmalige Anmeldung bei Salesforce konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf Salesforce zuweisen.

##Voraussetzungen

1. Azure Active Directory über Zugriff auf die [Azure-Verwaltungsportal](https://manage.windowsazure.com), benötigen Sie ein gültiges Azure-Abonnement.

2. Sie müssen einen gültigen Mandanten in [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Bei Verwendung eines Salesforce.com **Testversion** -Konto, und klicken Sie dann Sie automatisierte benutzerbereitstellung konfigurieren können. Bei Testkonten ist der erforderliche API-Zugriff erst nach dem Erwerb aktiviert.
> 
> Sie können diese Einschränkung umgehen, mithilfe einer [frei Entwicklerkonto](https://developer.salesforce.com/signup) zum Bearbeiten dieses Lernprogramms.

Wenn Sie eine Salesforce Sandbox-Umgebung verwenden, finden Sie in der [Salesforce-Sandkasten Integration Lernprogramm](https://go.microsoft.com/fwLink/?LinkID=521879).

##Videotutorials

Sie können zu diesem Tutorial die folgenden Videos nutzen.

**Videotutorial, Teil 1: Aktivieren der einmaligen Anmeldung**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Videotutorial, Teil 2: Automatisieren der Benutzerbereitstellung**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##Schritt 1: Hinzufügen von Salesforce zu Ihrem Verzeichnis

1. In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken Sie im linken Navigationsbereich auf **Active Directory**.

    ![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis, das Sie Salesforce zum hinzufügen möchten.

3. Klicken Sie auf **Anwendungen** im oberen Menü.

    ![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. In der **Suchfeld**, Typ **Salesforce**. Wählen Sie dann **Salesforce** aus den Ergebnissen, und klicken Sie auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Fügen Sie Salesforce hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für Salesforce sehen:

    ![Seite "Schnellstart" für Salesforce in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Bevor Sie die einmalige Anmeldung konfigurieren können, müssen Sie eine benutzerdefinierte Domäne für Ihre Salesforce-Umgebung einrichten und bereitstellen. Informationen hierzu finden Sie unter [Einrichten eines Domänennamens](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Salesforce Seite "Schnellstart" in Azure AD, klicken Sie auf die **einmaliges Anmelden konfigurieren** Schaltfläche.

    ![Schaltfläche zum Konfigurieren der einmaligen Anmeldung][6]

3. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Salesforce anmelden?" Wählen Sie **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

    > [AZURE.NOTE] Weitere Informationen zu den verschiedenen einmaligen Anmelden für Optionen zu [Klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

4. Auf der **App-Einstellungen konfigurieren** Seite, füllen Sie die **-Anmelde-URL** in Ihrem Salesforce-Domänen-URL im folgenden Format eingeben:
 - Enterprise-Konto:`https://<domain>.my.salesforce.com`
 - Developer-Konto:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Geben Sie Ihre Anmelde-URL ein.][8]

5. Auf der **einmaliges Anmelden für Salesforce konfigurieren** Seite, klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Laden Sie das Zertifikat herunter.][9]

6. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce-Administratorkonto an.

7. Unter den **Administrator** im Navigationsbereich, klicken Sie auf **Sicherheitskontrollen** auf den entsprechenden Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden für**.

    ![Klicken Sie unter "Sicherheitssteuerelemente" auf "Einstellungen für einmaliges Anmelden".][10]

8. Auf der **Single Sign-On Settings** auf die **Bearbeiten** Schaltfläche.

    ![Klicken Sie auf die Schaltfläche "Bearbeiten"][11]

    > [AZURE.NOTE] Wenn Sie nicht auf Einstellungen für einmaliges Anmelden für Ihr Salesforce-Konto aktivieren können, müssen Sie Salesforces-Support kontaktieren, um die für Sie aktiviert haben.

9. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

    ![Wählen Sie "SAML aktiviert".][12]

10. Um Ihre SAML einmaligen Anmelden Einstellungen konfigurieren, klicken Sie auf **Neu**.

    ![Wählen Sie "SAML aktiviert".][13]

11. Auf der **SAML Einstellung für einmaliges Anmelden bearbeiten** stellen die folgenden Konfigurationen:

    ![Screenshot der Konfigurationen, die Sie durchführen sollten][14]

 - Für den **Namen** Geben einen Namen für diese Konfiguration. Angabe eines Werts für **Namen** automatisch Auffüllen der **API-Name** Textfeld.

 - Kopieren Sie in Azure AD die **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Feld Salesforce.

 - In der **Entitäts-Id ein**, geben Sie Ihre Salesforce-Domänennamen, der dem folgenden Muster:
     - Enterprise-Konto:`https://<domain>.my.salesforce.com`
     - Developer-Konto:`https://<domain>-dev-ed.my.salesforce.com`

 - Klicken Sie auf **Durchsuchen** oder **Choose File** zum Öffnen der **für den Dateiupload** Dialogfeld Wählen Sie Ihr Salesforce-Zertifikat, und klicken Sie dann auf **Öffnen** zum Hochladen des Zertifikats.

 - Für **SAML-Identitätstyp**, Option **Assertion enthält den salesforce.com-Benutzernamen des Benutzers**.

 - Für **SAML Identity Location**, Option **Identität ist das Namenskennzeichner-Element der betreffanweisung**

 - Kopieren Sie in Azure AD die **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Feld Salesforce.

 - Für **Service Provider Initiated Request Binding**, Option **HTTP-Umleitung**.

 - Klicken Sie abschließend auf **Speichern** um Ihre SAML einmaligen Anmelden Einstellungen übernehmen.

12. Klicken Sie im linken Navigationsbereich in Salesforce auf **Domain Management** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Meine Domäne**.

    ![Klicken Sie auf "Meine Domäne"][15]

13. Führen Sie einen Bildlauf nach unten, um die **Authentifizierungskonfiguration** hinzu, und klicken Sie auf die **Bearbeiten** Schaltfläche.

    ![Klicken Sie auf die Schaltfläche "Bearbeiten"][16]

14. In der **Authentifizierungsdienst** Abschnitt wählen Sie den Anzeigenamen der SAML-SSO-Konfiguration, und klicken Sie dann auf **Speichern**.

    ![Wählen Sie Ihre SSO-Konfiguration][17]

    > [AZURE.NOTE] Wenn mehr als ein Authentifizierungsdienst aktiviert ist, werden dann Wenn Benutzer versuchen, einmaliges Anmelden für Ihre Umgebung Salesforce initiieren sie aufgefordert die Authentication-Dienst wählen sie für die Anmeldung. Wenn Sie dies möchten nicht, sollten Sie **lassen Sie alle anderen Authentifizierungsdienste deaktiviert**.

15. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das Zertifikat zu aktivieren, das Sie nach Salesforce hochgeladen haben. Klicken Sie dann auf **Weiter**.

    ![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][18]

16. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten. 

    ![Geben Sie Ihre E-Mail-Adresse ein.][19]

17. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen. Ihre Konfiguration testen können, finden Sie weiter unten im Abschnitt [Zuweisen von Benutzern zu Salesforce](#step-4-assign-users-to-salesforce).

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

1. Klicken Sie auf der Seite Azure AD-Schnellstart für Salesforce die **Benutzerbereitstellung** Schaltfläche.

    ![Klicken Sie auf die Schaltfläche "Benutzerbereitstellung konfigurieren"][20]

2. In der **Benutzerbereitstellung** Dialogfeld Ihre Salesforce-Administratorbenutzername und ein Kennwort eingeben.

    ![Geben Sie Ihren Administratorbenutzernamen oder Ihr Kennwort ein.][21]

    > [AZURE.NOTE] Wenn Sie eine produktionsumgebung konfigurieren, ist die bewährte Methode erstellen Sie ein neues Administratorkonto in Salesforce speziell für diesen Schritt. Dieses Konto müssen die **Systemadministrator** Profil in Salesforce zugewiesen.

3. Um Ihr Salesforce-Sicherheitstoken abzurufen, öffnen Sie eine neue Registerkarte, und melden Sie sich mit dem gleichen Salesforce-Administratorkonto an. In der oberen rechten Ecke der Seite, klicken Sie auf Ihren Namen, und klicken Sie dann auf **Meine Einstellungen**.

    ![Klicken Sie auf Ihren Namen und dann auf "Meine Einstellungen"][22]

4. Klicken Sie im linken Navigationsbereich auf **Persönliche** erweitern den entsprechenden Bereich, und klicken Sie dann auf **Mein Sicherheitstoken zurücksetzen**.

    ![Klicken Sie auf Ihren Namen und dann auf "Meine Einstellungen"][23]

5. Auf der **Mein Sicherheitstoken zurücksetzen** Seite, klicken Sie auf die **Sicherheitstoken zurücksetzen** Schaltfläche.

    ![Lesen Sie die Warnungen.][24]

6. Überprüfen Sie den E-Mail-Posteingang dieses Administratorkontos. Achten Sie auf eine E-Mail von Salesforce.com, die das neue Sicherheitstoken enthält.

7. Kopieren Sie das Token, wechseln Sie zu Ihrem Azure AD-Fenster, und fügen Sie ihn in die **Benutzersicherheitstoken** Feld. Klicken Sie dann auf **Weiter**.

    ![Fügen Sie das Sicherheitstoken ein.][25]

8. Auf der Bestätigungsseite können Sie wählen, E-Mail-Benachrichtigungen zu empfangen, wenn bei der Bereitstellung Fehler auftreten. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen.

    ![Geben Sie Ihre E-Mail-Adresse ein, um Benachrichtigungen zu erhalten.][26]

##Schritt 4: Zuweisen von Benutzern zu Salesforce

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite Schnellstart Salesforce auf die **Zuweisen von Benutzern** Schaltfläche.

    ![Klicken Sie auf "Benutzer zuweisen".][27]

3. Wählen Sie Ihren Testbenutzer, und klicken Sie auf die **Zuweisen** am unteren Bildschirmrand auf:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

        ![Confirm the assignment.][28]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Salesforce-Profil der Benutzer erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer Salesforce-Umgebung nach einigen Minuten angezeigt werden.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] If you are provisioning to a Salesforce **developer** environment, you will have a very limited number of licenses available for each profile. Therefore, it's best to provision users to the **Chatter Free User** profile, which has 4,999 licenses available.

4. Um Ihre Einstellungen für einmaliges Anmelden zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), melden Sie das Testkonto an, und klicken Sie auf **Salesforce**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png


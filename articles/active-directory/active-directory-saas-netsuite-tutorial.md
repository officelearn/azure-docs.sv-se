<properties
   pageTitle="Tutorial: Integrieren von NetSuite in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie NetSuite mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und mehr zu aktivieren!"
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

#Tutorial: Integrieren von NetSuite in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre NetSuite-Umgebung mit Ihrem Azure Active Directory (Azure AD) verbinden. Sie erfahren, wie Sie die einmalige Anmeldung bei NetSuite konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf NetSuite zuweisen. 

##Voraussetzungen

1. Azure Active Directory über Zugriff auf die [Azure-Verwaltungsportal](https://manage.windowsazure.com), benötigen Sie ein gültiges Azure-Abonnement.

2. Sie benötigen Administratorzugriff auf eine [NetSuite](http://www.netsuite.com/portal/home.shtml) Abonnement. Sie können ein kostenloses Testkonto für einen der Dienste verwenden.

##Schritt 1: Hinzufügen von NetSuite zu Ihrem Verzeichnis

1. In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken Sie im linken Navigationsbereich auf **Active Directory**.

    ![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis, das Sie NetSuite hinzufügen möchten.

3. Klicken Sie auf **Anwendungen** im oberen Menü.

    ![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. In der **Suchfeld**, Typ **NetSuite**. Wählen Sie dann **NetSuite** aus den Ergebnissen, und klicken Sie auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Fügen Sie NetSuite hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für NetSuite sehen:

    ![Seite "Schnellstart" für NetSuite in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für NetSuite auf die **einmaliges Anmelden konfigurieren** Schaltfläche.

    ![Schaltfläche zum Konfigurieren der einmaligen Anmeldung][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei NetSuite anmelden?" Wählen Sie **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

    > [AZURE.NOTE] Weitere Informationen zu den verschiedenen einmaligen Anmelden für Optionen zu [Klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Auf der **App-Einstellungen konfigurieren** Seite für die **-Antwort-URL** geben Ihre NetSuite-Mandanten-URL mit einem der folgenden Formate:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Geben Sie Ihre Mandanten-URL ein.][8]

4. Auf der **einmaliges Anmelden konfigurieren, um NetSuite** Seite, klicken Sie auf **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Laden Sie die Metadaten herunter.][9]

5. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netsuite-Unternehmenswebsite als Administrator an.

6. Klicken Sie in der Symbolleiste am oberen Rand der Seite auf **Setup**, klicken Sie dann auf **Installations-Manager**.

    ![Wechseln Sie zum Setup-Manager.][10]

7. Aus der **Setupaufgaben** Liste **Integration**.

    ![Wechseln Sie zur Integration.][11]

8. In der **Authentifizierung verwalten** auf **SAML Single Sign-On**.

    ![Wechseln Sie zur einmaligen Anmeldung für SAML.][12]

9. Auf der **SAML-Setup** führen die folgenden Schritte aus:

    - Kopieren Sie in Azure Active Directory, die **Remote-Anmelde-URL** Wert, und fügen Sie ihn in die **Anmeldeseite für Identitätsanbieter** in NetSuite Feld.

        ![Die SAML-Setup-Seite.][13]

    - Wählen Sie in NetSuite **primäre Authentifizierungsmethode**.

    - Für das Feld mit der Bezeichnung **SAMLV2-Identitätsanbietermetadaten**, auf **IDP-Metadatendatei hochladen**. Klicken Sie dann auf **Durchsuchen** zum Hochladen der Metadatendatei, die Sie in Schritt 4 # heruntergeladen.

        ![Laden Sie die Metadaten hoch.][16]

    - Klicken Sie auf **übermitteln**.

9. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das Zertifikat zu aktivieren, das Sie nach NetSuite hochgeladen haben. Klicken Sie dann auf **Weiter**.

    ![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][14]

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten. 

    ![Geben Sie Ihre E-Mail-Adresse ein.][15]

11. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen. Klicken Sie anschließend auf **Attribute** am oberen Rand der Seite.

    ![Klicken Sie auf Attribute.][17]

12. Klicken Sie auf **Benutzerattribut hinzufügen**.

    ![Klicken Sie auf "Benutzerattribut hinzufügen".][18]

13. Für die **Attributnamen** Geben `account`. Für die **Attributwert** Feld, geben Sie Ihre Konto-ID für NetSuite Beachten Sie die folgenden Anleitungen, um Ihre Konto-ID zu finden:

    ![Fügen Sie Ihre Konto-ID von NetSuite hinzu.][19]

    - Klicken Sie in NetSuite auf **Setup** im oberen Navigationsmenü.
    - Klicken Sie dann unter den **Setupaufgaben** Abschnitt im linken Navigationsmenü, wählen die **Integration** Abschnitt, und klicken Sie auf **Webdiensteeinstellungen**.
    - Kopieren Sie Ihre Konto-ID für NetSuite, und fügen Sie ihn in die **Attributwert** Feld in Azure AD.

        ![Rufen Sie Ihre Konto-ID ab.][20]

14. Klicken Sie in Azure AD auf **abgeschlossen** auf das SAML-Attribut hinzugefügt haben. Klicken Sie dann auf **Änderungen übernehmen** im unteren Menü.

    ![Speichern Sie die Änderungen.][21]

15. Bevor Benutzer die einmalige Anmeldung in NetSuite ausführen können, müssen ihnen zunächst die entsprechenden Berechtigungen in NetSuite zugewiesen werden. Befolgen Sie die folgenden Anweisungen, um diese Berechtigungen zuzuweisen.

    - Klicken Sie auf der oberen Navigationsmenü auf **Setup**, klicken Sie dann auf **Installations-Manager**.

        ![Wechseln Sie zum Setup-Manager.][10]

    - Wählen Sie im linken Navigationsmenü **Benutzer/Rollen**, klicken Sie dann auf **Rollen verwalten**.

        ![Wechseln Sie zum Verwalten von Rollen.][22]

    - Klicken Sie auf **neue Rolle**.

    - Geben Sie in einer **Namen** für die neue Rolle, und wählen Sie die **einmaliges Anmelden nur** Kontrollkästchen.

        ![Benennen Sie die neue Rolle.][23]

    - Klicken Sie auf **Speichern**.

    - Klicken Sie im Menü am oberen Rand **Berechtigungen**. Klicken Sie dann auf **Setup**.

        ![Wechseln Sie zu Berechtigungen.][24]

    - Wählen Sie **Festlegen von SAM Single Sign-On**, und klicken Sie dann auf **Hinzufügen**.

    - Klicken Sie auf **Speichern**.

    - Klicken Sie auf der oberen Navigationsmenü auf **Setup**, klicken Sie dann auf **Installations-Manager**.

        ![Wechseln Sie zum Setup-Manager.][10]

    - Wählen Sie im linken Navigationsmenü **Benutzer/Rollen**, klicken Sie dann auf **Benutzer verwalten**.

        ![Wechseln Sie zum Verwalten von Benutzern.][25]

    - Wählen Sie einen Testbenutzer. Klicken Sie dann auf **Bearbeiten**.

        ![Wechseln Sie zum Verwalten von Benutzern.][26]

    - Wählen Sie im Dialogfeld "Rollen" die Rolle, die Sie erstellt haben, und klicken Sie auf **Hinzufügen**.

        ![Wechseln Sie zum Verwalten von Benutzern.][27]

    - Klicken Sie auf **Speichern**.

19. Ihre Konfiguration testen können, finden Sie weiter unten im Abschnitt [Zuweisen von Benutzern zu NetSuite](#step-4-assign-users-to-netsuite).

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

> [AZURE.NOTE] Standardmäßig werden der Stamm-Niederlassung Ihrer NetSuite-Umgebung bereitgestellten Benutzer hinzugefügt werden.

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für NetSuite auf **Benutzerbereitstellung**.

    ![Konfigurieren der Benutzerbereitstellung][28]

2. Im Dialogfeld, das geöffnet wird, geben Sie die Administratoranmeldeinformationen für NetSuite, und klicken Sie dann **Weiter**.

    ![Geben Sie Ihre NetSuite-Admin-Anmeldeinformationen ein.][29]

3. Geben Sie auf der Bestätigungsseite Ihre E-Mail-Adresse ein, um Benachrichtigungen über Fehler bei der Bereitstellung zu erhalten.

    ![Bestätigen Sie den Vorgang.][30]

4. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen.

##Schritt 4: Zuweisen von Benutzern zu NetSuite

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite Schnellstart NetSuite auf die **Zuweisen von Benutzern** Schaltfläche.

    ![Klicken Sie auf "Benutzer zuweisen".][31]

3. Wählen Sie Ihren Testbenutzer, und klicken Sie auf die **Zuweisen** am unteren Bildschirmrand auf:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

        ![Confirm the assignment.][32]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Rolle der Benutzer in NetSuite erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer NetSuite-Umgebung nach einigen Minuten angezeigt werden.

4. Um Ihre Einstellungen für einmaliges Anmelden zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), Testkonto anmelden, und klicken Sie auf **NetSuite**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png



<properties
   pageTitle="Tutorial: Integrieren von Google Apps in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie Google Apps mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und mehr zu aktivieren!"
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
   ms.date="11/01/2015"
   ms.author="liviodlc"/>

#Tutorial: Integrieren von Google Apps in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Google Apps-Umgebung mit Ihrem Azure Active Directory (Azure AD) verbinden. Sie lernen, wie Sie die einmalige Anmeldung bei Google Apps konfigurieren, die automatisierte Benutzerbereitstellung aktivieren und Benutzern den Zugriff auf Google Apps zuweisen. 

##Voraussetzungen

1. Azure Active Directory über Zugriff auf die [Azure-Verwaltungsportal](https://manage.windowsazure.com), benötigen Sie ein gültiges Azure-Abonnement.

2. Sie müssen einen gültigen Mandanten für [Google Apps for Work](https://www.google.com/work/apps/) oder [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Sie können ein kostenloses Testkonto für einen der Dienste verwenden.

##Videotutorial

So aktivieren Sie das einmalige Anmelden bei Google Apps in zwei Minuten

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##Schritt 1: Hinzufügen von Google Apps zu Ihrem Verzeichnis

1. In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken Sie im linken Navigationsbereich auf **Active Directory**.

    ![Wählen Sie im linken Navigationsbereich "Active Directory".][0]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis, das Sie Google Apps hinzufügen möchten.

3. Klicken Sie auf **Anwendungen** im oberen Menü.

    ![Klicken Sie auf "Anwendungen".][1]

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Klicken Sie auf "Hinzufügen", um eine neue Anwendung hinzufügen.][2]

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Klicken Sie auf "Anwendung aus dem Katalog hinzufügen".][3]

6. In der **Suchfeld**, Typ **Google Apps**. Wählen Sie dann **Google Apps** aus den Ergebnissen, und klicken Sie auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Fügen Sie Google Apps hinzu.][4]

7. Sie sollten jetzt die Seite "Schnellstart" für Google Apps sehen:

    ![Seite "Schnellstart" für Google Apps in Azure AD][5]

##Schritt 2: Aktivieren der einmaligen Anmeldung

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für Google Apps, auf die **einmaliges Anmelden konfigurieren** Schaltfläche.

    ![Schaltfläche zum Konfigurieren der einmaligen Anmeldung][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Google Apps anmelden?" Wählen Sie **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

    > [AZURE.NOTE] Weitere Informationen zu den verschiedenen einmaligen Anmelden für Optionen zu [Klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Auf der **App-Einstellungen konfigurieren** Seite für die **-Anmelde-URL** geben Ihre Google Apps-Mandanten-URL im folgenden Format: `https://mail.google.com/a/<yourdomain>`

    ![Geben Sie Ihre Mandanten-URL ein.][8]

4. Auf der **automatisch konfigurieren Sie einmaliges Anmelden für** Geben Sie in der Domäne für Ihren Google Apps-Mandanten. Drücken Sie dann die **konfigurieren** Schaltfläche.

    ![Geben Sie Ihren Domänennamen ein, und klicken Sie auf „Konfigurieren“.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

    > [AZURE.NOTE] Wenn Sie einmaliges Anmelden manuell konfigurieren möchten, finden Sie unter [Optionaler Schritt: manuell konfigurieren Sie einmaliges Anmelden](#optional-step-manually-configure-single-sign-on)

5. Melden Sie sich mit Ihrem Google Apps-Administratorkonto an. Klicken Sie dann auf **Zulassen** um Azure Active Directory für Configuration Änderungen an Ihrem Google Apps-Abonnement zulässig.

    ![Geben Sie Ihren Domänennamen ein, und klicken Sie auf „Konfigurieren“.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. Warten Sie einige Sekunden, bis Azure Active Directory Ihren Google Apps-Mandanten konfiguriert hat. Sobald der Vorgang abgeschlossen ist, klicken Sie auf **Weiter**.

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten.

    ![Geben Sie Ihre E-Mail-Adresse ein.][14]

11. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen. Ihre Konfiguration testen können, finden Sie weiter unten im Abschnitt [Zuweisen von Benutzern zu Google Apps](#step-4-assign-users-to-google-apps).

##Optionaler Schritt: Manuelles Konfigurieren des einmaligen Anmeldens

Wenn Sie das einmalige Anmelden lieber manuell einrichten möchten, führen Sie die folgenden Schritte aus:

1. Klicken Sie in Azure AD auf der Seite "Schnellstart" für Google Apps, auf die **einmaliges Anmelden konfigurieren** Schaltfläche.

    ![Schaltfläche "Einmaliges Anmelden konfigurieren"][6]

2. Ein Dialogfeld wird geöffnet, und Sie sehen einen Bildschirm mit der Frage: "Wie sollen sich Benutzer bei Google Apps anmelden?" Wählen Sie **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Wählen Sie "Azure AD – einmaliges Anmelden".][7]

    > [AZURE.NOTE] Weitere Informationen zu den verschiedenen einmaligen Anmelden für Optionen zu [Klicken Sie hier](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Auf der **App-Einstellungen konfigurieren** Seite für die **-Anmelde-URL** geben Ihre Google Apps-Mandanten-URL im folgenden Format: `https://mail.google.com/a/<yourdomain>`

    ![Geben Sie Ihre Mandanten-URL ein.][8]

4. Auf der **automatisch konfigurieren Sie einmaliges Anmelden** Seite, aktivieren Sie das Kontrollkästchen mit der Bezeichnung **manuell konfigurieren, diese Anwendung für einmaliges Anmelden für**. Klicken Sie dann auf **Weiter**.

    ![Wählen Sie die manuelle Konfiguration aus.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

4. Auf der **einmaliges Anmelden bei Google Apps konfigurieren** Seite, klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Laden Sie das Zertifikat herunter.][9]

5. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei der [Google Apps-Verwaltungskonsole](http://admin.google.com/) mit Ihrem Administratorkonto an.

6. Klicken Sie auf **Security**. Wenn der Link nicht angezeigt wird, kann es unter ausgeblendet werden die **Weitere Steuerelemente** Menü am unteren Bildschirmrand.

    ![Klicken Sie auf "Sicherheit".][10]

7. Auf der **Sicherheit** auf **einmaliges Anmelden (SSO) einrichten.**

    ![Klicken Sie auf "SSO".][11]

8. Führen Sie die folgenden Konfigurationsänderungen durch:

    ![Konfigurieren von SSO][12]

    - Wählen Sie **SSO mit externem Identitätsanbieter**.

    - Kopieren Sie in Azure AD die **SSO-Dienst-URL für einmaliges**, und fügen Sie ihn in der **-in-Seiten-URL** Feld in Google Apps.

    - Kopieren Sie in Azure AD die **einmaliges Abmelden Dienst-URL**, und fügen Sie ihn in die **URL der Abmeldeseite** Feld in Google Apps.

    - Kopieren Sie in Azure AD die **URL für kennwortänderung**, und fügen Sie ihn in die **Kennwort-URL ändern** Feld in Google Apps.

    - In Google Apps für die **Verifizierungszertifikat**, Hochladen des Zertifikats, das Sie in Schritt 4 # heruntergeladen haben.

    - Klicken Sie auf **Speichern**.

9. Aktivieren Sie in Azure AD das Kontrollkästchen zur Bestätigung der Konfiguration der einmaligen Anmeldung, um das in Google Apps hochgeladene Zertifikat zu aktivieren. Klicken Sie dann auf **Weiter**.

    ![Aktivieren Sie das Kontrollkästchen zur Bestätigung.][13]

10. Geben Sie auf der letzten Seite des Dialogfelds eine E-Mail-Adresse ein, wenn Sie per E-Mail Fehler- und Warnmeldungen im Zusammenhang mit der Wartung dieser Konfiguration für die einmalige Anmeldung erhalten möchten. 

    ![Geben Sie Ihre E-Mail-Adresse ein.][14]

11. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen. Ihre Konfiguration testen können, finden Sie weiter unten im Abschnitt [Zuweisen von Benutzern zu Google Apps](#step-4-assign-users-to-google-apps).

##Schritt 3: Aktivieren der automatisierten Benutzerbereitstellung

> [AZURE.NOTE] Eine weitere Option für die Automatisierung der benutzerbereitstellung in Google Apps ist die Verwendung [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) der Ihrer lokalen Active Directory-Identitäten für Google Apps bereitstellt. Im Gegensatz dazu stellt die Lösung in diesem Tutorial Ihre Azure Active Directory-Benutzer (Cloud) und E-Mail-aktivierten Gruppen für Google Apps bereit.

1. Melden Sie sich bei der [Google Apps-Verwaltungskonsole](http://admin.google.com/) mithilfe von Ihrem Administratorkonto an, und klicken Sie auf **Security**. Wenn der Link nicht angezeigt wird, kann es unter ausgeblendet werden die **Weitere Steuerelemente** Menü am unteren Bildschirmrand.

    ![Klicken Sie auf "Sicherheit".][10]

2. Auf der **Sicherheit** auf **-API-Referenz**.

    ![Klicken Sie auf "API-Referenz".][15]

3. Wählen Sie **API-Zugriff aktivieren**.

    ![Klicken Sie auf "API-Referenz".][16]

    > [AZURE.IMPORTANT] Für jeden Benutzer, die Sie für die Bereitstellung auf ihren Benutzernamen in Azure Active Directory Google Apps möchten *müssen* zu einer benutzerdefinierten Domäne gebunden werden. Z. B. Benutzernamen, bei denen es bob@contoso.onmicrosoft.com von Google Apps nicht akzeptiert werden, wohingegen bob@contoso.com akzeptiert werden. Sie können die Domäne eines vorhandenen Benutzers ändern, indem Sie seine Eigenschaften in Azure AD bearbeiten. Anweisungen zum Festlegen einer benutzerdefinierten Domäne für Azure Active Directory und Google Apps sind unten aufgeführt.

4. Wenn Sie Ihrem Azure Active Directory noch keinen benutzerdefinierten Domänennamen hinzugefügt haben, führen Sie die folgenden Schritte aus:

    - In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken Sie im linken Navigationsbereich auf **Active Directory**. Wählen Sie in der Verzeichnisliste Ihr Verzeichnis aus. 

    - Klicken Sie auf **Domänen** aus Menü der obersten Ebene, und klicken Sie dann auf **Hinzufügen einer benutzerdefinierten Domäne**.

        ![Hinzufügen einer benutzerdefinierten Domäne][17]

    - Geben Sie Ihren Domänennamen in der **Domänenname** Feld. Dies sollte der gleiche Domänenname sein, den Sie für Google Apps verwenden möchten. Wenn Sie fertig sind, klicken Sie auf die **Hinzufügen** Schaltfläche.

        ![Geben Sie Ihren Domänennamen ein.][18]

    - Klicken Sie auf **Weiter** um zur Überprüfungsseite zu wechseln. Um sicherzustellen, dass Sie diese Domäne besitzen, müssen Sie die DNS-Einträge der Domäne gemäß der Werte bearbeiten, die auf dieser Seite bereitstehen. Wahlweise können Sie überprüfen, verwenden entweder **MX-Einträge** oder **TXT-Einträgen**, je nachdem, was Sie zum Auswählen der **Datensatztyp** Option. Umfassende Anweisungen zum Überprüfen von Domänennamen mit Azure AD finden Sie [Fügen Sie Ihren eigenen Domänennamen in Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

        ![Überprüfen Sie Ihren Domänennamen.][19]

    - Wiederholen Sie die obigen Schritte für alle Domänen, die Sie Ihrem Verzeichnis hinzufügen möchten.

5. Da Sie jetzt alle Ihre Domänen mit Azure AD überprüft haben, müssen Sie sie erneut mit Google Apps überprüfen. Führen Sie für jede Domäne, die noch nicht für Google Apps registriert ist, die folgenden Schritte aus:

    - In der [Google Apps-Verwaltungskonsole](http://admin.google.com/), klicken Sie auf **Domänen**.

        ![Klicken Sie auf "Domänen"][20]

    - Klicken Sie auf **Hinzufügen einer Domäne oder Domänenalias**.

        ![Hinzufügen einer neuen Domäne][21]

    - Wählen Sie **einer anderen Domäne hinzufügen**, und geben Sie den Namen der Domäne, die Sie hinzufügen möchten.

        ![Geben Sie Ihren Domänennamen ein][22]

    - Klicken Sie auf **Weiter und domänenbesitzrecht**. Führen Sie dann die Schritte aus, mit denen Sie prüfen, ob Sie den Domänennamen besitzen. Umfassende Anweisungen zum Überprüfen Ihrer Domäne mit Google Apps, finden Sie unter [Überprüfen des websitebesitzrechts mit Google Apps](https://support.google.com/webmasters/answer/35179).

    - Wiederholen Sie die obigen Schritte für alle weiteren Domänen, die Sie zu Google Apps hinzufügen möchten.

    > [AZURE.WARNING] Wenn Sie die primäre Domäne für Ihre Google Apps-Mandanten ändern, und falls Sie noch einmaliges Anmelden mit Azure AD konfiguriert, müssen, wiederholen Schritt 3 # unter [Schritt 2: Aktivieren Sie einmaliges Anmelden für](#step-two-enable-single-sign-on).

6. In der [Google Apps-Verwaltungskonsole](http://admin.google.com/), klicken Sie auf **Administratorrollen**.

    ![Klicken Sie auf "Google Apps".][26]

7. Ermitteln Sie, welches Administratorkonto Sie zum Verwalten der Benutzerbereitstellung verwenden möchten. Für die **Administratorrolle** Bearbeiten des Kontos, das **Berechtigungen** für diese Rolle. Stellen Sie sicher, dass alle von der **Admin-API-Berechtigungen** aktiviert, sodass dieses Konto für die Bereitstellung verwendet werden kann.

    ![Klicken Sie auf "Google Apps".][27]

    > [AZURE.NOTE] Wenn Sie eine produktionsumgebung konfigurieren, ist die bewährte Methode erstellen Sie ein neues Administratorkonto in Google Apps speziell für diesen Schritt. Diesem Konto muss eine Administratorrolle zugeordnet sein, die über die erforderlichen API-Berechtigungen verfügt.

8. Klicken Sie in Azure Active Directory auf **Applikationen** im Menü der obersten Ebene, und klicken Sie dann auf **Google Apps**.

    ![Klicken Sie auf "Google Apps".][23]

9. Klicken Sie auf der Seite "Schnellstart" für Google Apps, auf **Benutzerbereitstellung**.

    ![Konfigurieren der Benutzerbereitstellung][24]

10. Das Dialogfeld, das geöffnet wird, klicken Sie auf **benutzerbereitstellung** in Google Apps-Administratorkonto zu authentifizieren, die Sie zum Verwalten der Bereitstellung verwenden möchten.

    ![Aktivieren der Bereitstellung][25]

11. Bestätigen Sie, dass Sie Azure Active Directory die Berechtigung erteilen möchten, Änderungen an Ihrem Google Apps-Mandanten vorzunehmen.

    ![Überprüfen Sie die Berechtigungen.][28]

12. Klicken Sie auf **abgeschlossen** um das Dialogfeld zu schließen.

##Schritt 4: Zuweisen von Benutzern zu Google Apps

1. Um Ihre Konfiguration zu testen, beginnen Sie mit dem Erstellen eines neuen Testkontos im Verzeichnis.

2. Klicken Sie auf der Seite Google Apps-Schnellstart auf die **Zuweisen von Benutzern** Schaltfläche.

    ![Klicken Sie auf "Benutzer zuweisen".][29]

3. Wählen Sie Ihren Testbenutzer, und klicken Sie auf die **Zuweisen** am unteren Bildschirmrand auf:

 - Wenn Sie die automatisierte Benutzerbereitstellung nicht aktiviert haben, werden Sie aufgefordert, Folgendes zu bestätigen:

        ![Confirm the assignment.][30]

 - Wenn Sie die automatisierte Benutzerbereitstellung aktiviert haben, werden Sie aufgefordert, zu definieren, welche Art von Rolle der Benutzer in Google Apps erhalten soll. Neu bereitgestellte Benutzer sollten in Ihrer Google Apps-Umgebung nach einigen Minuten angezeigt werden.

4. Um Ihre Einstellungen für einmaliges Anmelden zu testen, öffnen Sie den Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/), melden Sie das Testkonto an, und klicken Sie auf **Google Apps**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png



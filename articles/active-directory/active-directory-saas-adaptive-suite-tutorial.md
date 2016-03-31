<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Adaptive Suite | Microsoft Azure"
    description="Erfahren Sie, wie Sie Adaptive Suite mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Adaptive Suite

In diesem Lernprogramm wird die Integration von Azure und Adaptive Suite erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Adaptive Suite-Mandanten

Nach Abschluss dieses Lernprogramms Adaptive Suite zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Adaptive Suite-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Adaptive Suite
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenario")
##Aktivieren der Anwendungsintegration für Adaptive Suite

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Adaptive Suite aktivieren.

###So aktivieren Sie die Anwendungsintegration für Adaptive Suite

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Adaptive Suite**.

    ![Anwendungskatalog](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Adaptive Suite**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Adaptive Suite zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Adaptive Suite müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Adaptive Suite** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden, Adaptive Suite** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** auf der Seite der **Antwort-URL** Textfeld Ihre URL nach dem Muster "*Https://login.adaptiveinsights.com:443/Samlsso/RlJFRVRSSUFMMTI3MTE =*", und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE] Sie können diesen Wert abrufen, der Adaptive Suite **SAML-SSO-Einstellungen** Seite.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configure App Settings")

4.  Auf der **Adaptive Suite einmaliges Anmelden konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Adaptive Suite-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  In der **Benutzer und Rollen** auf **SAML-SSO-Einstellungen verwalten**.

    ![Manage SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")

8.  Auf der **SAML-SSO-Einstellungen** führen die folgenden Schritte aus:

    ![SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")

    1.  In der **identitätsanbietername** Textfeld Geben Sie einen Namen für Ihre Konfiguration.
    2.  In Azure-Portal auf der **Adaptive Suite einmaliges Anmelden konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter Entitäts-ID** Textfeld.
    3.  In Azure-Portal auf der **Adaptive Suite einmaliges Anmelden konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter SSO-URL** Textfeld.
    4.  In Azure-Portal auf der **Adaptive Suite einmaliges Anmelden konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL für benutzerdefinierten** Textfeld.
    5.  Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Choose File**.
    6.  Als **SAML-Benutzer-Id**, Option **Adaptive Insights-Benutzername des Benutzers**.
    7.  Als **SAML User Id locationden**, auf **Benutzer-Id in der Betreffzeile NameID**.
    8.  Als **SAML NameID Format**, Option **e-Mail-Adresse**.
    9.  Als **Enable SAML**, Option **SAML-SSO ermöglichen und eine direkte Adaptive Insights Anmeldung**.
    10. Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Adaptive Suite anmelden können, müssen sie in Adaptive Suite bereitgestellt werden.  
Im Fall von Adaptive Suite ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Adaptive Suite** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Admin**.

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  In der **Benutzer und Rollen** auf **Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Add User")

4.  In der **neue Benutzer** führen die folgenden Schritte aus:

    ![Senden](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Submit")

    1.  Typ der **Namen**, **Anmeldung**, **E-Mail**, **Kennwort** eines gültigen Azure Active Directory-Benutzers, die Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie eine **Rolle**.
    3.  Klicken Sie auf **übermitteln**.

>[AZURE.NOTE] Können Sie alle anderen Adaptive Suite Benutzerkonten oder APIs von Adaptive Suite Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Adaptive Suite Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Adaptive Suite ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



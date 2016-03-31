<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Thirdlight | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Thirdlight mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Thirdlight
  
In diesem Tutorial wird die Integration von Azure und Thirdlight erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Thirdlight-Software-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Thirdlight zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Thirdlight-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Thirdlight
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scenario")

##Aktivieren der Anwendungsintegration für Thirdlight
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Thirdlight aktivieren.

###So aktivieren Sie die Anwendungsintegration für Thirdlight:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Thirdlight**.

    ![Anwendungskatalog](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Thirdlight**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Thirdlight zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Thirdlight müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Thirdlight** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden Thirdlight** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Thirdlight-Anmelde-URL** Textfeld verwendete URL von den Benutzern zur Anmeldung bei der Thirdlight-Anwendung (z. B.: "*http://azuresso2.thirdlight.com/*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configure App URL")

4.  Auf der **Thirdlight einmaliges Anmelden konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Thirdlight-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Konfiguration \ > Systemadministration**, und klicken Sie dann auf **SAML2**.

    ![Systemadministration](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "System Administration")

7.  Führen Sie im Abschnitt „SAML2-Konfiguration“ die folgenden Schritte aus:

    ![SAML Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Wählen Sie **SAML2 einmaliges Anmelden aktivieren**.
    2.  Als **Quelle für IdP-Metadaten**, Option **IdP-Metadaten von XML laden**.
    3.  Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt und fügen Sie ihn in die **IdP-Metadaten-XML** Textfeld.
    4.  Klicken Sie auf **Speichern SAML2 Einstellungen**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Thirdlight anmelden können, müssen sie in Thirdlight bereitgestellt werden.  
Im Fall von Thirdlight ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Thirdlight** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Benutzer** Registerkarte.

3.  Wählen Sie **Benutzer und Gruppen**.

4.  Klicken Sie auf **Hinzufügen eines neuen Benutzers** Schaltfläche.

5.  Geben Sie **den Benutzernamen, den Namen oder die Beschreibung, E-Mail, wählen Sie eine Voreinstellung oder der Gruppe neue Mitglieder** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.

6.  Klicken Sie auf **Erstellen**.

>[AZURE.NOTE] Können Sie alle anderen Thirdlight Benutzerkonten oder APIs von Thirdlight Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Thirdlight Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Thirdlight ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


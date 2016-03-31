<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Tinfoil Security | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie Tinfoil Security mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Tinfoil Security
  
In diesem Tutorial wird die Integration von Azure und Tinfoil Security erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Tinfoil Security-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Tinfoil Security zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Tinfoil Security-Unternehmenswebsite (Identity Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Tinfoil Security
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configure Single Sign-On")

##Aktivieren der Anwendungsintegration für Tinfoil Security
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Tinfoil Security aktivieren.

###So aktivieren Sie die Anwendungsintegration für Tinfoil Security:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Tinfoil Security**.

    ![Anwendungskatalog](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Tinfoil Security**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Tinfoil Security zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Tinfoil Security müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Tinfoil Security** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Tinfoil Security** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Tinfoil Security-Antwort-URL** Textfeld Ihre Tinfoil Security Assertion Consumer Service (ACS) URL (z. B.: "*https://www.tinfoilsecurity.com/saml/consume*", und klicken Sie dann auf **Weiter**.

    >[AZURE.NOTE] Sie sollten die ACS-URL von Tinfoil Security-Metadaten (https://www.tinfoilsecurity.com/saml/metadata) abrufen können.

    ![App-URL konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Tinfoil Security konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Tinfoil Security.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Tinfoil Security-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Mein Konto**.

    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Klicken Sie auf **Security**.

    ![Sicherheit](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Security")

8.  Auf der **Single Sign-On** Konfiguration führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")

    1.  Wählen Sie **SAML aktivieren**.
    2.  Klicken Sie auf **Manuelle Konfiguration**.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Tinfoil Security konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **SAML-Post-URL** Textfeld.
    4.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **SAML-Zertifikatfingerabdruck** Textfeld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    5.  Kopie **Ihre Konto-ID**.
    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configure Single Sign-On")

10. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributes")

11. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributes")

    1.  Klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld **Accountid**.
    3.  In der **Attributwert** Textbox, fügen Sie die Konto-ID-Wert Sie im vorherigen Abschnitt kopiert haben.
    4.  Klicken Sie auf **vollständige**.

12. Klicken Sie auf **Ändern**.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Tinfoil Security anmelden können, müssen sie in Tinfoil Security bereitgestellt werden.  
Im Fall von Tinfoil Security ist die Bereitstellung eine manuelle Aufgabe.

###So rufen Sie einen bereitgestellten Benutzer ab:

1.  Wenn der Benutzer einen Teil eines Enterprise-Kontos ist, müssen Sie sich an das Tinfoil Security-Supportteam wenden, um sich das Benutzerkonto anlegen zu lassen.

2.  Wenn der Benutzer ein normaler Tinfoil Security-Benutzer ist, kann der Benutzer zu allen Websites des Benutzers einen Mitarbeiter hinzufügen. Dies löst einen Prozess aus, bei dem eine Einladung an die angegebene E-Mail-Adresse gesendet wird, um ein neues Tinfoil Security-Benutzerkonto anzulegen.

>[AZURE.NOTE] Können Sie alle anderen Tinfoil Security Benutzerkonten oder APIs von Tinfoil Security Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Tinfoil Security Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Tinfoil Security ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


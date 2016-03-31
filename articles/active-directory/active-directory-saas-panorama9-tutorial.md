<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Panorama9 | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Panorama9 mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Panorama9
  
In diesem Tutorial wird die Integration von Azure und Panorama9 erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Panorama9-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Panorama9 zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Panorama9-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Panorama9
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenario")
##Aktivieren der Anwendungsintegration für Panorama9
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Panorama9 aktivieren.

###So aktivieren Sie die Anwendungsintegration für Panorama9

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-panorama9-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-panorama9-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Panorama9**.

    ![Anwendungskatalog](./media/active-directory-saas-panorama9-tutorial/IC790017.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Panorama9**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Panorama9 zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Panorama9 müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Panorama9** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Panorama9 anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Panorama9-Anmelde-URL** Textfeld verwendete URL durch die Benutzer bei Panorama9 anmelden (z. B.: "*https://dashboard.panorama9.com/saml/access/3262*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Panorama9 konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie sie lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Panorama9-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **verwalten**, und klicken Sie dann auf **Extensions**.

    ![Erweiterungen](./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")

7.  Auf der **Extensions** Dialogfeld klicken Sie auf **Single Sign-On**.

    ![Einmaliges Anmelden](./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")

8.  In der **Einstellungen** führen die folgenden Schritte aus:

    ![Einstellungen](./media/active-directory-saas-panorama9-tutorial/IC790025.png "Settings")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Panorama9 konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **-URL des Identitätsanbieters** Textfeld.
    2.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    3.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Panorama9 anmelden können, müssen sie in Panorama9 bereitgestellt werden.  
Im Fall von Panorama9 ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Panorama9** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **verwalten**, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-panorama9-tutorial/IC790027.png "Users")

3.  Klicken Sie auf **+**.

4.  Führen Sie im Abschnitt mit den Benutzerdaten die folgenden Schritte aus:

    ![Benutzer](./media/active-directory-saas-panorama9-tutorial/IC790028.png "Users")

    1.  In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse eines gültigen Azure Active Directory-Benutzers, die Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Können Sie alle anderen Panorama9 Benutzerkonten oder APIs von Panorama9 Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Panorama9 Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Panorama9** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-panorama9-tutorial/IC790029.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-panorama9-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


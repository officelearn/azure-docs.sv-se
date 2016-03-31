<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Picturepark | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Picturepark mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Picturepark
  
In diesem Tutorial wird die Integration von Azure und Picturepark erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Picturepark-Mandanten
  
Nach Abschluss dieses Lernprogramms Picturepark zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Picturepark-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Picturepark
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")
##Aktivieren der Anwendungsintegration für Picturepark
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Picturepark aktivieren.

###So aktivieren Sie die Anwendungsintegration für Picturepark

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Picturepark**.

    ![Anwendungskatalog](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Picturepark**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Picturepark zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Picturepark müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)...

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Picturepark** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Picturepark anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Picturepark-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.picturepark.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Picturepark konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Picturepark-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Verwaltung**, und klicken Sie dann auf **Management Console**.

    ![Verwaltungskonsole](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")

7.  Klicken Sie auf **Authentifizierung**, und klicken Sie dann auf **Identitätsanbieter**.

    ![Authentifizierung](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")

8.  In der **identitätsanbieterkonfiguration** führen die folgenden Schritte aus:

    ![Identitätsanbieterkonfiguration](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Identity provider configuration")

    1.  Klicken Sie auf **Hinzufügen**.
    2.  Geben Sie einen Namen für die Konfiguration ein.
    3.  Wählen Sie **als Standard**.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Picturepark konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **Aussteller-URI** Textfeld.
    5.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Trusted Issuer Thumb Print** Textfeld.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    6.  Klicken Sie auf **JoinDefaultUsersGroup**.
    7.  Festlegen der **Emailaddress** -Attribut in der **Anspruch** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Konfiguration](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
    8.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Picturepark anmelden können, müssen sie in Picturepark bereitgestellt werden.  
Im Fall von Picturepark ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Picturepark** Mandanten.

2.  Klicken Sie in der Symbolleiste am oberen Rand auf **Verwaltung**, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")

3.  In der **Users Overview** auf **Neu**.

    ![Benutzerverwaltung](./media/active-directory-saas-picturepark-tutorial/IC795068.png "User management")

4.  Auf der **Create User** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer erstellen](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")

    1.  Typ der: **e-Mail-Adresse**, **Kennwort**, **Kennwort bestätigen**, **First Name**, **Nachname**, **Unternehmen**, **Land**, **ZIP-**, **City** eines gültigen Azure Active Directory-Benutzers bereitgestellt werden soll in die entsprechenden Textfelder ein.
    2.  Wählen Sie eine **Language**.
    3.  Klicken Sie auf **Erstellen**.

>[AZURE.NOTE]Können Sie alle anderen Picturepark Benutzerkonten oder APIs von Picturepark Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Picturepark Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Picturepark ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


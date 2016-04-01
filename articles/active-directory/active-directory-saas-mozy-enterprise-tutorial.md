<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Mozy Enterprise | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Mozy Enterprise mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Mozy Enterprise
  
In diesem Tutorial wird die Integration von Azure und Mozy Enterprise erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mozy Enterprise-Mandanten
  
Nach Abschluss dieses Lernprogramms Mozy Enterprise zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Mozy Enterprise-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Mozy Enterprise
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")
##Aktivieren der Anwendungsintegration für Mozy Enterprise
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Mozy Enterprise aktivieren.

###So aktivieren Sie die Anwendungsintegration für Mozy Enterprise

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Mozy Enterprise**.

    ![Anwendungskatalog](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Mozy Enterprise**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Mozy Enterprise zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Mozy Enterprise-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Mozy Enterprise** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Mozy Enterprise anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Mozy Enterprise-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. MozyEnterprise.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Mozy Enterprise** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Mozy Enterprise-Unternehmenswebsite als Administrator an.

6.  In der **Konfiguration** auf **Authentifizierungsrichtlinie**.

    ![Authentifizierungsrichtlinie](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")

7.  Auf der **Authentifizierungsrichtlinie** führen die folgenden Schritte aus:

    ![Authentifizierungsrichtlinie](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")

    1.  Wählen Sie **Verzeichnisdienst** als **Anbieter**.
    2.  Wählen Sie **Use LDAP Push**.
    3.  Klicken Sie auf die **SAML-Authentifizierung** Registerkarte.
    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Mozy Enterprise** Kopieren der **Authentifizierungsanforderungs-URL** -Wert aus, und fügen Sie ihn in die **Authentifizierung URL** Textfeld.
    5.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Mozy Enterprise** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **SAML-Endpunkt** Textfeld.
    6.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    7.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie das gesamte Zertifikat in **SAML-Zertifikats** Textfeld.
    8.  Wählen Sie **Enable SSO for Admins, melden Sie sich mit ihren Anmeldeinformationen für das Netzwerk**.
    9.  Klicken Sie auf **Speichern**.

8.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Mozy Enterprise** Dialogfeld auf der Seite Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Mozy Enterprise anmelden können, müssen sie in Mozy Enterprise bereitgestellt werden.  
Im Fall von Mozy Enterprise ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Mozy Enterprise** Mandanten.

2.  Klicken Sie auf **Benutzer**, und klicken Sie dann auf **Add New User**.

    ![Benutzer](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")

    >[AZURE.NOTE]Die **Add New User** Option wird nur angezeigt, wenn **Mozy** ausgewählt ist, wie der Anbieter unter **Authentifizierungsrichtlinie**. Wenn die SAML-Authentifizierung konfiguriert ist, werden die Benutzer automatisch bei ihrer ersten Anmeldung mittels SSO hinzugefügt.

3.  Führen Sie im Dialogfeld „Neuer Benutzer“ die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")

    1.  Aus der **Wählen Sie eine Gruppe** wählen Sie eine Gruppe.
    2.  Aus der **welchen Typ von** Wählen Sie einen Typ.
    3.  In der **Benutzername** Textfeld Geben Sie den Namen des Azure AD-Benutzers.
    4.  In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse des Azure AD-Benutzers.
    5.  Wählen Sie **Send Users Instruction Email**.
    6.  Klicken Sie auf **Hinzufügen von Benutzern**.

    >[AZURE.NOTE]Nach dem Erstellen des Benutzers wird eine e-Mail an den Azure AD-Benutzer gesendet werden, die einen zum Bestätigen des Kontos Link, bevor es aktiviert wird.

>[AZURE.NOTE]Können Sie alle anderen Mozy Enterprise Benutzerkonten oder APIs von Mozy Enterprise Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
 
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Mozy Enterprise Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Mozy Enterprise ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


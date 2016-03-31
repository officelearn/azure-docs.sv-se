<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Chromeriver | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Chromeriver mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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


#Lernprogramm: Azure Active Directory-Integration mit Chromeriver

In diesem Lernprogramm wird die Integration von Azure und Chromeriver erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Chromeriver-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Chromeriver zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Chromeriver-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Chromeriver
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenario")
##Aktivieren der Anwendungsintegration für Chromeriver

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Chromeriver aktivieren.

###So aktivieren Sie die Anwendungsintegration für Chromeriver

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Chromeriver**.

    ![Anwendungskatalog](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Chromeriver**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Chromeriver zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Chromeriver** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Chromeriver anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** führen die folgenden Schritte aus:

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configure App Settings")

    1.  In der **-Antwort-URL** Textfeld Ihre chromeriver- **-AssertionConsumerService-URL** (z. B.: *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  

        >[AZURE.NOTE] Sie können diesen Wert von Ihrem Chromeriver-Supportteam abrufen.

    2.  Klicken Sie auf **Weiter**

4.  Auf der **einmaliges Anmelden konfigurieren, um Chromeriver** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam für Chromeriver.

    >[AZURE.NOTE] Ihr Chromeriver-Supportteam muss die tatsächliche SSO-Konfiguration zu übernehmen.  
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Chromeriver anmelden können, müssen sie in Chromeriver bereitgestellt werden.  
Im Fall von Chromeriver müssen die Benutzerkonten vom Supportteam von Chromeriver erstellt werden.

>[AZURE.NOTE] Können Sie alle anderen Chromeriver Benutzerkonten oder APIs von Chromeriver für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Chromeriver Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Chromeriver ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



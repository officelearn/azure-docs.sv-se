<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit 15Five | Microsoft Azure" 
    description="Erfahren Sie, wie Sie 15Five mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit 15Five

In diesem Lernprogramm wird die Integration von Azure und 15Five erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein 15Five-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms 15Five zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer 15five-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für 15Five
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")
##Aktivieren der Anwendungsintegration für 15Five

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für 15Five aktivieren.

###So aktivieren Sie die Anwendungsintegration für 15Five

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-15five-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-15five-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **15Five**.

    ![Anwendungskatalog](./media/active-directory-saas-15five-tutorial/IC784668.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **15Five**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei 15Five zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **15Five** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei 15Five anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **15Five-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.15Five.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um 15Five** auf **Metadaten**, und leitet dann die Metadatendatei zum 15five-Supportteam weiter.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")

    >[AZURE.NOTE] Einmaliges Anmelden muss vom Supportteam von 15Five aktiviert werden.

5.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei 15Five anmelden können, müssen sie in 15Five bereitgestellt werden.  
Im Fall von 15Five ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **15Five** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Manage Company**.

    ![Manage Company](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")

3.  Wechseln Sie zu **Personen \ > Hinzufügen von Personen**.

    ![Personen](./media/active-directory-saas-15five-tutorial/IC784676.png "People")

4.  Führen Sie im Abschnitt "Add New Person" die folgenden Schritte aus:

    ![Add New Person](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")

    1.  Typ der **Vorname**, **Nachname**, **Titel**, **e-Mail-Adresse** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Fertig**.

    >[AZURE.NOTE] Die Azure AD-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen 15Five Benutzerkonten oder APIs von 15five-APIs bereitstellen AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie 15Five Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** 15Five ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-15five-tutorial/IC784678.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



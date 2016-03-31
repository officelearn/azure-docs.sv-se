<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Cornerstone OnDemand | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Cornerstone OnDemand mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Cornerstone OnDemand

In diesem Tutorial wird die Integration von Azure und Cornerstone OnDemand erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Cornerstone OnDemand-Mandanten

Nach Abschluss dieses Lernprogramms Cornerstone OnDemand zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Cornerstone OnDemand-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cornerstone OnDemand
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")
##Aktivieren der Anwendungsintegration für Cornerstone OnDemand

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cornerstone OnDemand aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cornerstone OnDemand

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Cornerstone Ondemand**.

    ![Anwendungskatalog](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Cornerstone OnDemand**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cornerstone OnDemand zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Cornerstone OnDemand** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Aktivieren der einmaligen Anmeldung](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Cornerstone OnDemand anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Cornerstone OnDemand-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.csod.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Cornerstone OnDemand** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")

5.  Senden Sie Folgendes an das Supportteam von Cornerstone OnDemand:

    1.  Das heruntergeladene Zertifikat
    2.  Der **Remote-Anmelde-URL** Wert
    3.  Der **Remote-Abmelde-URL** Wert.

    >[AZURE.NOTE] Einmaliges Anmelden muss vom Supportteam von Cornerstone OnDemand konfiguriert werden.
    Sie erhalten eine Benachrichtigung vom Supportteam, wenn die Konfiguration abgeschlossen ist.

6.  Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Um Azure AD-Benutzern die Anmeldung bei Cornerstone OnDemand zu ermöglichen, müssen sie in Cornerstone OnDemand bereitgestellt werden.  
Im Fall von Cornerstone OnDemand ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Senden Sie die Informationen (z. B. Name und E-Mail) für den Azure AD-Benutzer, den Sie bereitstellen möchten, an das Supportteam von Cornerstone OnDemand.

>[AZURE.NOTE] Können Sie alle anderen Cornerstone OnDemand Benutzerkonten oder APIs von Cornerstone OnDemand Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cornerstone OnDemand Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Cornerstone OnDemand ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Benutzer zuweisen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assign Users")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



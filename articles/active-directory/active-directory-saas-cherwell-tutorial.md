<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Cherwell | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Cherwell mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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
    ms.date="10/20/2015" 
    ms.author="markvi" />

#Lernprogramm: Azure Active Directory-Integration mit Cherwell

In diesem Lernprogramm wird die Integration von Azure und Cherwell erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Cherwell-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.

Nach Abschluss dieses Lernprogramms Cherwell zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Cherwell-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cherwell
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario")
##Aktivieren der Anwendungsintegration für Cherwell

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cherwell aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cherwell

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Cherwell**.

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Wählen Sie im Ergebnisbereich **Cherwell**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cherwell zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Cherwell** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Cherwell anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zum Anmelden bei Ihrem **Cherwell** (z. B.: *https://pictdev.cherwellondemand.com/cherwellclient*).
    2.  Klicken Sie auf **Weiter**

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für cherwell** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configure Single Sign-On")

    1.  Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.
    2.  Kopieren der **Identitätsanbieter-URL**.
    3.  Kopieren der **Single Sign-On Service URL**.
    4.  Klicken Sie auf **Weiter**.

5.  Senden Sie das heruntergeladene Zertifikat, das **Identitätsanbieter-URL** und **Single Sign-On-Dienst-URL** an Ihr Cherwell-Supportteam.

    >[AZURE.NOTE] Ihr Cherwell-Supportteam muss die tatsächliche SSO-Konfiguration zu übernehmen.
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Cherwell anmelden können, müssen sie in Cherwell bereitgestellt werden.  
Im Fall von Cherwell müssen die Benutzerkonten vom Supportteam von Cherwell erstellt werden.

>[AZURE.NOTE] Können Sie alle anderen Cherwell Benutzerkonten oder APIs von Cherwell für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cherwell Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Cherwell ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



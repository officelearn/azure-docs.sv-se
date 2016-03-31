<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit FM:Systems | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie FM:Systems mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit FM:Systems
  
In diesem Tutorial wird die Integration von Azure und FM:Systems erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein FM:Systems-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms FM: Systems zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer FM: Systems-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für FM:Systems
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario")
##Aktivieren der Anwendungsintegration für FM:Systems
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für FM:Systems aktivieren.

###So aktivieren Sie die Anwendungsintegration für FM:Systems

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **FM: Systems**.

    ![Anwendungskatalog](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **FM: Systems**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei FM:Systems zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **FM: Systems** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei FM: Systems anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configure App URL")

    1.  In der **FM: Systems-Anmelde-URL** Textfeld Geben Sie Ihre FM: Systems **Antwort-URL** (z. B.: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Sie können diesen Wert abrufen, von Ihrem FM: Systems-Supportteam.

    2.  Klicken Sie auf **Weiter**

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für FM: Systems** Seite, um Ihre Metadaten herunterzuladen, klicken Sie **Metadaten**, und speichern Sie die Metadaten dann auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von FM:Systems.

    >[AZURE.NOTE] Das Supportteam von FM: Systems muss die tatsächliche SSO-Konfiguration.
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei FM:Systems anmelden können, müssen sie in FM:Systems bereitgestellt werden.  
Im Fall von FM:Systems ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich in einem anderen Webbrowserfenster bei der FM:Systems-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Systemadministration \ > Sicherheit verwalten \ > Benutzer \ > Benutzerliste**.

    ![Systemadministration](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "System Administration")

3.  Klicken Sie auf **neuen Benutzer erstellen**.

    ![Neuen Benutzer erstellen](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Create New User")

4.  In der **Create User** führen die folgenden Schritte aus:

    ![Benutzer erstellen](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Create User")

    1.  Geben Sie den Benutzernamen, das Kennwort, die Bestätigung des Kennworts, die E-Mail-Adresse und die Mitarbeiterkennung eines gültigen Azure Active Directory-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Weiter**.

>[AZURE.NOTE] Sie können eine beliebige andere FM: Systems Benutzerkonten verwenden oder APIs von FM: Systems Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie FM:Systems Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** FM: Systems ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


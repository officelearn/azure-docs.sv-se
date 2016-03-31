<properties
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit ABa Sainsburys Connect | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ABa Sainsburys Connect mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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

#Lernprogramm: Azure Active Directory-Integration mit ABa Sainsburys Connect

In diesem Lernprogramm wird die Integration von Azure und ABa Sainsburys Connect erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ABa Sainsburys Connect-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Aba Sainsburys-Verbindung zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Aba Sainsburys-Verbindung-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ABa Sainsburys Connect
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Scenario")
##Aktivieren der Anwendungsintegration für ABa Sainsburys Connect

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ABa Sainsburys Connect aktivieren.

###So aktivieren Sie die Anwendungsintegration für ABa Sainsburys Connect

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Aba Sainsburys-Verbindung**.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "Aba Sainsburys Connect")

7.  Wählen Sie im Ergebnisbereich **Aba Sainsburys-Verbindung**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "Aba Sainsburys Connect")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Aba Sainsburys Connect zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Aba Sainsburys-Verbindung** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Aba Sainsburys-Verbindung anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** führen die folgenden Schritte aus:

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Configure App Settings")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei Ihrer Anwendung Aba Sainsburys-Verbindung (z. B.: *https://myaba.co.uk/client-access/sainsburys/saml.php*).
    2.  Klicken Sie auf **Weiter**

4.  Auf der **einmaliges Anmelden bei Aba Sainsburys-Verbindung konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von Aba Sainsburys Connect.

    >[AZURE.NOTE] Das Supportteam von Aba Sainsburys-Verbindung ist auf die tatsächliche SSO-Konfiguration.
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit Azure AD-Benutzer sich bei Aba Sainsburys Connect anmelden können, müssen sie in Aba Sainsburys Connect bereitgestellt werden.  
Im Fall von Aba Sainsburys Connect müssen die Benutzerkonten vom Supportteam für Aba Sainsburys Connect erstellt werden.

>[AZURE.NOTE] Können Sie alle anderen Aba Sainsburys-Verbindung Benutzerkonten oder APIs von Aba Sainsburys-Verbindung für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Aba Sainsburys Connect Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Aba Sainsburys-Verbindung ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



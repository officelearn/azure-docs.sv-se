<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Clarizen | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Clarizen mit Azure Active Directory verwenden können, um das einmalige Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Clarizen

In diesem Lernprogramm wird die Integration von Azure und Clarizen erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Clarizen-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Clarizen zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Clarizen-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Clarizen
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")
##Aktivieren der Anwendungsintegration für Clarizen

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Clarizen aktivieren.

###So aktivieren Sie die Anwendungsintegration für Clarizen

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Clarizen**.

    ![Anwendungskatalog](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Clarizen**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Clarizen zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Clarizen** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Clarizen anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")

3.  Auf der **einmaliges Anmelden bei Clarizen konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")

4.  Melden Sie sich in einem Webbrowserfenster Ihre **Clarizen** -Unternehmenswebsite als Administrator (z. B.: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Klicken Sie auf Ihren Benutzernamen ein, und klicken Sie dann auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")

6.  Klicken Sie auf die **Globale Einstellungen** Registerkarte, und klicken Sie dann neben **Verbundauthentifizierung**, klicken Sie auf **Bearbeiten**.

    ![Global Settings](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")

7.  Auf der **Verbundauthentifizierung** im Dialogfeld die folgenden Schritte ausführen:

    ![Federated Authentication](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")

    1.  Klicken Sie auf **Hochladen** um das heruntergeladene Zertifikat hochzuladen.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Clarizen konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Anmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Abmelden bei Clarizen** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **URL Abmeldung** Textfeld.
    4.  Wählen Sie **Verwenden Sie POST**.
    5.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Clarizen anmelden können, müssen sie in Clarizen bereitgestellt werden.  
Im Fall von Clarizen ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Clarizen** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **Personen**.

    ![People](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")

3.  Klicken Sie auf **Benutzer einladen**.

    ![Invite Users](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")

4.  Führen Sie auf der Dialogfeldseite "Invite People" die folgenden Schritte aus:

    ![Invite People](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")

    1.  In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **einladen**.

    >[AZURE.NOTE] Der Azure Active Directory-Kontoinhaber wird eine e-Mail erhalten, und befolgen Sie einen Link, um das Konto zu bestätigen, bevor es aktiviert wird.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Clarizen Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Clarizen ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



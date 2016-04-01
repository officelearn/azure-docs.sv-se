<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Aha! | Microsoft Azure" 
    description="Erfahren Sie mehr über das Verwenden von Aha! mit Azure Active Directory Aktivieren der einmaligen Anmeldung, automatisierte Bereitstellung und mehr!" 
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

#Lernprogramm: Azure Active Directory-Integration mit Aha!

In diesem Lernprogramm wird die Integration von Azure und Aha! erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Anwendungsintegrationsseite! Einmaliges Anmelden für Abonnement mit aktivierter

Nach Abschluss dieses Lernprogramms haben Sie die Azure AD-Benutzer AHA zugewiesen! werden einmaligem Anmelden in die Anwendung auf Ihrer Aha! Unternehmens-Website (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Aha!
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")
##Aktivieren der Anwendungsintegration für Aha!

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Aha! aktivieren.

###So aktivieren Sie die Anwendungsintegration für Aha!

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-aha-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-aha-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Aha!**.

    ![Anwendungskatalog](./media/active-directory-saas-aha-tutorial/IC798945.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Aha!**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##Konfigurieren der einmaligen Anmeldung

Die in diesem Abschnitt wird zum Aktivieren von Benutzern bei Aha! mit ihrem Konto in Azure AD mithilfe von Verbund basierend auf dem SAML-Protokoll.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Aha!** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei Aha!** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Aha! Anmelde-URL** den URL ein, den Ihre Benutzer zur Anmeldung bei der Aha!- Anwendung (z. B.: "*https://company.aha.io/session/new*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-aha-tutorial/IC798948.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für AHA!** Seite zum Herunterladen der Metadatendatei, klicken Sie auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")

5.  Ein Webbrowserfenster melden Sie sich in Ihrer Aha! -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")

7.  Klicken Sie auf **Konto**.

    ![Profil](./media/active-directory-saas-aha-tutorial/IC798951.png "Profile")

8.  Klicken Sie auf **Sicherheit und einmaliges Anmelden für**.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")

9.  In **Single Sign-On** Abschnitt als **Identitätsanbieter**, auf **SAML2.0**.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")

10. Auf der **Single Sign-On** Konfiguration führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")

    1.  In den **Namen** Geben einen Namen für Ihre Konfiguration.
    2.  Für **Konfiguration mithilfe von**, auf **Metadatendatei**.
    3.  Um Ihre heruntergeladene Metadatendatei hochzuladen, klicken Sie auf **Durchsuchen**.
    4.  Klicken Sie auf **Update**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Aha! anmelden können, müssen sie in Aha! bereitgestellt werden.  
Im Fall von Aha! ist die Bereitstellung eine automatisierte Aufgabe.  
Für Sie steht kein Aktionselement zur Verfügung.
  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE] Sie können beliebige andere von Aha! Erstellen von Benutzerkonten oder APIs von Aha! AAD-Benutzerkonten bereitstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Aha! Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Anwendungsintegrationsseite! ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-aha-tutorial/IC798956.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



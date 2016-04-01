<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Kintone | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Kintone mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Kintone
  
In diesem Tutorial wird die Integration von Azure und Kintone erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Kintone-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Lernprogramms Kintone zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Kintone-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Kintone
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")
##Aktivieren der Anwendungsintegration für Kintone
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Kintone aktivieren.

###Führen Sie zum Aktivieren der Anwendungsintegration für Kintone die folgenden Schritte aus:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Kintone**.

    ![Anwendungskatalog](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Kintone**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Kintone zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Kintone** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Kintone anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Kintone-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.kintone.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Kintone konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5.  Melden Sie sich in einem Webbrowserfenster Ihre **Kintone** -Unternehmenswebsite als Administrator.

6.  Klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7.  Klicken Sie auf **Benutzer & Systemverwaltung**.

    ![Benutzer und Systemadministration](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8.  Unter **Systemadministration \ > Sicherheit** Klicken Sie auf **Anmeldung**.

    ![Anmeldung](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9.  Klicken Sie auf **SAML-Authentifizierung aktivieren**.

    ![SAML-Authentifizierung](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. Führen Sie im Abschnitt für die SAML-Authentifizierung die folgenden Schritte aus:

    ![SAML-Authentifizierung](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Kintone konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Kintone konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    3.  Klicken Sie auf **Durchsuchen** um das heruntergeladene Zertifikat hochzuladen.
    4.  Klicken Sie auf **Speichern**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Kintone anmelden können, müssen sie in Kintone bereitgestellt werden.  
Im Fall von Kintone ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Kintone** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **Einstellung**.

    ![Einstellungen](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3.  Klicken Sie auf **Benutzer & Systemverwaltung**.

    ![Benutzer und Systemadministration](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4.  Unter **User Administration**, klicken Sie auf **Abteilungen und Benutzer**.

    ![Abteilung und Benutzer](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5.  Klicken Sie auf **Neuer Benutzer**.

    ![Neue Benutzer](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6.  In der **neue Benutzer** führen die folgenden Schritte aus:

    ![Neue Benutzer](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")

    1.  Geben Sie einen **Anzeigenamen**, **Benutzername**, **Neues Kennwort**, **Kennwort bestätigen**, **e-Mail-Adresse** und andere Details eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen Kintone Benutzerkonten oder APIs von Kintone Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Kintone Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Kintone ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


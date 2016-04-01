<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TOPdesk - Secure | Microsoft Azure" description="Hier erfahren Sie, wie Sie TOPdesk - Secure mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit TOPdesk - Secure
  
In diesem Tutorial wird die Integration von Azure und TOPdesk - Secure erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TOPdesk - Secure-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms TOPdesk – Secure zugewiesene Azure AD-Benutzer werden bei der Anwendung auf Ihrer TOPdesk – Secure-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TOPdesk - Secure
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenario")

##Aktivieren der Anwendungsintegration für TOPdesk - Secure
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TOPdesk - Secure aktivieren.

###So aktivieren Sie die Anwendungsintegration für TOPdesk - Secure:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **TOPdesk – Secure**.

    ![Anwendungskatalog](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **TOPdesk – Secure**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TOPdesk - Secure zu authentifizieren.  
Für das Konfigurieren des einmaligen Anmeldens für TOPdesk - Secure müssen Sie eine Logosymboldatei hochladen. Um die Symboldatei zu erhalten, wenden Sie sich an das TOPdesk-Supportteam.

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich auf Ihre **TOPdesk – Secure** -Unternehmenswebsite als Administrator.

2.  In der **TOPdesk** Menü klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

3.  Klicken Sie auf **Anmeldeeinstellungen**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

4.  Erweitern Sie die **Anmeldedaten** Menü, und klicken Sie dann auf **Allgemeine**.

    ![Allgemein](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

5.  In der **Secure** Teil der **SAML Login** Konfiguration führen die folgenden Schritte aus:

    ![Technische Einstellungen](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")

    1.  Klicken Sie auf **herunterladen** an die öffentliche Metadatendatei herunterzuladen, und speichern sie lokal auf Ihrem Computer.
    2.  Öffnen Sie die Metadatendatei, und suchen Sie die **ein** Knoten.
        ![Assertion-Kundenservice](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion Consumer Service")
    3.  Kopieren der **AssertionConsumerService** Wert.  

        >[AZURE.NOTE] Sie benötigen den Wert in der **App-URL konfigurieren** Weiter unten in diesem Lernprogramm.

6.  Melden Sie sich in einem Webbrowserfenster Ihrer **Azure Active Directory** Portal als Administrator.

7.  Auf der **TOPdesk – Secure** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configure Single Sign-On")

8.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei TOPdesk – Secure** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configure Single Sign-On")

9.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configure App URL")

    1.  In der **TOPdesk – Secure-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer TOPdesk – Secure Anwendung anzumelden (z. B.: "*https://qssolutions.topdesk.net*").
    2.  In der **TOPdesk – Public-Antwort-URL** Textfeld einfügen der **TOPdesk – Secure-AssertionConsumerService-URL** (z. B.: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Klicken Sie auf **Weiter**.

10. Auf der **einmaliges Anmelden konfigurieren, um TOPdesk – Secure** Seite zum Herunterladen der Metadatendatei, klicken Sie auf **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configure Single Sign-On")

11. Zum Erstellen einer Zertifikatsdatei führen Sie die folgenden Schritte durch:

    ![Zertifikat](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificate")

    1.  Öffnen Sie die heruntergeladene Metadatendatei.
    2.  Erweitern Sie die **RoleDescriptor** Knoten mit einer **xsi: Type** von **fed: ApplicationServiceType**.
    3.  Kopieren Sie den Wert, der die **X509Certificate** Knoten.
    4.  Speichern Sie den kopierten **X509Certificate** Wert lokal auf Ihrem Computer in einer Datei.

12. Auf der TOPdesk – Secure-Unternehmenswebsite das **TOPdesk** Menü klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

13. Klicken Sie auf **Anmeldeeinstellungen**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

14. Erweitern Sie die **Anmeldedaten** Menü, und klicken Sie dann auf **Allgemeine**.

    ![Allgemein](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

15. In der **öffentlichen** auf **Hinzufügen**.

    ![Hinzufügen](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Add")

16. Auf der **SAML-Konfigurationsassistent** Dialogfeld führen die folgenden Schritte aus:

    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML Configuration Assistant")

    1.  Unter hochladen die heruntergeladenen Metadatendatei, **Verbundmetadaten**, klicken Sie auf **Durchsuchen**.
    2.  Unter Hochladen der Zertifikatsdatei **Zertifikat (RSA)**, klicken Sie auf **Durchsuchen**.
    3.  Die Logodatei hochladen, Sie haben Sie vom TOPdesk-Supportteam unter **Logo-Symbol**, klicken Sie auf **Durchsuchen**.
    4.  In der **Benutzernamenattribut** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  In der **Anzeigenamen** Textfeld Geben Sie einen Namen für Ihre Konfiguration.
    6.  Klicken Sie auf **Speichern**.

17. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TOPdesk - Secure anmelden können, müssen sie in TOPdesk - Secure bereitgestellt werden.  
Im Fall von TOPdesk - Secure ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich auf Ihre **TOPdesk – Secure** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **TOPdesk \ > New \ > Unterstützungsdateien \ > Operator**.

    ![Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operator")

3.  Auf der **Operator New** im Dialogfeld die folgenden Schritte ausführen:

    ![Neuer Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New Operator")

    1.  Klicken Sie auf die Registerkarte „Allgemein“.
    2.  In der **Surname** Textfeld die **Allgemeine** Abschnitt, und geben Sie den Nachnamen eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten.
    3.  Wählen Sie eine **Website** für das Konto in der **Speicherort** Abschnitt.
    4.  In der **Anmeldename** Textfeld die **TOPdesk-Anmeldung** Abschnitt, und geben Sie einen Anmeldenamen für den Benutzer.
    5.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Sie können alle anderen TOPdesk – Secure Benutzerkonten oder APIs von TOPdesk – Secure bereitzustellenden AAD-Benutzerkonten verwenden.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie  TOPdesk - Secure Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** TOPdesk – Secure ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit AppDynamics | Microsoft Azure" 
    description="Erfahren Sie, wie Sie AppDynamics mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit AppDynamics

In diesem Lernprogramm wird die Integration von Azure und AppDynamics erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AppDynamics-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms AppDynamics zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer AppDynamics-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AppDynamics
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")
##Aktivieren der Anwendungsintegration für AppDynamics

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AppDynamics aktivieren.

###So aktivieren Sie die Anwendungsintegration für AppDynamics

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **AppDynamics**.

    ![Anwendungskatalog](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **AppDynamics**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AppDynamics zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **AppDynamics** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")

2.  Auf der **wie sollen sich Benutzer bei AppDynamics anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")

3.  Auf der **App-URL konfigurieren** auf der Seite der **AppDynamics Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei AppDynamics ("*https://companyname.saas.appdynamics.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei AppDynamics konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AppDynamics-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**, und klicken Sie dann auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  Klicken Sie auf die **Authentifizierungsanbieter** Registerkarte.

    ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")

8.  In der **Authentifizierungsanbieter** führen die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")

    1.  Als **Authentifizierungsanbieter**, Option **SAML**.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei AppDynamics konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei AppDynamics konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    4.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    5.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **Zertifikat** Textbox
    6.  Klicken Sie auf **Speichern**.
        ![Speichern](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AppDynamics anmelden können, müssen sie in AppDynamics bereitgestellt werden.  
Im Fall von AppDynamics ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der AppDynamics-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Benutzer**, und klicken Sie dann auf **+** zum Öffnen der **Create User** Dialogfeld.

    ![Benutzer](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")

3.  In der **Create User** führen die folgenden Schritte aus:

    ![Benutzer erstellen](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")

    1.  Typ der **Benutzername**, **Namen**, **E-Mail**, **Neues Kennwort**, **Neues Kennwort wiederholen** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen AppDynamics Benutzerkonten oder APIs von AppDynamics zum Bereitstellen von Azure AD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AppDynamics Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** AppDynamics ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



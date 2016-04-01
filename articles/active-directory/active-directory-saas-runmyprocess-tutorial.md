<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit RunMyProcess | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie RunMyProcess mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit RunMyProcess
  
In diesem Tutorial wird die Integration von Azure und RunMyProcess erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein RunMyProcess-Mandant
  
Nach Abschluss dieses Lernprogramms RunMyProcess zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer RunMyProcess-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für RunMyProcess
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Scenario")
##Aktivieren der Anwendungsintegration für RunMyProcess
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für RunMyProcess aktivieren.

###So aktivieren Sie die Anwendungsintegration für RunMyProcess:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **RunMyProcess**.

    ![Anwendungskatalog](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **RunMyProcess**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei RunMyProcess zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **RunMyProcess** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei RunMyProcess anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **RunMyProcess-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.runmyprocess.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei RunMyProcess konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der RunMyProcess-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Konto \ > Konfiguration**.

    ![Konto](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Account")

7.  Klicken Sie auf die **Authentifizierungsmethode** Registerkarte.

8.  In der **Authentifizierungsmethode** führen die folgenden Schritte aus:

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  Als **Methode**, Option **SSO with Samlv2**.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei RunMyProcess konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **SSO Redirect** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei RunMyProcess konfigurieren** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **Logout Redirect** Textfeld.
    4.  In der **Name Id Format** Textfeld **Urn: Oasis: Namen: Tc: SAML: 1.1 NameID-Format: EmailAddress**.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **Zertifikat** Textbox
    7.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei RunMyProcess anmelden können, müssen sie in RunMyProcess bereitgestellt werden.  
Im Fall von RunMyProcess ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **RunMyProcess** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Konto \ > Benutzer**, und klicken Sie dann auf **Neuer Benutzer**.

    ![Neuer Benutzer](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "New User")

3.  In der **Benutzereinstellungen** führen die folgenden Schritte aus:

    ![Profil](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profile")

    1.  Typ der **Namen** und **E-mail** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie eine **IDE Language**, ein **Language** und einem **Profil**.
    3.  Wählen Sie **Account Creation E-mail an mich senden**.
    4.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE]Können Sie alle anderen RunMyProcess Benutzerkonten oder APIs von RunMyProcess für die Bereitstellung von Azure Active Directory-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie RunMyProcess Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** RunMyProcess ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


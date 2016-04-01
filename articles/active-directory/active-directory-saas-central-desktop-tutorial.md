<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Central Desktop | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Central Desktop mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Central Desktop

In diesem Tutorial wird die Integration von Azure und Central Desktop erläutert. Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Abonnement für Central Desktop mit aktiviertem einmaligen Anmelden und einen Central Desktop-Mandanten

Das in diesem Tutorial beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Central Desktop
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")
##Aktivieren der Anwendungsintegration für Central Desktop

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Central Desktop aktivieren.

###So aktivieren Sie die Anwendungsintegration für Central Desktop

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Central Desktop**.

    ![Anwendungskatalog](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Central Desktop**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Central Desktop zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Central Desktop-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).



###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Central Desktop** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Central Desktop anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**: 

    -   In der **Central Desktop-Anmelde-URL** Textfeld Geben Sie die URL Ihres Central Desktop-Mandanten (z. B.: *http://contoso.centraldesktop.com*).
    -   Geben Sie in das Textfeld Central Desktop-Antwort-URL die URL Ihres Central Desktop ein (z. B.: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Sie erhalten den Wert aus dem central desktop-Metadaten (z. B.: *http://contoso.centraldesktop.com*).

    ![App-URL konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für Central Desktop konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")

5.  Melden Sie sich bei Ihrem **Central Desktop** Mandanten.

6.  Wechseln Sie zu **Einstellungen**, klicken Sie auf **Erweitert**, und klicken Sie dann auf **Single Sign On**.

    ![Setup - Advanced](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")

7.  Auf der **Single Sign in Settings** führen die folgenden Schritte aus:

    ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")

    1.  Wählen Sie **Enable SAML v2 Single Sign On**.
    2.  In Azure AD-Portal auf der **einmaliges Anmelden für Central Desktop konfigurieren** Seite, und kopieren Sie die **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **SSO-URL** Textfeld.
    3.  In Azure AD-Portal auf der **einmaliges Anmelden für Central Desktop konfigurieren** Seite, und kopieren Sie die **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **SSO-Anmelde-URL** Textfeld.
    4.  In Azure AD-Portal auf der **einmaliges Anmelden für Central Desktop konfigurieren** Seite, und kopieren Sie die **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **SSO-Abmelde-URL** Textfeld.

8.  In der **Methode zur Nachrichtensignaturüberprüfung** führen die folgenden Schritte aus:

    ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")

    1.  Wählen Sie **Zertifikat**.
    2.  Aus der **SSO Certificate** Liste **RSH SHA256**.
    3.  Erstellen Sie eine Textdatei aus dem heruntergeladenen Zertifikat, kopieren Sie den Inhalt der Textdatei und fügen Sie ihn in die **SSO Certificate** Feld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Wählen Sie **zeigt einen Link zum SAMLv2-Anmeldeseite**.

9.  Klicken Sie auf **Update**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich AAD-Benutzer anmelden können, müssen sie in der Central Desktop-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Central Desktop AAD-Benutzerkonten erstellt werden.

###So stellen Sie Benutzerkonten für Central Desktop bereit

1.  Melden Sie sich bei Ihrem Central Desktop-Mandanten an.

2.  Wechseln Sie zu **Personen \ > interne Member**.

3.  Klicken Sie auf **interne Mitglieder hinzufügen**.

    ![Personen](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")

4.  In der **e-Mail-Adresse der neuen Elemente** Textfeld Geben Sie einen AAD-Kontos, die Sie bereitstellen möchten, und klicken Sie dann auf **Weiter**.

    ![Email Addresses of New Members](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")

5.  Klicken Sie auf **Hinzufügen interne Mitglieder**.

    ![Add Internal Member](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")

    >[AZURE.NOTE] Die Benutzer, die Sie hinzugefügt haben, erhalten eine e-Mail, die einen, die sie benötigen Bestätigungslink, klicken Sie auf, um das Konto zu aktivieren.

>[AZURE.NOTE] Können Sie alle anderen Central Desktop Benutzerkonten oder -APIs von Central Desktop Bereitstellung AAD-Benutzerkonten

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Central Desktop Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Central Desktop** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



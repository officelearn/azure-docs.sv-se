<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Cisco Webex | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Cisco Webex mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Lernprogramm: Azure Active Directory-Integration mit Cisco Webex

In diesem Lernprogramm wird die Integration von Azure und Cisco Webex erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Cisco Webex-Mandanten

Nach Abschluss dieses Lernprogramms Cisco Webex zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Cisco Webex-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cisco Webex
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")
##Aktivieren der Anwendungsintegration für Cisco Webex

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cisco Webex aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cisco Webex

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Cisco Webex**.

    ![Anwendungskatalog](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Cisco Webex**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cisco Webex zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Base64-codiertes Zertifikat erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Cisco Webex** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Cisco Webex anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configure app URL")

    1.  In der **Anmelde-URL** Textfeld Geben Sie die URL Ihres Cisco Webex-Mandanten (z. B.: *http://contoso.webex.com*).
    2.  In der **Cisco Webex-Antwort-URL** geben Ihre **URL von Cisco Webex ein** (z. B.: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Auf der **einmaliges Anmelden konfigurieren, um Cisco Webex** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Cisco Webex-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Site Administration**.

    ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")

7.  In der **verwalten** auf **SSO-Konfiguration**.

    ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")

8.  Führen Sie im Abschnitt "Federated Web SSO Configuratio" die folgenden Schritte aus:

    ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")

    1.  Aus der **Verbundprotokoll** Liste **SAML 2.0**.
    2.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    3.  Öffnen Sie das Base64-codierte Zertifikat in Editor, und kopieren Sie seinen Inhalt.
    4.  Klicken Sie auf **Import SAML Metadata**, und fügen Sie das Base64-codierte Zertifikat.
    5.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Cisco Webex** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Issuer for SAML (IdP ID)** Textfeld.
    6.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Cisco Webex** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in den **Customer SSO Service Login URL** Textfeld.
    7.  Aus der **NameID Format** Liste **e-Mail-Adresse**.
    8.  In der **AuthnContextClassRef** Textfeld **Urn: Oasis: Namen: Tc: SAML:2.0:ac:classes:Password**.
    9.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Cisco Webex** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in den **Customer SSO Service Logout URL** Textfeld.
    10. Klicken Sie auf **Update**.

9.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Cisco Webex** Dialogfeld auf der Seite Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Cisco Webex anmelden können, müssen sie in Cisco Webex bereitgestellt werden.  
Im Fall von Cisco Webex ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Cisco Webex** Mandanten.

2.  Wechseln Sie zu **Verwalten von Benutzern \ > Benutzer hinzufügen**.

    ![Hinzufügen von Benutzern](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Add users")

3.  Führen Sie im Abschnitt "Add User" die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Add user")

    1.  Als **Kontotyp**, Option **Host**.
    2.  Geben Sie die Informationen für einen vorhandenen Azure AD-Benutzer in die folgenden Textfelder ein: **Vorname, Nachname**, **Benutzername**, **E-Mail**, **Kennwort**, **Kennwort bestätigen**.
    3.  Klicken Sie auf **Hinzufügen**.

>[AZURE.NOTE] Können Sie alle anderen Cisco Webex Benutzerkonten oder APIs von Cisco Webex Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cisco Webex Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Cisco Webex ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



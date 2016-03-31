<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Adobe EchoSign | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Adobe EchoSign mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" authors="markusvi"  
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

#Lernprogramm: Azure Active Directory-Integration mit Adobe EchoSign

In diesem Lernprogramm wird die Integration von Azure und Adobe EchoSign erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Adobe EchoSign-Abonnement, für das einmaliges Anmelden aktiviert ist.

Nach Abschluss dieses Lernprogramms Adobe EchoSign zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Adobe EchoSign-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Adobe EchoSign
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenario")
##Aktivieren der Anwendungsintegration für Adobe EchoSign

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Adobe EchoSign aktivieren.

###So aktivieren Sie die Anwendungsintegration für Adobe EchoSign

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Adobe EchoSign**.

    ![Anwendungskatalog](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Adobe EchoSign**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Adobe EchoSign zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Adobe EchoSign** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Adobe EchoSign** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite die **Adobe EchoSign-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.echosign.com/*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Adobe EchoSign konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Adobe EchoSign-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Konto**, und klicken Sie dann im Navigationsbereich auf der linken Seite auf **SAML-Einstellungen** unter **Konteneinstellungen**.

    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Account")

7.  Führen Sie im Abschnitt für die SAML-Einstellungen die folgenden Schritte aus:

    ![SAML-Einstellungen](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML Settings")

    1.  Als **SAML-Modus**, Option **SAML obligatorischen**.
    2.  Wählen Sie **Allow EchoSign Account Administrators to melden Sie sich mit ihren Anmeldeinformationen EchoSign**.
    3.  Als **Benutzererstellung**, auf **über SAML authentifiziert Benutzer automatisch hinzufügen**.

8.  Fahren Sie mit den folgenden Schritten fort:

    ![SAML Settings](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML Settings")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Adobe EchoSign konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **IdP-Entitäts-ID** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Adobe EchoSign konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP-Anmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Adobe EchoSign konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP-Abmelde-URL** Textfeld.
    4.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)
    5.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **IdP Certificate** Textbox
    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Adobe EchoSign anmelden können, müssen sie in Adobe EchoSign bereitgestellt werden.  
Im Fall von Adobe EchoSign ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Adobe EchoSign** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Konto**, und klicken Sie dann im Navigationsbereich auf der linken Seite auf **Benutzer und Gruppen**, und klicken Sie dann auf **Erstellen eines neuen Benutzers**.

    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Account")

3.  In der **neue Benutzer erstellen** führen die folgenden Schritte aus:

    ![Benutzer erstellen](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")

    1.  Typ der **e-Mail-Adresse**, **First Name** und **Nachname** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **erstellen Benutzer**.

        >[AZURE.NOTE] Der Azure Active Directory-Kontoinhaber erhält eine e-Mail, die einen zum Bestätigen des Kontos Link, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Adobe EchoSign Benutzerkonten oder APIs von Adobe EchoSign Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Adobe EchoSign Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Adobe EchoSign ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



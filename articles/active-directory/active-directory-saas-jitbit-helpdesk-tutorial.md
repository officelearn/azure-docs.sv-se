<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Jitbit Helpdesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk
  
In diesem Tutorial wird die Integration von Azure und Jitbit Helpdesk erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Jitbit Helpdesk-Mandanten
  
Nach Abschluss dieses Lernprogramms Jitbit Helpdesk zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Jitbit Helpdesk-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jitbit Helpdesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")
##Aktivieren der Anwendungsintegration für Jitbit Helpdesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jitbit Helpdesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jitbit Helpdesk

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Jitbit Helpdesk**.

    ![Anwendungskatalog](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Jitbit Helpdesk**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##Konfigurieren der einmaligen Anmeldung
  
Ziel dieses Abschnitts ist wie Benutzern bei Jitbit Helpdesk mit ihrem Konto in Azure AD mithilfe von Verbund basierend auf dem SAML-Protokoll aktiviert werden kann. .  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Um für Ihren Jitbit Helpdesk-Mandanten einmaliges Anmelden konfigurieren zu können, müssen Sie zuerst erhalten Sie die technischen Jitbit Helpdesk-Support, um diese Funktion aktiviert zu lassen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Jitbit Helpdesk** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Jitbit Helpdesk anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Jitbit Helpdesk-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Jitbit.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configure app URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Jitbit Helpdesk** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Jitbit Helpdesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Jitbit Helpdesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  Klicken Sie auf **Allgemeine Einstellungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

8.  In der **Authentifizierungseinstellungen** Konfiguration führen die folgenden Schritte aus:

    ![Authentifizierungseinstellungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")

    1.  Wählen Sie **Enable SAML 2.0 einmaliges Anmelden** Melden Sie sich mit einmaliges Anmelden (SSO) mit **OneLogin**.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Jitbit Helpdesk** Kopieren der **Service Provider initiierter Endpunkt** -Wert aus, und fügen Sie ihn in die **Endpunkt-URL** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat, den Inhalt in die Zwischenablage kopieren und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    5.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Jitbit Helpdesk anmelden können, müssen sie in Jitbit Helpdesk bereitgestellt werden.  
Im Fall von Jitbit Helpdesk ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Jitbit Helpdesk** Mandanten.

2.  Klicken Sie im Menü am oberen Rand **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  Klicken Sie auf **Benutzer, Unternehmen und Berechtigungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

4.  Klicken Sie auf **Add User**.

    ![Benutzer hinzufügen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")

5.  Geben Sie im Abschnitt erstellen die Daten von Azure AD-Kontos, das Sie bereitstellen möchten, in die folgenden Textfelder ein: **Benutzername**, **E-Mail**, **First Name**, **Nachname**

    ![Erstellen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Create")

6.  Klicken Sie auf **Erstellen**.

>[AZURE.NOTE] Können Sie alle anderen Jitbit Helpdesk Benutzerkonten oder APIs von Jitbit Helpdesk Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Jitbit Helpdesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Jitbit Helpdesk ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Igloo Software | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Igloo Software mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="prasannas"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="12/04/2015" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Igloo Software
  
In diesem Tutorial wird die Integration von Azure und Igloo Software erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein [Igloo Software](http://www.igloosoftware.com/) einmaliges Anmelden aktiviert Abonnement
  
Nach Abschluss dieses Lernprogramms Igloo Software zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Igloo Software-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Igloo Software
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenario")
##Aktivieren der Anwendungsintegration für Igloo Software
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Igloo Software aktivieren.

###So aktivieren Sie die Anwendungsintegration für Igloo Software

1.  Klicken Sie in der klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Igloo Software**.

    ![Anwendungskatalog](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Igloo Software**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Igloo Software zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Central Desktop-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In der klassischen Azure-Portal auf der **Igloo Software** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Igloo Software** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Igloo Software-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.igloocommunities.com/?signin*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Igloo Software konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Igloo Software-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu der **Control Panel**.

    ![Systemsteuerung](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Control Panel")

7.  In der **Mitgliedschaft** auf **Sign In Settings**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Sign in Settings")

8.  Klicken Sie im Abschnitt SAML-Konfiguration auf **SAML-Authentifizierung konfigurieren**.

    ![SAML-Konfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML Configuration")

9.  In den **Allgemeine Konfiguration** führen die folgenden Schritte aus:

    ![Allgemeine Konfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "General Configuration")

    1.  In der **Verbindungsname** Textfeld Geben Sie einen benutzerdefinierten Namen für Ihre Konfiguration.
    2.  In der klassischen Azure-Portal auf der **einmaliges Anmelden bei Igloo Software konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP-Anmelde-URL** Textfeld.
    3.  In der klassischen Azure-Portal auf der **einmaliges Anmelden bei Igloo Software konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP-Abmelde-URL** Textfeld.
    4.  Als **Logout Response and Request HTTP Type**, Option **POST**.
    5.  Erstellen Sie aus dem heruntergeladenen Zertifikat eine Textdatei.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Entfernen Sie die erste und letzte Zeile aus der textdateiversion Ihres Zertifikats, kopieren Sie den verbleibenden zertifikattext und fügen Sie ihn in die **öffentliches Zertifikat** Textfeld.

10. In der **Antwort- und Authentifizierungskonfiguration**, gehen Sie folgendermaßen vor:

    ![Antwort- und Authentifizierungskonfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Response and Authentication Configuration")

    1.  Als **Identitätsanbieter**, Option **Microsoft ADFS**.
    2.  Als **Bezeichnertyp**, Option **e-Mail-Adresse**.
    3.  In der **-e-Mail-Attribut** Textfeld **Emailaddress**.
    4.  In der **First Name Attribute** Textfeld **Givenname**.
    5.  In der **Last Name-Attribut** Textfeld **Surname**.

11. Führen Sie die folgenden Schritte aus, um die Konfiguration abzuschließen:

    ![Benutzererstellung beim Anmelden](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "User creation on Sign in")

    1.  Als **Erstellung des Benutzers bei der Anmeldung**, Option **Erstellen Sie einen neuen Benutzer an Ihrem Standort, bei der Anmeldung**.
    2.  Als **Sign in Settings**, Option **Use SAML Button auf dem Bildschirm "Anmelden"**.
    3.  Klicken Sie auf **Speichern**.

12. Auf dem Azure-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Igloo Software steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Igloo Software anzumelden, überprüft Igloo Software, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Igloo Software automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Igloo Software Benutzer zu

1.  Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2.  Auf der ** Igloo Software ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


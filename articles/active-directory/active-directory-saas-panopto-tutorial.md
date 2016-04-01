<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Panopto | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Panopto mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Panopto
  
In diesem Tutorial wird die Integration von Azure und Panopto erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Panopto-Mandanten
  
Nach Abschluss dieses Lernprogramms Panopto zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Panopto-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Panopto
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenario")
##Aktivieren der Anwendungsintegration für Panopto
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Panopto aktivieren.

###So aktivieren Sie die Anwendungsintegration für Panopto

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-panopto-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-panopto-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Panopto**.

    ![Anwendungskatalog](./media/active-directory-saas-panopto-tutorial/IC777666.png "Appkication Gallery")

7.  Wählen Sie im Ergebnisbereich **Panopto**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Panopto zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Panopto** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Panopto anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Panopto-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Panopto.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configure app URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Panopto** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Panopto-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste auf der linken Seite auf **System**, und klicken Sie dann auf **Identitätsanbieter**.

    ![System](./media/active-directory-saas-panopto-tutorial/IC777670.png "System")

7.  Klicken Sie auf **Anbieter hinzufügen**.

    ![Identitätsanbieter](./media/active-directory-saas-panopto-tutorial/IC777671.png "Identity Providers")

8.  Führen Sie im Abschnitt für den SAML-Anbieter die folgenden Schritte aus:

    ![SaaS-Konfiguration](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS configuration")

    1.  Aus der **Anbietertyp** Liste **SAML20**
    2.  In der **Instanzname** Textfeld Geben Sie einen Namen für die Instanz.
    3.  In der **Beschreibung** Textfeld eine Beschreibung eingeben.
    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Panopto** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    5.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Panopto** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **Bounce Page Url** Textfeld.
    6.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    7.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **öffentlichen Schlüssel** Textbox
    8.  Klicken Sie auf **Speichern**.
        ![Speichern](./media/active-directory-saas-panopto-tutorial/IC777673.png "Save")

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Panopto steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Panopto anzumelden, überprüft Panopto, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Panopto automatisch erstellt.

>[AZURE.NOTE]Können Sie alle anderen Panopto Benutzerkonten oder APIs von Panopto zum Bereitstellen von Azure AD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Panopto Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Panopto ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-panopto-tutorial/IC777675.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-panopto-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TimeOffManager | Microsoft Azure" 
    description="Erfahren Sie, wie Sie TimeOffManager mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit TimeOffManager
  
In diesem Tutorial wird die Integration von Azure und TimeOffManager erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TimeOffManager-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms TimeOffManager zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer TimeOffManager-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TimeOffManager
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

##Aktivieren der Anwendungsintegration für TimeOffManager
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TimeOffManager aktivieren.

###So aktivieren Sie die Anwendungsintegration für TimeOffManager:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **TimeOffManager**.

    ![Anwendungskatalog](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **TimeOffManager**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TimeOffManager zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren TimeOffManager-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **TimeOffManager** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei TimeOffManager** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **TimeOffManager-Antwort-URL** Textfeld Geben Sie die URL Ihres TimeOffManager ein (z. B.: "*Beispiel: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company \_id=IC34216*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configure App URL")

    Sie können die Antwort-URL von der Seite zur Einstellung der einfachen Anmeldung beim TimeOffManager abrufen.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei TimeOffManager** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der TimeOffManager-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Konto \ > Konto Optionen \ > Single Sign-On Settings**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")

7.  In der **Single Sign-On Settings** führen die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")

    1.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    2.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie das gesamte Zertifikat in **x. 509-Zertifikat** Textfeld.
    3.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei TimeOffManager** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Idp-Aussteller** Textfeld.
    4.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei TimeOffManager** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IdP Endpoint URL** Textfeld.
    5.  Als **Enforce SAML**, Option **Nr.**.
    6.  Als **Auto-Create Users**, Option **Ja**.
    7.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei TimeOffManager** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    8.  Klicken Sie auf **Speichern**.

8.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei TimeOffManager** Dialogfeld auf der Seite Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **vollständig**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configure Single Sign-On")

9.  Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributes")

10. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![SAML-Tokenattribute](./media/active-directory-saas-timeoffmanager-tutorial/IC795921.png "saml token attributes")

  	|Attributname|Attributwert|
  	|---|---|
  	|Firstname|User.givenname|
  	|Lastname|User.surname|

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.
    3.  In der **Attributwert** Textfeld, wählen Sie für diese Zeile angezeigten Wert des Attributs.
    4.  Klicken Sie auf **vollständige**.

11. Klicken Sie auf **Ändern**.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TimeOffManager anmelden können, müssen sie in TimeOffManager bereitgestellt werden.  
TimeOffManager unterstützt die bedarfsabhängige Benutzerbereitstellung. Für Sie steht kein Aktionselement zur Verfügung.  
Die Benutzer werden automatisch während der erstmaligen und einmaligen Anmeldung hinzugefügt.

>[AZURE.NOTE] Können Sie alle anderen TimeOffManager Benutzerkonten oder APIs von TimeOffManager Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TimeOffManager Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **TimeOffManager** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



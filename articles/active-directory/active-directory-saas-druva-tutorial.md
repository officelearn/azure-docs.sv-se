<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Druva | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Druva mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Druva

In diesem Tutorial wird die Integration von Azure und Druva erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Druva-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Druva zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Druva-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Druva
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")
##Aktivieren der Anwendungsintegration für Druva

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Druva aktivieren.

###So aktivieren Sie die Anwendungsintegration für Druva

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-druva-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-druva-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Druva**.

    ![Anwendungskatalog](./media/active-directory-saas-druva-tutorial/IC795085.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Druva**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Druva zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

Ihre Druva-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![SAML-Tokenattribute](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  Im Azure Active Directory-Portal auf der **Druva** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen die ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795027.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei druva anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795088.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Druva-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zur Anmeldung bei Ihrer Druva-Anwendung (z. B.: "*https://cloud.druva.com/home/*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-druva-tutorial/IC795089.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei Druva** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795090.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Druva-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Verwalten \ > Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")

7.  Führen Sie im Dialogfeld „Einstellungen für einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")

    1.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Druva** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **ID Provider Login URL** Textfeld.
    2.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei Druva** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **ID Provider Logout URL** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **ID-Anbieterzertifikat** Textbox
    5.  Zum Öffnen der **Einstellungen** auf **Speichern**.

8.  Auf der **Einstellungen** auf **Generate SSO Token**.

    ![Einstellungen](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")

9.  Auf der **einmaliges Anmelden Authentifizierungstoken** im Dialogfeld die folgenden Schritte ausführen:

    ![SSO-Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")

    1.  Klicken Sie auf **Kopieren**.
    2.  Klicken Sie auf **Schließen**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-druva-tutorial/IC795095.png "Configure Single Sign-On")

11. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributes")

12. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

  	|Attributname|Attributwert|
  	|---|---|
  	|insync\_auth\_token|<*zwischenablagewert*>|

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.
    3.  In der **Attributwert** Textfeld Geben Sie für diese Zeile angezeigten Wert des Attributs.
    4.  Klicken Sie auf **vollständige**.

13. Klicken Sie auf **Ändern**.
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Druva anmelden können, müssen sie in Druva bereitgestellt werden.  
Im Fall von Druva ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Druva** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Verwalten \ > Benutzer**.

    ![Benutzer verwalten](./media/active-directory-saas-druva-tutorial/IC795097.png "Manage Users")

3.  Klicken Sie auf **Erstellen Sie ein neues**.

    ![Benutzer verwalten](./media/active-directory-saas-druva-tutorial/IC795098.png "Manage Users")

4.  Führen Sie im Dialogfeld „Neuen Benutzer erstellen“ die folgenden Schritte aus:

    ![Neuen Benutzer erstellen](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")

    1.  Geben Sie die E-Mail-Adresse und den Namen eines gültigen Azure Active Directory-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **erstellen Benutzer**.

>[AZURE.NOTE] Können Sie alle anderen Druva Benutzerkonten oder APIs von Druva Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Druva Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Druva ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-druva-tutorial/IC795100.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-druva-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



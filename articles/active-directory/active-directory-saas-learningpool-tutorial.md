<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Learningpool | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Learningpool mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Learningpool
  
In diesem Tutorial wird die Integration von Azure und Learningpool erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Learningpool-Abonnement, für das das einmalige Anmelden aktiviert ist.
  
Nach Abschluss dieses Lernprogramms Learningpool zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Learningpool-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Learningpool
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")
##Aktivieren der Anwendungsintegration für Learningpool
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Learningpool aktivieren.

###Führen Sie zum Aktivieren der Anwendungsintegration für Learningpool die folgenden Schritte aus:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Learningpool**.

    ![Anwendungskatalog](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Learningpool**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Learningpool zu authentifizieren.
  
Ihre Learningpool-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![SAML-Tokenattribute](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Learningpool** anwendungsintegrationsseite im Menü am oberen Rand, klicken Sie auf **Attribute** Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")

2.  So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ###  

  	|Attributname                |Attributwert            |
  	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
  	|-------------------------------|--------------------------|  
     urn:oid:2.5.4.42|User.givenname   
  	|urn:oid:0.9.2342.19200300.100.1.3|User.mail
  	|urn:oid:2.5.4.4|User.surname

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.
    3.  Aus der **Attributwert** wählen Sie für diese Zeile angezeigten Wert des Attributs.
    4.  Klicken Sie auf **vollständige**.

3.  Klicken Sie auf **Ändern**.

4.  Klicken Sie in Ihrem Browser auf **wieder** zum Öffnen der **Schnellstart** erneut zu öffnen.

5.  Klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")

6.  Auf der **wie sollen sich Benutzer bei Learningpool anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")

7.  Auf der **App-URL konfigurieren** auf der Seite der **Learningpool-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer Ihre Learningpool-Anwendung anmelden (z. B.: 
https://Parliament.Preview.learningpool.com/AUTH/shibboleth/Index.PHP), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")

8.  Auf der **einmaliges Anmelden konfigurieren, um Learningpool** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")

9.  Leiten Sie die Metadatendatei an das Supportteam von Learningpool weiter.

    >[AZURE.NOTE]Einmaliges Anmelden muss durch das Supportteam von Learningpool aktiviert werden.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Learningpool anmelden können, müssen sie in Learningpool bereitgestellt werden.
  
Für das Konfigurieren der Benutzerbereitstellung in Learningpool steht kein Aktionselement zur Verfügung.  
Benutzer müssen vom Supportteam von Learningpool erstellt werden.

>[AZURE.NOTE]Können Sie alle anderen Learningpool Benutzerkonten oder APIs von Learningpool Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Learningpool Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Learningpool ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


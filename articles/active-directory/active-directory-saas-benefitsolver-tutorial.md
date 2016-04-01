<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Benefitsolver | Microsoft Azure"
    description="Erfahren Sie, wie Sie Benefitsolver mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Benefitsolver

In diesem Lernprogramm wird die Integration von Azure und Benefitsolver erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Benefitsolver-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms Benefitsolver zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Benefitsolver
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenario")
##Aktivieren der Anwendungsintegration für Benefitsolver

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Benefitsolver aktivieren.

###So aktivieren Sie die Anwendungsintegration für Benefitsolver

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Benefitsolver**.

    ![Anwendungskatalog](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Benefitsolver**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Benefitsolver zu authentifizieren.  
Die Benefitsolver-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Benefitsolver** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden Benefitsolver** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** führen die folgenden Schritte aus:

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configure App Settings")

    1.  In der **-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer für die Anmeldung bei der Benefitsolver-Anwendung (z. B.: "*http://azure-dev.benefitsolver.com*
    2.  In der **-Antwort-URL** Textfeld Geben Sie die URL Ihres Benefitsolver ein (z. B.: "*https://dev.benefitsolver.com/benefits/BenefitSolverView?page \_name=single\_signon\_saml*").  

        >[AZURE.NOTE] Sie können den tatsächlichen Wert für Ihre Umgebung von Ihrem Supportteam Benefitsolver erhalten.

    3.  Klicken Sie auf **Weiter**.

4.  Auf der **Benefitsolver einmaliges Anmelden konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von Benefitsolver.

    >[AZURE.NOTE] Ihr Benefitsolver-Supportteam muss die tatsächliche SSO-Konfiguration.
     Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configure Single Sign-On")

7.  Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributes")

8.  So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")

  	|Attributname|Attributwert|
  	|---|---|
  	|ClientID|Sie müssen diesen Wert beim Supportteam von Benefitsolver anfragen.|
  	|ClientKey|Sie müssen diesen Wert beim Supportteam von Benefitsolver anfragen.|
  	|LogoutURL|Sie müssen diesen Wert beim Supportteam von Benefitsolver anfragen.|
  	|EmployeeID|Sie müssen diesen Wert beim Supportteam von Benefitsolver anfragen.|

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.
    3.  In der **Attributwert** Textfeld, wählen Sie für diese Zeile angezeigten Wert des Attributs.
    4.  Klicken Sie auf **vollständige**.

9.  Klicken Sie auf **Ändern**.
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Benefitsolver anmelden können, müssen sie in Benefitsolver bereitgestellt werden.  
Im Fall von Benefitsolver müssen Benutzer manuell vom Benefitsolver-Supportteam erstellt werden.

>[AZURE.NOTE] Können Sie alle anderen Benefitsolver Benutzerkonten oder APIs von Benefitsolver Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Benefitsolver Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Benefitsolver ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



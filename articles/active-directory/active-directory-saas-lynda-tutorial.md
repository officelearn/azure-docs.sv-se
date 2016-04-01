<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Lynda.com | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Lynda.com mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Lynda.com
  
In diesem Tutorial wird die Integration von Azure und Lynda.com erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Lynda.com-Mandanten
  
Nach Abschluss dieses Lernprogramms Lynda.com zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer Lynda.com-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Lynda.com
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")
##Aktivieren der Anwendungsintegration für Lynda.com
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Lynda.com aktivieren.

###So aktivieren Sie die Anwendungsintegration für Lynda.com

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-lynda-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-lynda-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Lynda.com**.

    ![Anwendungskatalog](./media/active-directory-saas-lynda-tutorial/IC777524.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Lynda.com**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Lynda.com zu authentifizieren.

>[AZURE.IMPORTANT]Um einmaliges Anmelden für Ihren Mandanten Lynda.com konfigurieren können, müssen Sie zuerst erhalten Sie technischen Support von Lynda.com zum Abrufen dieses Feature aktiviert ist.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Lynda.com** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Lynda.com anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Lynda.com-Anmelde-URL** Textfeld Geben Sie die URL Ihres Lynda.com-Mandanten (z. B.: *Https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configure app URL")

4.  Auf der **einmaliges Anmelden bei Lynda.com konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configure single sign-on")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von Lynda.com. Das Supportteam von Lynda.com übernimmt das Konfigurieren des einmaligen Anmeldens für Sie.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Lynda.com steht kein Aktionselement zur Verfügung.  
Wenn sich ein zugewiesener Benutzer über den Zugriffsbereich bei Lynda.com anmelden möchte, überprüft Lynda.com, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Lynda.com automatisch erstellt.

>[AZURE.NOTE]Können Sie alle anderen Lynda.com Benutzerkonten oder APIs von Lynda.com Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Lynda.com Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Lynda.com ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-lynda-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


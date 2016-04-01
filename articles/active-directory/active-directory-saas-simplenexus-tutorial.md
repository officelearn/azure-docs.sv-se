<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SimpleNexus | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie SimpleNexus mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit SimpleNexus
  
In diesem Tutorial wird die Integration von Azure und SimpleNexus erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SimpleNexus-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms SimpleNexus zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer SimpleNexus-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SimpleNexus
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenario")
##Aktivieren der Anwendungsintegration für SimpleNexus
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SimpleNexus aktivieren.

###So aktivieren Sie die Anwendungsintegration für SimpleNexus:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **simple Nexus**.

    ![Anwendungskatalog](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **SimpleNexus**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SimpleNexus zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SimpleNexus** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei SimpleNexus anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **SimpleNexus-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://simplenexus.com/CompanyName \_login*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um SimpleNexus** auf **Metadaten**, und klicken Sie dann leiten die Metadatendatei zum SimpleNexus-Supportteam.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configure Single Sign-On")

    >[AZURE.NOTE] Einmaliges Anmelden muss vom Supportteam von SimpleNexus aktiviert werden.

5.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei SimpleNexus anmelden können, müssen sie in SimpleNexus bereitgestellt werden.  
Im Fall von SimpleNexus ist die Bereitstellung eine manuelle Aufgabe, die vom Tenant-Administrator ausgeführt wird.

>[AZURE.NOTE] Können Sie alle anderen SimpleNexus Benutzerkonten oder APIs von SimpleNexus Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SimpleNexus Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** SimpleNexus ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


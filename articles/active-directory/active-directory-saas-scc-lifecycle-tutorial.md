<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SCC LifeCycle | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie SCC LifeCycle mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit SCC LifeCycle
  
In diesem Tutorial wird die Integration von Azure und  SCC LifeCycle erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SCC LifeCycle-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms SCC LifeCycle zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer SCC LifeCycle-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SCC LifeCycle
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")
##Aktivieren der Anwendungsintegration für SCC LifeCycle
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SCC LifeCycle aktivieren.

###So aktivieren Sie die Anwendungsintegration für  SCC LifeCycle:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **SCC LifeCycle**.

    ![Anwendungskatalog](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **SCC LifeCycle**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SCC LifeCycle zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SCC LifeCycle** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei SCC LifeCycle** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern zur Anmeldung bei Ihrer SCC LifeCycle-Anwendung mithilfe des folgenden Musters "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei SCC LifeCycle konfigurieren** auf **Metadaten**, und speichern Sie dann die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configure Single Sign-On")

5.  Leiten Sie die Metadatendatei an das Supportteam von  SCC LifeCycle weiter.

    >[AZURE.NOTE]Einmaliges Anmelden muss vom Supportteam von SCC LifeCycle aktiviert werden.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei SCC LifeCycle anmelden können, müssen sie in SCC LifeCycle bereitgestellt werden.
  
Für das Konfigurieren der Benutzerbereitstellung in SCC LifeCycle steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich bei SCC LifeCycle anzumelden, wird ggf. automatisch ein SCC LifeCycle-Konto erstellt.

>[AZURE.NOTE]Können Sie alle anderen SCC LifeCycle Benutzerkonten oder APIs von SCC LifeCycle Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SCC LifeCycle Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** SCC LifeCycle ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


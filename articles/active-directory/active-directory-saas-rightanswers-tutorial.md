<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit RightAnswers | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie RightAnswers mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit RightAnswers
  
In diesem Tutorial wird die Integration von Azure und RightAnswers erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein RightAnswers-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms RightAnswers zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für RightAnswers
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")
##Aktivieren der Anwendungsintegration für RightAnswers
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für RightAnswers aktivieren.

###So aktivieren Sie die Anwendungsintegration für RightAnswers:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **RightAnswers**.

    ![Anwendungskatalog](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **RightAnswers**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei RightAnswers zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **RightAnswers** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden RightAnswers** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** auf der Seite der **Anmelde-URL** Textfeld die URL ein, Ihre Benutzer für die Anmeldung bei der RightAnswers-Anwendung (z. B.: *https://fortify.rightanswers.com/portal/ss/*), und klicken Sie dann auf **Weiter**.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configure App Settings")

4.  Auf der **RightAnswers einmaliges Anmelden konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configure Single Sign-On")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von RightAnswers.

    >[AZURE.NOTE] Ihr RightAnswers-Supportteam muss die tatsächliche SSO-Konfiguration.
    Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei RightAnswers anmelden können, müssen sie in RightAnswers bereitgestellt werden.  
Im Fall von RightAnswers ist die Bereitstellung eine automatisierte Aufgabe.  
Für Sie steht kein Aktionselement zur Verfügung.
  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE]Können Sie alle anderen RightAnswers Benutzerkonten oder APIs von RightAnswers Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie RightAnswers Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** RightAnswers ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


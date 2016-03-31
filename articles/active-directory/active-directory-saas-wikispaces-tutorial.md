<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Wikispaces | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Wikispaces mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Wikispaces
  
In diesem Tutorial wird die Integration von Azure und Wikispaces erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Wikispaces-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Wikispaces zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Wikispaces-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Wikispaces
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##Aktivieren der Anwendungsintegration für Wikispaces
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Wikispaces aktivieren.

###So aktivieren Sie die Anwendungsintegration für Wikispaces:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Wikispaces**.

    ![Anwendungskatalog](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Wikispaces**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Wikispaces zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Wikispaces** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Wikispaces anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Wikispaces-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.wikispaces.net*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Wikispaces** auf **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")

5.  Senden Sie die Metadatendatei an das Supportteam von  Wikispaces.

    >[AZURE.NOTE] Konfiguration die einmaligen Anmeldung muss durch das Supportteam von Wikispaces erfolgen. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Wikispaces anmelden können, müssen sie in Wikispaces bereitgestellt werden.  
Im Fall von Wikispaces ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Wikispaces** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Elemente**.

    ![Mitglieder](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")

3.  Klicken Sie auf die **Personen einladen**.

    ![Personen einladen](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")

4.  In der **Invite People** führen die folgenden Schritte aus:

    ![Personen einladen](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")

    1.  Typ der **Benutzernamen oder e-Mail-Adresse** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Senden**.  

        >[AZURE.NOTE] Der Azure Active Directory-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Wikispaces Benutzerkonten oder APIs von Wikispaces Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Wikispaces Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Wikispaces ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


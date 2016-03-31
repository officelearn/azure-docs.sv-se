<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Projectplace | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Projectplace mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Projectplace
  
In diesem Tutorial wird die Integration von Azure und Projectplace erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Projectplace-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Projectplace zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Projectplace-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Projectplace
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")
##Aktivieren der Anwendungsintegration für Projectplace
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Projectplace aktivieren.

###So aktivieren Sie die Anwendungsintegration für Projectplace

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Projectplace**.

    ![Anwendungskatalog](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Projectplace**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Projectplace zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Projectplace** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")

2.  Auf der **wie sollen sich Benutzer bei Projectplace anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Projectplace-Anmelde-URL** Textfeld Geben Sie die URL Ihres ProjectPlace-Mandanten (z. B.: "*http://company.projectplace.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Projectplace konfigurieren** auf **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")

5.  Senden Sie die Metadatendatei an das Supportteam von Projectplace.

    >[AZURE.NOTE] Konfiguration die einmaligen Anmeldung muss durch das Supportteam von Projectplace erfolgen. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Projectplace anmelden können, müssen sie in Projectplace bereitgestellt werden.  
Im Fall von Projectplace ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Projectplace** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Personen**, und klicken Sie dann auf **Member**.

    ![Personen](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")

3.  Klicken Sie auf **Mitglied hinzufügen**.

    ![Mitglieder hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")

4.  In der **Mitglied hinzufügen** führen die folgenden Schritte aus:

    ![Neue Mitglieder](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")

    1.  In der **neue Mitglieder** Textfeld Geben Sie die e-Mail-Adresse eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Senden**

        >[AZURE.NOTE] Eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird, wird an der Azure Active Directory-Kontoinhaber gesendet.
    
>[AZURE.NOTE]Können Sie alle anderen Projectplace Benutzerkonten oder APIs von Projectplace Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Projectplace Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Projectplace ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Huddle | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Huddle mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Huddle
  
In diesem Tutorial wird die Integration von Azure und Huddle erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Huddle-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Huddle zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Huddle-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Huddle
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configure Single Sign-On")
##Aktivieren der Anwendungsintegration für Huddle
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Huddle aktivieren.

###So aktivieren Sie die Anwendungsintegration für Huddle

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-huddle-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-huddle-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Huddle**.

    ![Anwendungskatalog](./media/active-directory-saas-huddle-tutorial/IC787831.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Huddle**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Huddle zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Huddle** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Huddle anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Huddle-Anmelde-URL** Geben die URL Ihres huddle-Mandanten nach dem Muster "*http://company.huddle.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für huddle** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configure Single Sign-On")

    1.  Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.
    2.  Kopieren der **Aussteller-URL** Wert, der **SAML-SSO-URL** und das heruntergeladene Zertifikat und senden Sie diese an das Supportteam von Huddle.

    >[AZURE.NOTE] Einmaliges Anmelden muss vom Supportteam von Huddle aktiviert werden.
    Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

5.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Huddle anmelden können, müssen sie in Huddle bereitgestellt werden.  
Im Fall von Huddle ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Huddle** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **Arbeitsbereich**.

3.  Klicken Sie auf **Personen \ > einladen**.

    ![Personen](./media/active-directory-saas-huddle-tutorial/IC787838.png "People")

4.  In der **Erstellen Sie eine neue Einladung** führen die folgenden Schritte aus:

    ![Neue Einladung](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")

    1.  In der **Wählen Sie ein Team einladen von Personen zu** Liste **Team**.
    2.  Typ der **e-Mail-Adresse** eines gültigen AAD-Kontos, das Sie bereitstellen, in das entsprechende Textfeld möchten.
    3.  Klicken Sie auf **einladen**.

    >[AZURE.NOTE] Die Azure AD-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Huddle Benutzerkonten oder APIs von Huddle Bereitstellung von AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Huddle Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Huddle ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-huddle-tutorial/IC787840.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-huddle-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ShiftPlanning | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ShiftPlanning mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit ShiftPlanning
  
In diesem Tutorial wird die Integration von Azure und ShiftPlanning erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ShiftPlanning-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms ShiftPlanning zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer ShiftPlanning-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ShiftPlanning
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")
##Aktivieren der Anwendungsintegration für ShiftPlanning
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ShiftPlanning aktivieren.

###So aktivieren Sie die Anwendungsintegration für ShiftPlanning:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ShiftPlanning**.

    ![Anwendungskatalog](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **ShiftPlanning**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ShiftPlanning zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ShiftPlanning** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei ShiftPlanning anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **ShiftPlanning-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.shiftplanning.com/includes/saml/*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei ShiftPlanning konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")

5.  Melden Sie sich in einem Webbrowserfenster Ihre **ShiftPlanning** -Unternehmenswebsite als Administrator.

6.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7.  Unter **Integration**, klicken Sie auf **Single Sign-On**.

    ![Einmaliges Anmelden](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  In der **Single Sign-On** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  Wählen Sie **SAML aktiviert**.
    2.  Wählen Sie **Allow Password Login**
    3.  In Azure-Portal auf der **einmaliges Anmelden bei ShiftPlanning konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **SAML Issuer URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei ShiftPlanning konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Abmelde-URL** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    7.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei ShiftPlanning anmelden können, müssen sie in ShiftPlanning bereitgestellt werden.  
Im Fall von ShiftPlanning ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **ShiftPlanning** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf **Admin**.

    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3.  Klicken Sie auf **Personal**.

    ![Mitarbeiter](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")

4.  Unter **Aktionen**, klicken Sie auf **Mitarbeiter hinzufügen**.

    ![Mitarbeiter hinzufügen](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")

5.  In der **Add Employees** führen die folgenden Schritte aus:

    ![Mitarbeiter speichern](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")

    1.  Typ der **Vorname**, **Nachname** und **E-Mail** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern die Mitarbeiter**.

>[AZURE.NOTE]Können Sie alle anderen ShiftPlanning Benutzerkonten oder APIs von ShiftPlanning Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ShiftPlanning Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ShiftPlanning ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


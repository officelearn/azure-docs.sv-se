<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Concur | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Concur mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Concur  


In diesem Tutorial wird die Integration von Azure und Concur erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in Concur

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Concur
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-concur-tutorial/IC769766.png "Scenario")

>[AZURE.NOTE] Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO via SAML ist eine separate Aufgabe Sie hierzu Concur kontaktieren müssen.

##Aktivieren der Anwendungsintegration für Concur

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Concur aktivieren.

###So aktivieren Sie die Anwendungsintegration für Concur

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für das Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-concur-tutorial/IC700994.png "Applications")

4.  Zum Öffnen der **Anwendungskatalog**, klicken Sie auf **App hinzufügen**, und klicken Sie dann auf **Hinzufügen einer Anwendung für meine Organisation**.

    ![Was möchten Sie tun?](./media/active-directory-saas-concur-tutorial/IC700995.png "What do you want to do?")

5.  In der **Suchfeld**, Typ **Concur**.

    ![Anwendungskatalog](./media/active-directory-saas-concur-tutorial/IC721727.png "Application Gallery")

6.  Wählen Sie im Ergebnisbereich **Concur**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Concur zu authentifizieren.

>[AZURE.NOTE] Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO via SAML ist eine separate Aufgabe Sie hierzu Concur kontaktieren müssen.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der ** Concur ** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-concur-tutorial/IC769767.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Concur anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-concur-tutorial/IC769768.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Concur-Anmelde-URL** Textfeld Geben Sie Ihre Concur-Mandanten Anmelde-URL, und klicken Sie dann auf **Weiter**: 

    ![Anmelde-ULR konfigurieren](./media/active-directory-saas-concur-tutorial/IC769769.png "Configure sign in URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für concur** führen die folgenden Schritte aus.

    ![Anmelde-ULR konfigurieren](./media/active-directory-saas-concur-tutorial/IC769770.png "Configure sign in URL")

    1.  Klicken Sie auf „Metadaten herunterladen“, und speichern Sie die Datendatei auf Ihrem Computer.
    2.  Wenden Sie sich an das Supportteam von Concur, um SSO für Ihren Mandanten konfigurieren.
    3.  Wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.  

    >[AZURE.NOTE] Die Konfiguration Ihres Concur-Abonnements für Verbund-SSO via SAML ist eine separate Aufgabe Sie hierzu Concur kontaktieren müssen.

##Konfigurieren der Benutzerbereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Concur aktivieren.

Die Aktivierung von Apps im Dienst für Ausgaben erfordert die korrekte Einrichtung und Verwendung eines Webdienstadministrator-Profils. Fügen Sie die Webdienstadministrator-Rolle (WSAdmin) nicht einfach dem vorhandenen Administratorprofil hinzu, das Sie zur Verwaltung von Reisekosten und Spesen verwenden.

Concur-Berater oder der Kundenadministrator müssen ein separates Webdienstadministrator-Profil erstellen, und der Kundenadministrator muss dieses Profil für die Webdienstadministrator-Funktionen verwenden (z. B. zum Aktivieren von Apps). Diese Profile müssen vom normalen Administratorprofil für Reisekosten und Spesen des Kundenadministrators getrennt sein (dem Administratorprofil für Reisekosten und Spesen sollte die WSAdmin-Rolle nicht zugewiesen werden).

Geben Sie beim Erstellen des Profils, das zum Aktivieren der App verwendet wird, den Namen des Kundenadministrators in die Felder des Benutzerprofils ein. Dadurch wird das Profil als Besitzer zuzuweisen. Sobald die Profile erstellt wurde, muss der Client mit diesem Profil, klicken Sie auf Anmelden die "*aktivieren*" Schaltfläche für eine Partneranwendung im Webdienste-Menü.

Aus den folgenden Gründen sollte diese Aktion nicht mit dem Profil ausgeführt werden, das für die normale Verwaltung von Reisekosten und Spesen verwendet wird.

1.  Der Client hat auf die Schaltfläche "*Ja*" im Dialogfenster, das nach der Aktivierung einer Anwendung angezeigt. Dadurch wird bestätigt, dass der Kunde dem Zugriff der Partneranwendung auf seine Daten zustimmt. Es ist daher nicht möglich, dass Sie oder der Partner auf diese Schaltfläche „Ja“ klicken.
2.  Falls ein Kundenadministrator, der eine App mit dem Reisekosten- und Spesenprofil aktiviert hat, das Unternehmen verlässt (und das Profil deaktiviert wird), funktionieren alle Apps, die mit diesem Profil aktiviert wurden, erst wieder, wenn sie mit einem anderen aktiven Webdienstadministrator-Profil aktiviert werden. Aus diesem Grund sollten separate Webdienstadministrator-Profile erstellt werden.
3.  Wenn ein Administrator das Unternehmen verlässt, kann der mit dem Webdienstadministrator-Profil verknüpfte Name ggf. in den Namen des neuen Administrators geändert werden. Da dieses Profil nicht deaktiviert werden muss, hat dies keinerlei Auswirkungen auf die aktivierte App.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich Ihre **Concur** Mandanten.

2.  Von der **Verwaltung** Klicken Sie im Menü **Webdienste**.

    ![Concur-Mandant](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur tenant")

3.  Auf der linken Seite von der **Webdienste** Klicken Sie im Bereich **Partner der Anwendung aktivieren**.

    ![Partneranwendung aktivieren](./media/active-directory-saas-concur-tutorial/IC721730.png "Enable Partner Application")

4.  Von der **der Anwendung aktivieren** Liste **Azure Active Directory**, und klicken Sie dann auf **aktivieren**.

    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Klicken Sie auf **Ja** Schließen die **Aktion bestätigen** Dialogfeld.

    ![Aktion bestätigen](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirm Action")

6.  Wählen Sie in der Azure-Verwaltungsportal **Concur** in der Anwendungsliste aus, öffnen die **Concur** Seite.

7.  Öffnen der **Benutzerbereitstellung konfigurieren** Seite, klicken Sie auf **Benutzerbereitstellung**.

8.  Geben Sie den Benutzernamen und das Kennwort Ihres Concur-Administrators ein, und klicken Sie dann auf **Weiter**.

9.  Um die Konfiguration auf Fertig stellen die **Bestätigung** auf die **vollständig** Schaltfläche.

Sie können nun ein Testkonto erstellen und nach 10 Minuten überprüfen, ob das Konto mit Concur synchronisiert wurde.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Concur Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Concur ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-concur-tutorial/IC769771.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-concur-tutorial/IC767830.png "Yes")

Nach 10 Minuten können Sie überprüfen, ob das Konto mit Concur synchronisiert wurde.

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



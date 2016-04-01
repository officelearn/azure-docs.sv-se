<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit FreshService | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie FreshService mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit FreshService
  
In diesem Tutorial wird die Integration von Azure und FreshService erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein FreshService-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms FreshService zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für FreshService
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")
##Aktivieren der Anwendungsintegration für FreshService
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für FreshService aktivieren.

###So aktivieren Sie die Anwendungsintegration für FreshService

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **FreshService**.

    ![Anwendungskatalog](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **FreshService**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei FreshService zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für FreshService müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **FreshService** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei FreshService anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **FreshService URL für Anmeldung** Textfeld die URL ein, Ihre Benutzer zur Anmeldung bei der Freshdesk-Anwendung (z. B.: "*http://democompany.freshservice.com/*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um FreshService** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der FreshService-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

7.  In der **Kundenportal**, klicken Sie auf **Security**.

    ![Sicherheit](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Security")

8.  In der **Sicherheit** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")

    1.  Switch **Single Sign OnON**.
    2.  Wählen Sie **SAML SSO-**.
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um FreshService** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **SAML-Anmelde-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um FreshService** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    5.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Sicherheitszertifikats** Textfeld.
    
        >[AZURE.TIP]Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei FreshService anmelden können, müssen sie in FreshService bereitgestellt werden.  
Im Fall von FreshService ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **FreshService** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

3.  In der **Benutzerverwaltung** auf **anfordernde Personen**.

    ![Anfordernde Personen](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")

4.  Klicken Sie auf **Neuer Anforderer**.

    ![Neue anfordernde Personen](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")

5.  In der **Neuer Anforderer** führen die folgenden Schritte aus:

    ![Neue anfordernde Person](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")

    1.  Geben Sie die **Vorname** und **E-Mail** Attribute eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE] Der Azure Active Directory-Kontoinhaber erhalten eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird

>[AZURE.NOTE] Können Sie alle anderen FreshService Benutzerkonten oder APIs von FreshService Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie FreshService Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** FreshService ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit NetDocuments | Microsoft Azure" 
    description="Erfahren Sie, wie Sie NetDocuments mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit NetDocuments
  
In diesem Tutorial wird die Integration von Azure und NetDocuments erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen NetDocuments-Mandanten
  
Nach Abschluss dieses Lernprogramms NetDocuments zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer NetDocuments-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für NetDocuments
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenario")
##Aktivieren der Anwendungsintegration für NetDocuments
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für NetDocuments aktivieren.

###So aktivieren Sie die Anwendungsintegration für NetDocuments

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **NetDocuments**.

    ![Anwendungskatalog](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **NetDocuments**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei NetDocuments zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für NetDocuments müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **NetDocuments** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei NetDocuments anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** führen die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configure App URL")

    1.  In der **-Anmelde-URL** Textfeld verwendete URL von den Benutzern zur Anmeldung bei Ihrer NetDocuments-Anwendung (z. B.: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  In der **NetDocuments-Antwort-URL** Textfeld Geben Sie den gleichen Wert, die Sie, in eingegeben haben der die **Anmelde-URL** Textfeld.  

        >[AZURE.NOTE]Sie finden den richtigen Wert am Ende der **Federated Identity** Dialogfeld (Siehe Screenshot für Schritt 9).

    3.  Klicken Sie auf **Weiter**

4.  Auf der **einmaliges Anmelden konfigurieren, um NetDocuments** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der NetDocuments-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Admin**.

7.  Klicken Sie auf **Hinzufügen und Entfernen von Benutzern und Gruppen**.

    ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")

8.  Klicken Sie auf **Erweiterte Authentifizierungsoptionen konfigurieren**.

    ![Erweiterte Authentifizierungsoptionen konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configure advanced authentication options")

9.  Auf **Identitätsverbund** im Dialogfeld die folgenden Schritte ausführen:

    ![Identitätsverbund](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federated Identitty")

    1.  Als **Servertyp des Identitätsverbunds**, Option **Active Directory Federation Services**.
    2.  Klicken Sie auf **Choose File**, um die heruntergeladene Metadatendatei hochzuladen.
    3.  Klicken Sie auf **OK**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei NetDocuments anmelden können, müssen sie in NetDocuments bereitgestellt werden.  
Im Fall von NetDocuments ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich auf Ihre **NetDocuments** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")

3.  Klicken Sie auf **Hinzufügen und Entfernen von Benutzern und Gruppen**.

    ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")

4.  In der **e-Mail-Adresse** Textfeld Geben Sie die e-Mail-Adresse eines gültigen Azure Active Directory-Kontos Sie bereitstellen möchten, und klicken Sie dann auf **Benutzer hinzufügen**.

    ![E-Mail-Adresse](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Email Address")

    >[AZURE.NOTE]Der Azure Active Directory-Kontoinhaber erhält eine e-Mail, die einen zum Bestätigen des Kontos Link, bevor es aktiviert wird.

>[AZURE.NOTE]Können Sie alle anderen NetDocuments Benutzerkonten oder APIs von NetDocuments Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie NetDocuments Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** NetDocuments ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


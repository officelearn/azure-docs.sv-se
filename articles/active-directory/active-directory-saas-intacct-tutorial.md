<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Intacct | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Intacct mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Intacct
  
In diesem Tutorial wird die Integration von Azure und Intacct erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Intacct-Mandanten
  
Nach Abschluss dieses Lernprogramms Intacct zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Intacct-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Intacct
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")
##Aktivieren der Anwendungsintegration für Intacct
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Intacct aktivieren.

###So aktivieren Sie die Anwendungsintegration für Intacct

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-intacct-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Intacct**.

    ![Anwendungskatalog](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Intacct**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Intacct zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Intacct** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Intacct anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Intacct-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://Intacct.com/company*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Intacct konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Intacct-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf das **Unternehmen** und klicken Sie dann auf **Unternehmensinformationen**.

    ![Unternehmen](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")

7.  Klicken Sie auf die **Sicherheit** Registerkarte, und klicken Sie dann auf **Bearbeiten**.

    ![Sicherheit](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")

8.  In der **einmaliges Anmelden (SSO)** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")

    1.  Wählen Sie **einmaliges Anmelden aktivieren**.
    2.  Als **identitätsanbietertyp**, auf **SAML 2.0**.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Intacct konfigurieren** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Intacct konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **Zertifikat** Textbox
    7.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Intacct anmelden können, müssen sie in Intacct bereitgestellt werden.  
Im Fall von Intacct ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Intacct** Mandanten.

2.  Klicken Sie auf das **Unternehmen** und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")

3.  Klicken Sie auf die **Hinzufügen** Registerkarte

    ![Hinzufügen](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")

4.  In der **Benutzerinformationen** führen die folgenden Schritte aus:

    ![Benutzerinformationen](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

    1.  Typ der **Benutzer-ID**, die **Nachname**, **First Name**, die **e-Mail-Adresse**, der **Titel** und die **Phone** eines Azure AD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie die **Administratorrechte** eines Azure AD-Kontos, das Sie bereitstellen möchten.
    3.  Klicken Sie auf **Speichern**.
        
        >[AZURE.NOTE] AAD-Kontoinhaber erhält eine e-Mail und folgt einem Link, um ihr Konto zu bestätigen, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Intacct Benutzerkonten oder APIs von Intacct Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Intacct Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Intacct ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


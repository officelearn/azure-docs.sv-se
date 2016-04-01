<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Egnyte | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Egnyte mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Egnyte
  
In diesem Tutorial wird die Integration von Azure und Egnyte erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Egnyte-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Egnyte zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Egnyte-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Egnyte
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenario")
##Aktivieren der Anwendungsintegration für Egnyte
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Egnyte aktivieren.

###So aktivieren Sie die Anwendungsintegration für Egnyte

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Egnyte**.

    ![Anwendungskatalog](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Egnyte**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Egnyte zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Egnyte** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Egnyte anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Egnyte-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.egnyte.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Egnyte konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Egnyte-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Settings")

7.  Klicken Sie im Menü auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Settings")

8.  Klicken Sie auf die **Konfiguration** Registerkarte, und klicken Sie dann auf **Security**.

    ![Sicherheit](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Security")

9.  In der **Single Sign-On Authentication** führen die folgenden Schritte aus:

    ![SSO-Authentifizierung](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")

    1.  Als **Authentifizierung mit einmaligem Anmelden**, Option **SAML 2.0**.
    2.  Als **Identitätsanbieter**, auf **AzureAD**.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Egnyte konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die ** Identity Provider Login URL ** Textbox.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Egnyte konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **Identity Provider Entity ID** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **das Zertifikat des Identitätsanbieters** Textfeld.
    7.  Als **Default User Mapping**, Option **e-Mail-Adresse**.
    8.  Als **domänenspezifischen Aussteller Wert**, Option **deaktiviert**.
    9.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Egnyte anmelden können, müssen sie in Egnyte bereitgestellt werden.  
Im Fall von Egnyte ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Egnyte** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Einstellungen \ > Benutzer und Gruppen**.

3.  Klicken Sie auf **Add New User**, und wählen Sie dann den Typ des Benutzers, die Sie hinzufügen möchten.

    ![Benutzer](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Users")

4.  In der **Neuer Standardbenutzer** führen die folgenden Schritte aus:

    ![Neuer Standardbenutzer](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")

    1.  Typ der **E-Mail**, **Benutzername** und andere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE] Die Azure Active Directory-Kontoinhaber erhält eine e-Mail-Benachrichtigung.

>[AZURE.NOTE] Können Sie alle anderen Egnyte Benutzerkonten oder APIs von Egnyte Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Egnyte Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Egnyte ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


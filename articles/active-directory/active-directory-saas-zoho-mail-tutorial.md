<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zoho Mail | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zoho Mail mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zoho Mail
  
In diesem Tutorial wird die Integration von Azure und Zoho Mail erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zoho Mail-Mandanten
  
Nach Abschluss dieses Lernprogramms Zoho Mail zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Zoho Mail-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zoho Mail
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario")

##Aktivieren der Anwendungsintegration für Zoho Mail
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zoho Mail aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zoho Mail:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Zoho Mail**.

    ![Anwendungskatalog](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Zoho Mail**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zoho Mail zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Zoho Mail** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Zoho Mail** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Zoho Mail-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://Company.ZohoMail.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Zoho Mail konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zoho Mail-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu der **Systemsteuerung**.

    ![Systemsteuerung](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Control Panel")

7.  Klicken Sie auf die **SAML-Authentifizierung** Registerkarte.

    ![SAML-Authentifizierung](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML Authentication")

8.  In der **SAML Authentication Details** führen die folgenden Schritte aus:

    ![SAML-Authentifizierungsdetails](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML Authentication Details")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Zoho Mail konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei Zoho Mail konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Zoho Mail konfigurieren** Kopieren der **Kennwort-URL ändern** -Wert aus, und fügen Sie ihn in die **Kennwort-URL ändern** Textfeld.
    4.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    5.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **PublicKey** Textfeld.
    6.  Als **Algorithmus**, Option **RSA**.
    7.  Klicken Sie auf **OK**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Zoho Mail anmelden können, müssen sie in Zoho Mail bereitgestellt werden.  
Im Fall von Zoho Mail ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Zoho Mail** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Control Panel \ > E-Mail und Dokumente**.

3.  Wechseln Sie zu **Benutzerdetails \ > Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Add User")

4.  Auf der **Hinzufügen von Benutzern** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer hinzufügen](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Add User")

    1.  Typ der **Vorname**, **Nachname**, **e-Mail-ID**, **Kennwort** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **OK**.  

        >[AZURE.NOTE] Die Azure Active Directory-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Zoho Mail-Benutzerkonten oder APIs von Zoho Mail-Bereitstellung von AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zoho Mail Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Zoho Mail ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


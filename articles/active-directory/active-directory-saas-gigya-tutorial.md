<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Gigya | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Gigya mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="10/20/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit Gigya
  
In diesem Tutorial wird die Integration von Azure und Gigya erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Gigya-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Gigya zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Gigya-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Gigya
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configure Single Sign-On")
##Aktivieren der Anwendungsintegration für Gigya
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Gigya aktivieren.

###So aktivieren Sie die Anwendungsintegration für Gigya

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-gigya-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-gigya-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Gigya**.

    ![Anwendungskatalog](./media/active-directory-saas-gigya-tutorial/IC789513.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Gigya**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Gigya zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Gigya** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei Gigya anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Gigya-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.gigya.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Gigya** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Gigya-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Einstellungen \ > SAML-Anmeldung**, und klicken Sie dann auf die **Hinzufügen** Schaltfläche.

    ![SAML-Anmeldung](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML Login")

7.  In der **SAML Login** führen die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML Configuration")

    1.  In den **Namen** Geben einen Namen für Ihre Konfiguration.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Gigya** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Gigya** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Single Sign-On-Dienst-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Gigya** Kopieren der **Namensbezeichnerformat** -Wert aus, und fügen Sie ihn in die **Name ID Format** Textfeld.
    5.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    6.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textfeld.
    7.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Gigya anmelden können, müssen sie in Gigya bereitgestellt werden.  
Im Fall von Gigya ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Gigya** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Admin \ > Manage Users**, und klicken Sie dann auf **Invite Users**.

    ![Benutzer verwalten](./media/active-directory-saas-gigya-tutorial/IC789535.png "Manage Users")

3.  Führen Sie im Dialogfeld „Benutzer einladen“ die folgenden Schritte aus:

    ![Invite Users](./media/active-directory-saas-gigya-tutorial/IC789536.png "Invite Users")

    1.  In der **E-Mail** Textfeld Geben Sie den e-Mail-Alias eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Benutzer einladen**.
    
        >[AZURE.NOTE] Der Azure Active Directory-Kontoinhaber erhält eine e-Mail, die einen zum Bestätigen des Kontos Link, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Gigya Benutzerkonten oder APIs von Gigya Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Gigya Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Gigya ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-gigya-tutorial/IC789537.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-gigya-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


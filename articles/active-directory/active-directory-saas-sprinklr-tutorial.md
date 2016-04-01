<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Sprinklr | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Sprinklr mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Sprinklr
  
In diesem Tutorial wird die Integration von Azure und Sprinklr erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Sprinklr-Mandant
  
Nach Abschluss dieses Lernprogramms Sprinklr zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Sprinklr-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Sprinklr
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

##Aktivieren der Anwendungsintegration für Sprinklr
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sprinklr aktivieren.

###So aktivieren Sie die Anwendungsintegration für Sprinklr:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Sprinklr**.

    ![Anwendungskatalog](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Sprinklr**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sprinklr zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Sprinklr** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Sprinklr anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Sprinklr-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>.sprinklr.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Sprinklr** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Sprinklr-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Verwaltung \ > Einstellungen**.

    ![Verwaltung](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  Wechseln Sie zu **Verwalten Partner \ > einmalige** auf im linken Bereich.

    ![Partner verwalten](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Manage Partner")

8.  Klicken Sie auf **+ Add Single Sign Ons**.

    ![Einmaliges Anmelden](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Single Sign-Ons")

9.  Auf der **Single Sign-on** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Single Sign-Ons")

    1.  In den **Namen** Geben einen Namen für die Konfiguration (z. B.: *WAADSSOTest*).
    2.  Wählen Sie **aktiviert**.
    3.  Wählen Sie **Verwenden Sie new SSO Certificate**.
    4.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    5.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **das Zertifikat des Identitätsanbieters** Textfeld
    6.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Sprinklr** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **Entitäts-Id** Textfeld.
    7.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Sprinklr** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Login URL** Textfeld.
    8.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Sprinklr** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Logout URL** Textfeld.
    9.  Als **SAML User ID Type**, Option **Assertion contains User "'s sprinklr.com Username**.
    10. Als **SAML User ID Locationden**, auf **Benutzer-ID wird der Name Identifier Element der betreffanweisung**.
    11. Schließen **Speichern**.

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configure single sign-on")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich AAD-Benutzer anmelden können, müssen sie für den Zugriff innerhalb der Sprinklr-Anwendung bereitgestellt werden.  
In diesem Abschnitt wird beschrieben, wie in Sprinklr AAD-Benutzerkonten erstellt werden.

###Führen Sie zum Bereitstellen von Benutzerkonten in Sprinklr die folgenden Schritte aus:

1.  Melden Sie sich bei der Sprinklr-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Verwaltung \ > Einstellungen**.

    ![Verwaltung](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  Wechseln Sie zu **Clientcomputer verwalten \ > Benutzer** im linken Bereich.

    ![Einstellungen](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Einstellungen](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")

5.  Auf der **Edit User** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer bearbeiten](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Edit user")

    1.  In der **E-Mail**, **First Name** und **Nachname** Textfelder ein, geben Sie die Informationen eines Azure AD-Benutzerkontos, die Sie bereitstellen möchten.
    2.  Wählen Sie **Password Disabled**.
    3.  Wählen Sie eine **Language**.
    4.  Wählen Sie eine **Benutzertyp**.
    5.  Klicken Sie auf **Update**.

    >[AZURE.IMPORTANT] **Kennwort deaktiviert** muss ausgewählt werden, damit einen Benutzer über einen Identitätsanbieter anmelden.

6.  Wechseln Sie zu **Rolle**, und führen Sie dann die folgenden Schritte aus:

    ![Partnerrollen](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner Roles")

    1.  Aus der **Global** Liste **ALL\_Permissions**.
    2.  Klicken Sie auf **Update**.

>[AZURE.NOTE] Können Sie alle anderen Sprinklr Benutzerkonten oder APIs von Sprinklr zum Bereitstellen von Azure AD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Sprinklr Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Sprinklr ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit TeamSeer | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie TeamSeer mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit TeamSeer
  
In diesem Tutorial wird die Integration von Azure und TeamSeer erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein TeamSeer-Mandant
  
Nach Abschluss dieses Lernprogramms TeamSeer zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer TeamSeer-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für TeamSeer
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

##Aktivieren der Anwendungsintegration für TeamSeer
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für TeamSeer aktivieren.

###So aktivieren Sie die Anwendungsintegration für TeamSeer:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **TeamSeer**.

    ![Anwendungskatalog](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **TeamSeer**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei TeamSeer zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **TeamSeer** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei TeamSeer anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **TeamSeer-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://www.teamseer.com/companyid*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei TeamSeer konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der TeamSeer-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **HR Admin**.

    ![Personalverwaltung](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")

7.  Klicken Sie auf **Setup**.

    ![Einrichtung](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Setup")

8.  Klicken Sie auf **Richten Sie die Details der SAML-Anbieter**.

    ![SAML-Einstellungen](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")

9.  Führen Sie im Abschnitt „SAML-Anbieterdetails“ die folgenden Schritte aus:

    ![SAML Settings](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei TeamSeer konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **URL** Textfeld.
    2.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    3.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **IdP Public Certificate** Textfeld.

10. Führen Sie zum Abschluss der SAML-Anbieterkonfiguration die folgenden Schritte aus:

    ![SAML-Einstellungen](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")

    1.  In der **Test-e-Mail-Adressen**, geben Sie die e-Mail-Adresse des Testbenutzers.
    2.  In der **Aussteller** Textfeld Geben Sie den Aussteller-URL des Dienstanbieters.
    3.  Klicken Sie auf **Speichern**.

11. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei TeamSeer anmelden können, müssen sie in TeamSeer bereitgestellt werden.  
Im Fall von TeamSeer ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **TeamSeer** -Unternehmenswebsite als Administrator.

2.  Führen Sie die folgenden Schritte aus:

    ![Personalverwaltung](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")

    1.  Wechseln Sie zu **HR Admin \ > Benutzer**.
    2.  Klicken Sie auf **Führen Sie den Assistenten für neue Benutzer**.

3.  In der **Benutzerdetails** führen die folgenden Schritte aus:

    ![Benutzerdetails](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")

    1.  Typ der **Vorname**, **Surname**, **Benutzernamen (e-Mail-Adresse)** eines gültigen AAD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Weiter**.

4.  Führen Sie die Anweisungen auf dem Bildschirm zum Hinzufügen eines neuen Benutzers aus, und klicken Sie auf **Fertig stellen**.

>[AZURE.NOTE] Können Sie alle anderen TeamSeer Benutzerkonten oder APIs von TeamSeer zum Bereitstellen von Azure AD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie TeamSeer Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** TeamSeer ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


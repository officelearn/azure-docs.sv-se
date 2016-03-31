<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit InsideView | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie InsideView mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit InsideView
  
In diesem Tutorial wird die Integration von Azure und InsideView erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen InsideView-Mandanten
  
Nach Abschluss dieses Lernprogramms Inside View zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Inside View-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für InsideView
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")
##Aktivieren der Anwendungsintegration für InsideView
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für InsideView aktivieren.

###So aktivieren Sie die Anwendungsintegration für InsideView

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **InsideView**.

    ![Anwendungskatalog](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **InsideView**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei InsideView zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **InsideView** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")

2.  Auf der **wie sollen sich Benutzer bei InsideView anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")

3.  Auf der **App-URL konfigurieren** auf der Seite der **InsideView-Antwort-URL** Textfeld Geben Sie Ihre InsideView-SSO-URL (z. B.: `https://my.insideview.com/iv/<STS Name>/login.iv`), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden für insideview** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der InsideView-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Admin**, **Einstellungen für die einmalige Anmeldung**, und klicken Sie dann auf **SAML hinzufügen**.

    ![SAML-Einstellungen für einmaliges Anmelden](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")

7.  In der **neue SAML hinzufügen** führen die folgenden Schritte aus:

    ![Neue SAML hinzufügen](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")

    1.  In der **STS-Name** Textfeld Geben Sie einen Namen für Ihre Konfiguration.
    2.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden für insideview** Kopieren der **Service Provider (SP) initiierter Endpunkt** -Wert aus, und fügen Sie ihn in die **SamlP/WS-Fed Unsolicated EndPoint** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **STS Certificate** Textbox
    5.  In der **Crm User Id Mapping** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  In der **Crm Email Mapping** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  In der **Crm First Name Mapping** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  In der **Crm LastName Mapping** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei InsideView anmelden können, müssen sie in InsideView bereitgestellt werden.  
Im Fall von InsideView ist die Bereitstellung eine manuelle Aufgabe.
  
Um in InsideView erstellten Benutzer oder Kontakte abzurufen, wenden Sie sich an Ihren Kundenbetreuer, oder senden Sie eine e-Mail an **support@insideview.com**

>[AZURE.NOTE] Können Sie alle anderen InsideView Benutzerkonten oder APIs von InsideView zum Bereitstellen von Azure AD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie InsideView Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** InsideView ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


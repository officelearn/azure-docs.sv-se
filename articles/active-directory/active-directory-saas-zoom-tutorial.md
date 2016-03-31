<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zoom | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zoom mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Zoom
  
In diesem Tutorial wird die Integration von Azure und Zoom erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zoom-Mandanten
  
Nach Abschluss dieses Lernprogramms Zoom zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Zoom-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zoom
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenario")

##Aktivieren der Anwendungsintegration für Zoom
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zoom aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zoom:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-zoom-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-zoom-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zoom-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Zoom**.

    ![Anwendungskatalog](./media/active-directory-saas-zoom-tutorial/IC784694.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Zoom**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zoom zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Zoom** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784696.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Zoom anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784697.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Zoom-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*http://company.zoom.us*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784698.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um Zoom** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784699.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zoom-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf die **Single Sign-On** Registerkarte.

    ![Einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784700.png "Single sign-on")

7.  Klicken Sie auf die **Sicherheitskontrolle** Registerkarte, und fahren Sie mit der **Single Sign-On** Einstellungen.

8.  Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784701.png "Single sign-on")

    1.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Zoom** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in der **-in-Seiten-URL** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Zoom** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **URL der Abmeldeseite** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **das Zertifikat des Identitätsanbieters** Textbox
    5.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Zoom** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textfeld.
    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784702.png "Configure single sign-on")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Zoom anmelden können, müssen sie in Zoom bereitgestellt werden.  
Im Fall von Zoom ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Zoom** -Unternehmenswebsite als Administrator.

2.  Klicken Sie auf die **Kontenverwaltung** Registerkarte, und klicken Sie dann auf **User Management**.

3.  Klicken Sie im Abschnitt Verwaltung auf **Hinzufügen von Benutzern**.

    ![Benutzerverwaltung](./media/active-directory-saas-zoom-tutorial/IC784703.png "User management")

4.  Auf der **Hinzufügen von Benutzern** führen die folgenden Schritte aus:

    ![Hinzufügen von Benutzern](./media/active-directory-saas-zoom-tutorial/IC784704.png "Add users")

    1.  Als **Benutzertyp**, Option **grundlegende**.
    2.  In der **-e-Mails** Textfeld Geben Sie die e-Mail-Adresse eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    3.  Klicken Sie auf **Hinzufügen**.

>[AZURE.NOTE] Können Sie alle anderen Zoom Benutzerkonten oder APIs durch Bereitstellung von AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zoom Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Zoom ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-zoom-tutorial/IC784705.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zoom-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Kudos | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Kudos mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Kudos
  
In diesem Tutorial wird die Integration von Azure und Kudos erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Kudos-Mandanten
  
Nach Abschluss dieses Lernprogramms Kudos zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer Kudos-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Kudos
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Scenario")
##Aktivieren der Anwendungsintegration für Kudos
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Kudos aktivieren.

###So aktivieren Sie die Anwendungsintegration für Kudos

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-kudos-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-kudos-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-kudos-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Kudos**.

    ![Anwendungskatalog](./media/active-directory-saas-kudos-tutorial/IC787800.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Kudos**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Kudos zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Kudos** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Kudos anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Kudos-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://company.kudosnow.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Kudos konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Kudos-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")

7.  Klicken Sie auf **Integrationen \ > SSO**.

8.  In der **SSO** führen die folgenden Schritte aus:

    ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei Kudos konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die ** Anmelde-URL ** Textbox.
    2.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]
        Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    3.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Kudos konfigurieren** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die ** Abmelde-URL ** Textbox.
    5.  In der **Ihre Kudos-URL** Textfeld Geben Sie den Namen Ihres Unternehmens.
    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Kudos anmelden können, müssen sie in Kudos bereitgestellt werden.  
Im Fall von Kudos ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Kudos** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Menü am oberen Rand **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")

3.  Klicken Sie auf **Benutzername Admin**.

4.  Klicken Sie auf die **Benutzer** und klicken Sie dann auf **Hinzufügen eines Benutzers**.

    ![Benutzeradministrator](./media/active-directory-saas-kudos-tutorial/IC787809.png "User Admin")

5.  In der **Hinzufügen eines Benutzers** führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-kudos-tutorial/IC787810.png "Add a User")

    1.  Typ der **Vorname**, **Nachname**, **E-Mail** und andere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **erstellen Benutzer**.

>[AZURE.NOTE]Können Sie alle anderen Kudos Benutzerkonten oder APIs von Kudos Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Kudos Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Kudos ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-kudos-tutorial/IC787811.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-kudos-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit ClickTime | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ClickTime mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit ClickTime

In diesem Lernprogramm wird die Integration von Azure und ClickTime erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen ClickTime-Mandanten

Nach Abschluss dieses Lernprogramms ClickTime zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer ClickTime-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ClickTime
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Scenario")
##Aktivieren der Anwendungsintegration für ClickTime

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ClickTime aktivieren.

###So aktivieren Sie die Anwendungsintegration für ClickTime

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ClickTime**.

    ![Anwendungskatalog](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **ClickTime**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ClickTime zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren ClickTime-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Um auf Ihren ClickTime-Mandanten einmaliges Anmelden konfigurieren zu können, müssen Sie zuerst erhalten Sie technischen Support von ClickTime zum Abrufen dieses Feature aktiviert ist.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ClickTime** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer bei ClickTime anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configure single sign-on")

3.  Auf der **einmaliges Anmelden konfigurieren, um ClickTime** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configure single sign-on")

4.  Melden Sie sich in einem anderen Webbrowserfenster bei der ClickTime-Unternehmenswebsite als Administrator an.

5.  Klicken Sie in der Symbolleiste am oberen Rand auf **Voreinstellungen**, und klicken Sie dann auf **Security Settings**.

6.  In der **Voreinstellungen für einmaliges Anmelden** Konfiguration führen die folgenden Schritte aus:

    ![Security Settings](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Security Settings")

    1.  Wählen Sie **Zulassen** Melden Sie sich mit einmaliges Anmelden (SSO) mit **OneLogin**.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um ClickTime** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Endpoint** Textfeld.
    3.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    4.  Öffnen Sie das Base64-codierte Zertifikat in **Editor**, kopieren Sie den Inhalt, und fügen Sie ihn in die **x. 509-Zertifikat** Textfeld.
    5.  Klicken Sie auf **Speichern**.

7.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ClickTime anmelden können, müssen sie in ClickTime bereitgestellt werden.  
Im Fall von ClickTime ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **ClickTime** Mandanten.

2.  Klicken Sie in der Symbolleiste am oberen Rand auf **Unternehmen**, und klicken Sie dann auf **Personen**.

    ![Personen](./media/active-directory-saas-clicktime-tutorial/IC777282.png "People")

3.  Klicken Sie auf **Person hinzufügen**.

    ![Add Person](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Add Person")

4.  Führen Sie im Abschnitt "New Person" die folgenden Schritte aus:

    ![Personen](./media/active-directory-saas-clicktime-tutorial/IC777284.png "People")

    1.  In der **e-Mail-Adresse** Textfeld Geben Sie die e-Mail-Adresse Ihres Azure AD-Kontos.
    2.  In der **Vollständiger Name** Textfeld Geben Sie den Namen Ihres Azure AD-Kontos.  

        >[AZURE.NOTE] Wenn Sie möchten, können Sie zusätzliche Eigenschaften des neuen Personenobjekts festlegen.

    3.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen ClickTime Benutzerkonten oder APIs von ClickTime Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ClickTime Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ClickTime ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



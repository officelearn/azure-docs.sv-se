<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit BambooHR | Microsoft Azure" 
    description="Erfahren Sie, wie Sie BambooHR mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit BambooHR

In diesem Lernprogramm wird die Integration von Azure und BambooHR erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein BambooHR-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms BambooHR zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer BambooHR-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für BambooHR
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")
##Aktivieren der Anwendungsintegration für BambooHR

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für BambooHR aktivieren.

###So aktivieren Sie die Anwendungsintegration für BambooHR

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **BambooHR**.

    ![Anwendungskatalog](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **BambooHR**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei BambooHR zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **BambooHR** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Szenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")

2.  Auf der **wie sollen sich Benutzer bei BambooHR anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **BambooHR-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zur Anmeldung bei der BambooHR-Anwendung (z. B.: https://company.bamboohr.com), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configure app URL")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden bei BambooHR** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der BambooHR-Unternehmenswebsite als Administrator an.

6.  Führen Sie auf der Startseite die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")

    1.  Klicken Sie auf **Apps**.
    2.  Klicken Sie auf das Menü apps auf der linken Seite auf **Single Sign-On**.
    3.  Klicken Sie auf **SAML Single Sign-On**.

7.  In der **SAML Single Sign-On** führen die folgenden Schritte aus:

    ![SAML Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  In Azure-Portal auf der **Konfigurieren Sie einmaliges Anmelden bei BambooHR** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die ** SSO-Anmelde-URL ** Textbox.
    2.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    3.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **x. 509-Zertifikat** Textbox
    4.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei BambooHR anmelden können, müssen sie in BambooHR bereitgestellt werden.  
Im Fall von BambooHR ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **BambooHR** Website als Administrator.

2.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**.

    ![Einstellung](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Setting")

3.  Klicken Sie auf **Übersicht**.

4.  Wechseln Sie im linken Navigationsbereich zur **Sicherheit \ > Benutzer**.

5.  Geben Sie Benutzername, Kennwort und E-Mail-Adresse eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.

6.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen BambooHR Benutzerkonten oder APIs von BambooHR Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie BambooHR Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** BambooHR ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



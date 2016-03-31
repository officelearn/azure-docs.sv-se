<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Bime | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Bime mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Bime

In diesem Lernprogramm wird die Integration von Azure und Bime erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Bime-Mandanten

Nach Abschluss dieses Lernprogramms Bime zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Bime-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Bime
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario")
##Aktivieren der Anwendungsintegration für Bime

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Bime aktivieren.

###So aktivieren Sie die Anwendungsintegration für Bime

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-bime-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-bime-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Bime**.

    ![Anwendungskatalog](./media/active-directory-saas-bime-tutorial/IC775553.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Bime**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Bime zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Bime müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Bime** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bime-tutorial/IC771709.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei Bime anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bime-tutorial/IC775555.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Bime-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Bimeapp.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-bime-tutorial/IC775556.png "Configure App URL")

4.  Auf der **einmaliges Anmelden für bime konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Bime.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bime-tutorial/IC775557.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Bime-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste auf **Admin**, und klicken Sie dann **Konto**.

    ![Admin](./media/active-directory-saas-bime-tutorial/IC775558.png "Admin")

7.  Führen Sie auf der Kontenkonfigurationsseite die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bime-tutorial/IC775559.png "Configure Single Sign-On")

    1.  Wählen Sie **SAML-Authentifizierung aktivieren**.
    2.  In Azure-Portal auf der **einmaliges Anmelden für bime konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Anmelde-URL** Textfeld.
    3.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    4.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-bime-tutorial/IC775560.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Bime anmelden können, müssen sie in Bime bereitgestellt werden.  
Im Fall von Bime ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **Bime** Mandanten.

2.  Klicken Sie in der Symbolleiste auf **Admin**, und klicken Sie dann **Benutzer**.

    ![Admin](./media/active-directory-saas-bime-tutorial/IC775561.png "Admin")

3.  In der **Benutzerliste**, klicken Sie auf **Add New User** ("+").

    ![Benutzer](./media/active-directory-saas-bime-tutorial/IC775562.png "Users")

4.  Auf der **Benutzerdetails** Dialogfeld führen die folgenden Schritte aus:

    ![Benutzerdetails](./media/active-directory-saas-bime-tutorial/IC775563.png "User Details")

    1.  Geben Sie den "Vornamen", den "Nachnamen", den "Anmeldenamen" und die "E-Mail-Adresse" eines gültigen AAD-Benutzerkontos ein, das Sie bereitstellen möchten.
    2.  Klicken Sie auf Speichern.

>[AZURE.NOTE] Können Sie alle anderen Bime Benutzerkonten oder APIs von Bime Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Bime Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Bime ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-bime-tutorial/IC775564.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-bime-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



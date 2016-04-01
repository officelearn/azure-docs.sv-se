<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit AnswerHub | Microsoft Azure" 
    description="Erfahren Sie, wie Sie AnswerHub mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit AnswerHub

In diesem Lernprogramm wird die Integration von Azure und AnswerHub erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AnswerHub-Abonnement, für das das einmalige Anmelden aktiviert ist.

Nach Abschluss dieses Lernprogramms AnswerHub zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer AnswerHub-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AnswerHub
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")
##Aktivieren der Anwendungsintegration für AnswerHub

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AnswerHub aktivieren.

###So aktivieren Sie die Anwendungsintegration für AnswerHub

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **AnswerHub**.

    ![Anwendungskatalog](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **AnswerHub**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AnswerHub zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **AnswerHub** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei AnswerHub** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **AnswerHub Sign In URL** Textfeld Ihre URL nach dem Muster "*https://company.answerhub.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei AnswerHub konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AnswerHub-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Verwaltung**.

7.  Klicken Sie auf die **Benutzer- und** Registerkarte.

8.  Klicken Sie im Navigationsbereich auf der linken Seite in der **Social Settings** auf **SAML-Setup**.

9.  Klicken Sie auf **IDP Config** Registerkarte.

10. Auf der **IDP Config** führen die folgenden Schritte aus:

    ![SAML Setup](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")

    1.  In Azure-Portal auf der **einmaliges Anmelden bei AnswerHub konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **IDP-Anmelde-URL** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden bei AnswerHub konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **IDP-Abmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei AnswerHub konfigurieren** Kopieren der **Namensbezeichnerformat** -Wert aus, und fügen Sie ihn in die **IDP-Namensbezeichnerformat** Textfeld.
    4.  Klicken Sie auf **Schlüssel und Zertifikate**.

11. Führen Sie auf der Registerkarte "Keys and Certificates" die folgenden Schritte aus:

    ![Keys and Certificates](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")

    1.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    2.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **IDP Public Key (x 509 Format)** Textfeld.
    3.  Klicken Sie auf **Speichern**.

12. Auf der **IDP Config** auf **Speichern**.

13. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AnswerHub anmelden können, müssen sie in AnswerHub bereitgestellt werden.  
Im Fall von AnswerHub ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **AnswerHub** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Verwaltung**.

3.  Klicken Sie auf die **Benutzer und Gruppen** Registerkarte.

4.  Klicken Sie im Navigationsbereich auf der linken Seite in der **Benutzer verwalten** auf **Erstellen oder Importieren von Benutzern**.

    ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")

5.  Typ der **e-Mail-Adresse**, **Benutzername** und **Kennwort** eines gültigen Azure Active Directory-Kontos sollen Bereitstellen in die entsprechenden Textfelder ein, und klicken Sie dann auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen AnswerHub Benutzerkonten oder APIs von AnswerHub Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AnswerHub Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** AnswerHub ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



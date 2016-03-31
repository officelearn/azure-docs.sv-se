<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit ITRP | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie ITRP mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit ITRP
  
In diesem Tutorial wird die Integration von Azure und ITRP erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen ITRP-Mandanten
  
Nach Abschluss dieses Lernprogramms ITRP zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer ITRP-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ITRP
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario")
##Aktivieren der Anwendungsintegration für ITRP
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ITRP aktivieren.

###So aktivieren Sie die Anwendungsintegration für ITRP

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-itrp-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-itrp-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ITRP**.

    ![Anwendungskatalog](./media/active-directory-saas-itrp-tutorial/IC775565.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **ITRP**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ITRP zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für ITRP müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ITRP** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer bei ITRP anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **ITRP-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. ITRP.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configure App URL")

4.  Auf der **einmaliges Anmelden für itrp konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\ITRP.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ITRP-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Wählen Sie im linken Navigationsbereich **Single Sign-On**.

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8.  Führen Sie im Konfigurationsabschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")

    ![Einmaliges Anmelden](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")

    1.  Klicken Sie auf **aktivieren**.
    2.  In Azure-Portal auf der **einmaliges Anmelden für itrp konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote-Abmelde-URL** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden für itrp konfigurieren** Kopieren der **SAML-SSO-URL** -Wert aus, und fügen Sie ihn in die **SAML-SSO-URL** Textfeld.
    4.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Zertifikatfingerabdruck** Textfeld.
        
        >[AZURE.TIP]Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    5.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei ITRP anmelden können, müssen sie in ITRP bereitgestellt werden.  
Im Fall von ITRP ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **ITRP** Mandanten.

2.  Klicken Sie in der Symbolleiste am oberen Rand auf **Datensätze**.

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3.  Wählen Sie im Popupmenü **Personen**.

    ![Personen](./media/active-directory-saas-itrp-tutorial/IC775587.png "People")

4.  Klicken Sie auf **neue Person hinzufügen** ("+").

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5.  Führen Sie im Dialogfeld „Neue Person hinzufügen“ die folgenden Schritte aus:

    ![Benutzer](./media/active-directory-saas-itrp-tutorial/IC775577.png "User")

    1.  Typ der **Namen**, **E-Mail** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen ITRP Benutzerkonten oder APIs von ITRP Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ITRP Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ITRP ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-itrp-tutorial/IC775588.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-itrp-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit xMatters OnDemand | Microsoft Azure" description="Hier erfahren Sie, wie Sie xMatters OnDemand mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit xMatters OnDemand
  
In diesem Tutorial wird die Integration von Azure und xMatters OnDemand erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen xMatters OnDemand-Mandanten
  
Nach Abschluss dieses Lernprogramms xMatters OnDemand zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer xMatters OnDemand-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für xMatters OnDemand
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

##Aktivieren der Anwendungsintegration für xMatters OnDemand
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für xMatters OnDemand aktivieren.

###So aktivieren Sie die Anwendungsintegration für xMatters OnDemand

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **xMatters OnDemand**.

    ![Anwendungskatalog](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **XMatters OnDemand**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei xMatters OnDemand zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **XMatters OnDemand** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei XMatters OnDemand** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **XMatters OnDemand-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. XMattersOnDemandapp.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")

4.  Auf der **einmaliges Anmelden für XMatters OnDemand konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Sie müssen das Zertifikat an das xMatters-Supportteam weiterleiten. Das Zertifikat muss vom xMatters-Supportteam hochgeladen werden, bevor Sie die Konfiguration der einmaligen Anmeldung abschließen können.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der xMatters OnDemand-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste am oberen Rand auf **Admin**, und klicken Sie dann auf **Unternehmensdetails** in der Navigationsleiste auf der linken Seite.

    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  Auf der **SAML-Konfiguration** führen die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")

    1.  Wählen Sie **SAML aktivieren**.
    2.  In Azure-Portal auf der **einmaliges Anmelden für XMatters OnDemand konfigurieren** Kopieren der **Identitätsanbieter-ID** -Wert aus, und fügen Sie ihn in die **Identitätsanbieter-ID** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden für XMatters OnDemand konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Single Sign On URL** Textbox.
    4.  In Azure-Portal auf der **einmaliges Anmelden für XMatters OnDemand konfigurieren** Kopieren der **-Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **Single Logout URL** Textfeld.
    5.  Klicken Sie auf der Seite Ihres Unternehmens oben **Speichern**.
        ![Unternehmensdetails](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

##Konfigurieren der Benutzerbereitstellung
  
Um Azure AD-Benutzern die Anmeldung bei xMatters OnDemand zu ermöglichen, müssen sie in xMatters OnDemand bereitgestellt werden.  
Im Fall von xMatters OnDemand ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **XMatters OnDemand** Mandanten.

2.  Klicken Sie auf die **Benutzer** Registerkarte.

3.  Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4.  Wählen Sie **Active**.

5.  In der **Hinzufügen eines Benutzers** führen die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")

    1.  Geben Sie die **UserID**, **First Name**, **Nachname**, **Website** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen XMatters OnDemand Benutzerkonten oder APIs von XMatters OnDemand Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie xMatters OnDemand Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** XMatters OnDemand ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


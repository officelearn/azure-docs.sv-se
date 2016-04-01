<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit OfficeSpace Software | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie OfficeSpace Software mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit OfficeSpace Software
  
In diesem Tutorial wird die Integration von Azure und OfficeSpace Software erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein OfficeSpace Software-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms OfficeSpace Software zugewiesenen Azure AD-Benutzer werden mit der Anwendung auf Ihrer OfficeSpace Software-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für OfficeSpace Software
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenario")
##Aktivieren der Anwendungsintegration für OfficeSpace Software
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für OfficeSpace Software aktivieren.

###So aktivieren Sie die Anwendungsintegration für OfficeSpace Software:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **OfficeSpace Software**.

    ![Anwendungskatalog](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **OfficeSpace Software**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei OfficeSpace Software zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für OfficeSpace Software müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **OfficeSpace Software** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configure single sign=on")

2.  Auf der **wie sollen sich Benutzer bei OfficeSpace Software anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configure single sign-on")

3.  Auf der **App-URL konfigurieren** auf der Seite der **OfficeSpace Software-Anmelde-URL** Textfeld die URL ein, Ihre Benutzer zur Anmeldung bei der OfficeSpace Software-Anwendung (z. B.: "*https://company.officespacesoftware.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um OfficeSpace Software** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der OfficeSpace Software-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Admin \ > Connectors**.

    ![Admin](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Admin")

7.  Klicken Sie auf **SAML Authorization**.

    ![Connectors](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connectors")

8.  In der **SAML Authorization** führen die folgenden Schritte aus:

    ![SAML-Konfiguration](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "SAML configuration")

    1.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um OfficeSpace Software** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **Logout Provider Url** Textfeld.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um OfficeSpace Software** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in den **Client idp-Ziel-Url** Textfeld.
    3.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in den **Client idp Cert Fingerprint** Textfeld.  

        >[AZURE.TIP]
        Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    4.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configure single sign-on")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei OfficeSpace Software anmelden können, müssen sie in OfficeSpace Software bereitgestellt werden. Im Fall von OfficeSpace Software ist die Bereitstellung eine automatisierte Aufgabe.  
Für Sie steht kein Aktionselement zur Verfügung.  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE]Können Sie alle anderen OfficeSpace Software Benutzerkonten oder APIs von OfficeSpace Software Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie OfficeSpace Software Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** OfficeSpace Software ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


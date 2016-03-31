<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit PolicyStat | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie PolicyStat mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit PolicyStat
  
In diesem Tutorial wird die Integration von Azure und PolicyStat erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen PolicyStat-Mandanten
  
Nach Abschluss dieses Lernprogramms PolicyStat zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer PolicyStat-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für PolicyStat
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario")
##Aktivieren der Anwendungsintegration für PolicyStat
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für PolicyStat aktivieren.

###So aktivieren Sie die Anwendungsintegration für PolicyStat

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-policystat-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-policystat-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **PolicyStat**.

    ![Anwendungskatalog](./media/active-directory-saas-policystat-tutorial/IC808627.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **PolicyStat**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei PolicyStat zu authentifizieren.  
Die PolicyStat-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **PolicyStat** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden PolicyStat** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** auf der Seite der **Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei der URL-PolicyStat-Anwendung (z. B.: *"https://demo-azure.policystat.com"*), und klicken Sie dann auf **Weiter**.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configure App Settings")

4.  Auf der **Konfigurieren Sie einmaliges Anmelden, um PolicyStat** auf **Metadaten**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der PolicyStat-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf die **Admin** Registerkarte, und klicken Sie dann auf **Konfiguration von SSO** im linken Navigationsbereich.

    ![Administratormenü](./media/active-directory-saas-policystat-tutorial/IC808633.png "Administrator Menu")

7.  In der **Setup** Abschnitt **Aktivieren Sie einmaliges Anmelden Integration**.

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808634.png "Single Sign-On Configuration")

8.  Klicken Sie auf **Attribute konfigurieren**, und klicken Sie dann auf die **Attribute konfigurieren** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  In der **Benutzernamenattribut** Textfeld **Uid**.
    2.  In der **First Name Attribute** Textfeld **Firstname**.
    3.  In der **Last Name-Attribut** Textfeld **Lastname**.
    4.  In der **-e-Mail-Attribut** Textfeld **Emailaddress**.
    5.  Klicken Sie auf **Speichern**.

9.  Klicken Sie auf **Ihre IDP-Metadaten**, und klicken Sie dann auf die **Ihre IDP-Metadaten** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt und fügen Sie ihn in die **Ihre Identitätsanbietermetadaten** Textbox
    2.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configure Single Sign-On")

11. 12. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributes")

13. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributes")

    1.  Klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld **Uid**.
    3.  In der **Attributwert** Textfeld select **ExtractMailPrefix()**.
    4.  Aus der **Mail** Liste **User.mail**.
    5.  Klicken Sie auf **vollständige**.
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei PolicyStat anmelden können, müssen sie in PolicyStat bereitgestellt werden.  
PolicyStat unterstützt die bedarfsabhängige Benutzerbereitstellung. Das bedeutet, dass Sie die Benutzer nicht manuell zu PolicyStat hinzufügen müssen.  
Die Benutzer werden bei der ersten Anmeldung mittels einmaligem Anmelden automatisch hinzugefügt.

>[AZURE.NOTE]Können Sie alle anderen PolicyStat Benutzerkonten oder APIs von PolicyStat Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie PolicyStat Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** PolicyStat ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-policystat-tutorial/IC808636.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-policystat-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Citrix GoToMeeting | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Citrix GoToMeeting mit Azure Active Directory verwenden können, um einmaliges Anmelden, die automatisierte Bereitstellung u. v. m. zu aktivieren." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/01/2015" 
    ms.author="markvi" />

#Lernprogramm: Azure Active Directory-Integration mit Citrix GoToMeeting  
Gilt für: Azure

>[AZURE.TIP]Um Feedback zu erhalten, klicken Sie auf [hier](http://go.microsoft.com/fwlink/?LinkId=522412).

In diesem Lernprogramm wird die Integration von Azure und Citrix GoToMeeting erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandant für Citrix GoToMeeting

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Citrix GoToMeeting
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Konfiguration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")
##Aktivieren der Anwendungsintegration für Citrix GoToMeeting

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Citrix GoToMeeting aktivieren.

###So aktivieren Sie die Anwendungsintegration für Citrix GoToMeeting

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6.  In der **Suchfeld**, Typ **Citrix GoToMeeting**.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Wählen Sie im Ergebnisbereich **Citrix GoToMeeting**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Citrix GoToMeeting zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base64-codierte Zertifikatsdatei in Ihren Citrix GoToMeeting-Mandanten hochladen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  Auf der **Citrix GoToMeeting** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **EINMALIGES Anmelden konfigurieren** Dialogfeld.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2.  Auf der **wie sollen sich Benutzer anmelden bei Citrix GoToMeeting** Seite **Microsoft Azure AD Single Sign-On**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")


3. Auf der **App-Einstellungen konfigurieren** auf **Weiter**. <br><br>![Einmaliges Anmelden aktivieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4.  Auf der **einmaliges Anmelden bei Citrix GoToMeeting konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Browserfenster, in die Mitte der Citrix-Organisation ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Klicken Sie auf die **Identitätsanbieter** Registerkarte, und führen Sie die folgenden Schritte aus:  <br><br> ![SAML-setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")

     6.1. Wählen Sie **manuell**

     6.2. In Azure-Portal auf der **einmaliges Anmelden bei Citrix GoToMeeting konfigurieren** Kopieren der **-In-Seiten-URL** -Wert aus, und fügen Sie ihn in der **-in-Seiten-URL** Textbox. 

     6.3. In Azure-Portal auf der **einmaliges Anmelden bei Citrix GoToMeeting konfigurieren** Kopieren der **URL der Abmeldeseite** -Wert aus, und fügen Sie ihn in die **URL der Abmeldeseite** Textfeld.

     6.4. In Azure-Portal auf der **einmaliges Anmelden bei Citrix GoToMeeting konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **Identity Provider Entity ID** Textfeld.

     6.5. Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Zertifikat hochladen**.

     6.6. Klicken Sie auf **Speichern**.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")


7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.<br><br> ![SAML-setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")





##Konfigurieren der Benutzerbereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Citrix GoToMeeting aktivieren.

###So konfigurieren Sie die Benutzerbereitstellung

1.  In der Azure-Verwaltungsportal auf die **Citrix GoToMeeting** AHA klicken Sie auf **Benutzerbereitstellung** zum Öffnen der **Benutzerbereitstellung konfigurieren** Dialogfeld.

    ![Konfigurieren der Benutzerbereitstellung](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2.  Auf der **Einstellungen und Administratoranmeldeinformationen** führen die folgenden Schritte aus:

    ![Konfigurieren der Benutzerbereitstellung](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")

     2.1. In der **Administratorbenutzername für Citrix GoToMeeting** Textfeld Geben Sie den Benutzernamen eines Administrators.

     2.2. In der **Citrix GoToMeeting-Administratorkennwort** Textfeld, das Kennwort des Administrators.

     2.3. Klicken Sie auf **Weiter**.

3.  Auf der **Bestätigung** Seite, klicken Sie auf das Häkchen, um die Konfiguration zu speichern.

4.  Klicken Sie auf der **überprüfen** Schaltfläche, um Ihre Konfiguration zu überprüfen.
##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Citrix GoToMeeting Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der **Citrix GoToMeeting** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

Nach 10 Minuten können Sie überprüfen, ob das Konto mit Citrix GoToMeeting synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, Dashboard in der D auf die **Citrix GoToMeeting** Integration Anwendungsseite im Azure-Verwaltungsportal.

![Dashboard](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:

![Integrationsstatus](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich.

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).



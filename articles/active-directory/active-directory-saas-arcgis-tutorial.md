<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit ArcGIS | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ArcGIS mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit ArcGIS

In diesem Lernprogramm wird die Integration von Azure und ArcGIS erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein ArcGIS-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms ArcGIS zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer ArcGIS-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ArcGIS
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")
##Aktivieren der Anwendungsintegration für ArcGIS

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ArcGIS aktivieren.

###So aktivieren Sie die Anwendungsintegration für ArcGIS

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **ArcGIS**.

    ![Anwendungskatalog](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")

7.  Wählen Sie im Ergebnisbereich **ArcGIS**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ArcGIS zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **ArcGIS** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei ArcGIS anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **ArcGIS-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern, melden Sie sich mit dem folgenden Muster "*https://company.maps.arcgis.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um ArcGIS** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der ArcGIS-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Bearbeiten**.

    ![Einstellungen bearbeiten](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")

7.  Klicken Sie auf **Security**.

    ![Sicherheit](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")

8.  Unter **Enterprise Logins**, klicken Sie auf **Set Identity Provider**.

    ![Enterprise Logins](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")

9.  Auf der **Set Identity Provider** Konfiguration führen die folgenden Schritte aus:

    ![Set Identity Provider](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")

    1.  Geben Sie in das Textfeld "Name" den Namen Ihrer Organisation ein.
    2.  Für **Metadaten für den Enterprise-Identitätsanbieter bereitgestellt, die mithilfe von**, auf **Datei**.
    3.  Um Ihre heruntergeladene Metadatendatei hochzuladen, klicken Sie auf **Choose File**.
    4.  Klicken Sie auf **Set Identitätsprovider**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei ArcGIS anmelden können, müssen sie in ArcGIS bereitgestellt werden.  
Im Fall von ArcGIS ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **ArcGIS** Mandanten.

2.  Klicken Sie auf **Mitglieder einladen**.

    ![Invite Members](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")

3.  Wählen Sie **Mitglieder automatisch hinzufügen, ohne eine e-Mail senden**, und klicken Sie dann auf **Weiter**.

    ![Add Members Automatically](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")

4.  Auf der **Member** Dialogfeld führen die folgenden Schritte aus:

    ![Add and review](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")

    1.  Geben Sie die **Vorname**, **Nachname** und **E-Mail** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Hinzufügen und überprüfen Sie**.

5.  Überprüfen Sie die Daten, die Sie eingegeben haben, und klicken Sie dann auf **Mitglieder hinzufügen**.

    ![Add member](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[AZURE.NOTE] Können Sie alle anderen ArcGIS Benutzerkonten oder APIs von ArcGIS Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ArcGIS Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** ArcGIS ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



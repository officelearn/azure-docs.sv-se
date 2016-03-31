<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit AirWatch | Microsoft Azure" 
    description="Erfahren Sie, wie Sie AirWatch mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit AirWatch

In diesem Lernprogramm wird die Integration von Azure und AirWatch erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein AirWatch-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Lernprogramms AirWatch zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer AirWatch-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für AirWatch
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Aktivieren der Anwendungsintegration für AirWatch

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für AirWatch aktivieren.

###So aktivieren Sie die Anwendungsintegration für AirWatch

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **AirWatch**.

    ![Anwendungskatalog](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **AirWatch**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei AirWatch zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **AirWatch** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei AirWatch anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **AirWatch URL für Anmeldung** Textfeld verwendete URL durch die Benutzer bei der AirWatch-Anwendung (z. B.: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um AirWatch** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der AirWatch-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im linken Navigationsbereich auf **Konten**, und klicken Sie dann auf **Administratoren**.

    ![Administratoren](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrators")

7.  Erweitern Sie die **Einstellungen** Menü, und klicken Sie dann auf **Directory Services**.

    ![Einstellungen](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Settings")

8.  Klicken Sie auf die **Benutzer** Registerkarte der **Basis-DN** Textfield Geben Sie Ihren Domänennamen ein, und klicken Sie dann auf **Speichern**.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791922.png "User")

9.  Klicken Sie auf die **Server** Registerkarte.

    ![Server](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Führen Sie die folgenden Schritte aus:

    ![Hochladen](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Upload")

    1.  Als **Verzeichnistyp**, Option **keine**.
    2.  Wählen Sie **Use SAML For Authentication**.
    3.  Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Hochladen**.

11. In der **anfordern** führen die folgenden Schritte aus:

    ![Request](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Request")

    1.  Als **Request Binding Type**, Option **POST**.
    2.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um Airwatch** Kopieren der **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Identity Provider Single Sign On URL** Textfeld.
    3.  Als **NameID Format**, Option **e-Mail-Adresse**.
    4.  Klicken Sie auf **Speichern**.

12. Klicken Sie auf die **Benutzer** Registerkarte erneut.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791926.png "User")

13. In der **Attribut** führen die folgenden Schritte aus:

    ![Attribut](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribute")

    1.  In der **Objektbezeichner** Textfeld **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  In der **Benutzername** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  In der **Anzeigenamen** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  In der **Vorname** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  In der **Nachname** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  In der **E-Mail** Textfeld **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Klicken Sie auf **Speichern**.

14. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei AirWatch anmelden können, müssen sie in AirWatch bereitgestellt werden.  
Im Fall von AirWatch ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **AirWatch** -Unternehmenswebsite als Administrator.

2.  Klicken Sie im Navigationsbereich auf der linken Seite auf **Konten**, und klicken Sie dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Users")

3.  In der **Benutzer** Menü klicken Sie auf **Listenansicht**, und klicken Sie dann auf **Hinzufügen \ > Add User**.

    ![Benutzer hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Add User")

4.  Auf der **Add / Edit User** im Dialogfeld die folgenden Schritte ausführen:

    ![Benutzer hinzufügen](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Add User")

    1.  Typ der **Benutzername**, **Kennwort**, **Kennwort bestätigen**, **First Name**, **Nachname**, **e-Mail-Adresse** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

>[AZURE.NOTE] Können Sie alle anderen AirWatch Benutzerkonten oder APIs von AirWatch Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie AirWatch Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** AirWatch ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



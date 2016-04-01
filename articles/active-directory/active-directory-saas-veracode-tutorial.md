<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Veracode | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Veracode mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu aktivieren." 
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

#Tutorial: Azure Active Directory-Integration mit Veracode
  
In diesem Tutorial wird die Integration von Azure und Veracode erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Veracode-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms Veracode zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Veracode
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

##Aktivieren der Anwendungsintegration für Veracode
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Veracode aktivieren.

###So aktivieren Sie die Anwendungsintegration für Veracode

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Veracode**.

    ![Anwendungskatalog](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Veracode**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Veracode zu authentifizieren.  
Die Veracode-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Veracode** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden Veracode** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")

3.  Auf der **App-Einstellungen konfigurieren** auf **Weiter**.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")

4.  Auf der **Veracode einmaliges Anmelden konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Veracode-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Einstellungen**, und klicken Sie dann auf **Admin**.

    ![Verwaltung](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  Klicken Sie auf die **SAML** Registerkarte.

8.  In der **SAML-Einstellungen für Unternehmen** führen die folgenden Schritte aus:

    ![Verwaltung](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  In Azure-Portal auf der **Veracode einmaliges Anmelden konfigurieren** Kopieren der **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Aussteller** Textbox
    2.  Um das heruntergeladene Zertifikat hochzuladen, klicken Sie auf **Choose File**.
    3.  Wählen Sie **Aktivieren Sie die automatische Registrierung**.

9.  In der **selbst überschneiden** im Abschnitt führen die folgenden Schritte aus, und klicken Sie dann auf **Speichern**:

    ![Verwaltung](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  Als **neue Benutzer Aktivierung**, Option **keine Aktivierung erforderlich**.
    2.  Als **Benutzer Datenupdates**, auf **Veracode Benutzer zu**.
    3.  Für **SAML Textattributdetails**, wählen Sie die folgenden:
        -   **Benutzerrollen**
        -   **Richtlinien-Administrator**
        -   **Prüfer**
        -   **Leiter der Sicherheitsabteilung**
        -   **Geschäftsleitung**
        -   **Absender**
        -   **Creator (Ersteller)**
        -   **Alle Scantypen**
        -   **Teammitgliedschaften**
        -   **Standardteam**

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")

11. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")

12. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

  	| Attributname | Attributwert |
  	|:---------------|:----------------|
  	| firstname      | User.givenname  |
  	| lastname       | User.surname    |
  	| email          | User.mail       |

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.

    3.  In der **Attributwert** Textfeld, wählen Sie für diese Zeile angezeigten Wert des Attributs.

    4.  Klicken Sie auf **vollständige**.

13. Klicken Sie auf **Ändern**.

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Veracode anmelden können, müssen sie in Veracode bereitgestellt werden.  
Im Fall von Veracode ist die Bereitstellung eine automatisierte Aufgabe.  
Für Sie steht kein Aktionselement zur Verfügung.
  
Benutzer werden beim Versuch des einmaligen Anmeldens bei Bedarf automatisch erstellt.

>[AZURE.NOTE] Können Sie alle anderen Veracode Benutzerkonten oder APIs von Veracode Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Veracode Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Veracode ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


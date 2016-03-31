<properties 
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit Clever | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Clever mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Lernprogramm: Azure Active Directory-Integration mit Clever

In diesem Lernprogramm wird die Integration von Azure und Clever erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Clever-Mandanten

Nach Abschluss dieses Lernprogramms Clever zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer raffinierte-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Clever
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")
##Aktivieren der Anwendungsintegration für Clever

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Clever aktivieren.

###So aktivieren Sie die Anwendungsintegration für Clever

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Clever**.

    ![Anwendungskatalog](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Clever**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Clever zu authentifizieren.  
Die intelligente Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration.  
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Clever** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der ** einmaliges Anmelden ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden Clever** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **raffinierte Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei der intelligente Anwendung (z. B.: *https://clever.com/in/azsandbox*), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")

4.  Auf der **Clever einmaliges Anmelden konfigurieren** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Clever-Unternehmenswebsite als Administrator an.

6.  Klicken Sie in der Symbolleiste auf **Instant Anmeldung**.

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")

7.  Auf der **Instant Anmeldung** führen die folgenden Schritte aus:

    ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")

    1.  Typ der **Anmelde-URL**.  

        >[AZURE.NOTE] Die **-Anmelde-URL** ist ein benutzerdefinierter Wert.
         Sie erhalten diesen Wert vom Clever-Supportteam.

    2.  Als **Identitätssystem**, Option **ADFS**.
    3.  Klicken Sie auf **Speichern**.

8.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")

9.  Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld.

    ![Attribute](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")

10. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![SAML-Tokenattribute](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")

  	|Attributname|Attributwert|
  	|---|---|
  	|clever.Student.Credentials.district\_username|User.userprincipalname|

    1.  Für jede Datenzeile in der obigen Tabelle, klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  In der **Attributnamen** Textfeld Geben Sie die für diese Zeile angezeigten Attributnamen ein.
    3.  In der **Attributwert** Textfeld, wählen Sie für diese Zeile angezeigten Wert des Attributs.
    4.  Klicken Sie auf **vollständige**.

11. Klicken Sie auf **Ändern**.

##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Clever anmelden können, müssen sie in Clever bereitgestellt werden.  
Im Fall von Clever ist die Bereitstellung eine manuelle Aufgabe, die von Ihrem Clever-Support-Team ausgeführt werden muss.

>[AZURE.NOTE] Können Sie alle anderen intelligente Erstellung von Benutzerkonten oder APIs von Clever Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Clever Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Clever ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



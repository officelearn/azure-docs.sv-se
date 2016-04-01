<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit IdeaScale | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie IdeaScale mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit IdeaScale
  
In diesem Tutorial wird die Integration von Azure und IdeaScale erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein IdeaScale-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms IdeaScale zugewiesene Azure AD-Benutzer werden in der Anwendung mit einmaligem Anmelden die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für IdeaScale
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")
##Aktivieren der Anwendungsintegration für IdeaScale
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für IdeaScale aktivieren.

###So aktivieren Sie die Anwendungsintegration für IdeaScale

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **IdeaScale**.

    ![Anwendungskatalog](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **IdeaScale**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei IdeaScale zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für IdeaScale müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **IdeaScale** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")

2.  Auf der **Wie möchten Sie Benutzer zur Anmeldung bei IdeaScale** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **IdeaScale-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer Anwendung IdeaScale anmelden (z. B.: "*https://company.IdeaScale.com*"), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configure App URL")

4.  Auf der **einmaliges Anmelden konfigurieren, um IdeaScale** zum Herunterladen Ihrer Metadaten klicken **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der IdeaScale-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Communityeinstellungen**.

    ![Communityeinstellungen](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")

7.  Wechseln Sie zu **Sicherheit \ > Einstellungen für einmaliges Anmelden einmaliges**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")

8.  Als **einmaligen Typ**, Option **SAML 2.0**.

    ![SSO-Typ](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")

9.  Auf der **Seite Einstellungen für einmaliges** im Dialogfeld die folgenden Schritte ausführen:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")

    1.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um IdeaScale** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **SAML IdP-Entitäts-ID** Textfeld.
    2.  Kopieren Sie den Inhalt der heruntergeladenen Metadatendatei, und fügen Sie ihn in die **SAML IdP-Metadaten** Textfeld.
    3.  In Azure-Portal auf der **einmaliges Anmelden konfigurieren, um IdeaScale** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Erfolg-Abmelde-URL** Textfeld.
    4.  Klicken Sie auf **Speichern**.

10. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei IdeaScale anmelden können, müssen sie in IdeaScale bereitgestellt werden.  
Im Fall von IdeaScale ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei Ihrem **IdeaScale** -Unternehmenswebsite als Administrator.

2.  Wechseln Sie zu **Communityeinstellungen**.

    ![Communityeinstellungen](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")

3.  Wechseln Sie zu **Grundeinstellungen \ > Mitgliederverwaltung**.

4.  Klicken Sie auf **Mitglied hinzufügen**.

    ![Mitgliederverwaltung](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")

5.  Führen Sie im Abschnitt „Neues Mitglied hinzufügen“ die folgenden Schritte aus:

    ![Neues Mitglied hinzufügen](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")

    1.  In der **e-Mail-Adressen** Textfeld Geben Sie die e-Mail-Adresse eines gültigen AAD-Kontos, das Sie bereitstellen möchten.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE] Die Azure Active Directory-Kontoinhaber erhält eine e-Mail mit einem Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen IdeaScale Benutzerkonten oder APIs von IdeaScale Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie IdeaScale Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** IdeaScale ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


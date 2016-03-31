<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SuccessFactors | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie SuccessFactors mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit SuccessFactors
  
Dieses Lernprogramm ist die Integration von Azure und SuccessFactors in **SP-initiierte SSO-Modus**.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein für SuccessFactor SSO (einmaliges Anmelden) aktiviertes Abonnement im  SP-initiierten Modus
  
Nach Abschluss dieses Lernprogramms SuccessFactors zugewiesene Azure AD-Benutzer werden mit der Anwendung auf Ihrer SuccessFactors-Unternehmenswebsite (vom Dienstanbieter initiierte Anmeldung) anmelden oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SuccessFactors
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Scenario")

##Aktivieren der Anwendungsintegration für SuccessFactors
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SuccessFactors aktivieren.

###So aktivieren Sie die Anwendungsintegration für SuccessFactors:

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **SuccessFactors**.

    ![Anwendungskatalog](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Appliation Gallery")

7.  Wählen Sie im Ergebnisbereich **SuccessFactors**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SuccessFactors zu authentifizieren.
  
Wenn einmaliges Anmelden konfiguriert werden soll, müssen Sie sich an das SuccessFactors-Supportteam wenden.

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **SuccessFactors** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer bei SuccessFactors anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configure App URL")

    1.  In der **SuccessFactors-Anmelde-URL** Textfeld verwendete URL von den Benutzern zur Anmeldung bei Ihrem SuccessFactors-Anwendung (z. B.: "*https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*").
    2.  In der **SuccessFactors-Antwort-URL** Textfeld **https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**.

        >[AZURE.NOTE] Dieser Wert ist nur ein vorübergehender Platzhalter.  
        >Sie erhalten den tatsächlichen Wert von Ihrem SuccessFactors Supportteam.  
        >Später in diesem Lernprogramm werden Sie Anweisungen für die Kontaktaufnahme mit Ihrem SuccessFactors-Supportteam erhalten.  
        >Im Rahmen dieser Konversation erhalten Sie Ihre tatsächliche SuccessFactors-Antwort-URL.

4.  Auf der **einmaliges Anmelden bei SuccessFactors konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configure Single Sign-On")

5.  Um das SAML-basierte einmalige Anmelden (SSO) zu konfigurieren, wenden Sie sich an das  SuccessFactors-Supportteam. Stellen Sie diesem die folgenden Elemente bereit:

    1.  Das heruntergeladene Zertifikat
    2.  Die Remoteanmelde-URL
    3.  Die Remoteabmelde-URL

    >[AZURE.IMPORTANT] Wenden Sie sich an Ihr SuccessFactors-Supportteam zum Festlegen des Parameters NameId Format "*Unspecified*".

    Ihr Successfactors-Supportteam sendet Ihnen die richtige **Successfactors-Antwort-URL** benötigen Sie für die **App-URL konfigurieren** Dialogfeld.

6.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configure Single Sign-On")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei SuccessFactors anmelden können, müssen sie in SuccessFactors bereitgestellt werden.  
Im Fall von SuccessFactors ist die Bereitstellung eine manuelle Aufgabe.
  
Um in SuccessFactors erstellte Benutzer abzurufen, müssen Sie sich an das SuccessFactors-Supportteam wenden.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SuccessFactors Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** SuccessFactors ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


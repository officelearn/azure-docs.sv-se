<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Freshdesk | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Freshdesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Freshdesk
  
In diesem Tutorial wird die Integration von Azure und Freshdesk erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Freshdesk-Mandanten
  
Nach Abschluss dieses Lernprogramms Freshdesk zugewiesene Azure AD-Benutzer werden mit einmaligem Anmelden bei der Anwendung auf Ihrer Freshdesk-Unternehmenssite (vom Dienstanbieter initiierte Anmeldung) oder über die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Freshdesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")
##Aktivieren der Anwendungsintegration für Freshdesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Freshdesk aktivieren.

###So aktivieren Sie die Anwendungsintegration für Freshdesk

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Add application")

5.  Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Add an application from gallerry")

6.  In der **Suchfeld**, Typ **Freshdesk**.

    ![Anwendungskatalog](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Application gallery")

7.  Wählen Sie im Ergebnisbereich **Freshdesk**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Freshdesk zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Freshdesk müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI).

###So konfigurieren Sie einmaliges Anmelden

1.  In Azure AD-Portal auf der **Freshdesk** anwendungsintegrationsseite klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen der ** einmaliges Anmelden konfigurieren ** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configure Single Sign-On")

2.  Auf der **wie sollen sich Benutzer anmelden bei Freshdesk** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configure Single Sign-On")

3.  Auf der **App-URL konfigurieren** auf der Seite der **Freshdesk-Anmelde-URL** Textfeld Ihre URL nach dem Muster "*https://\<tenant-name\>. Freshdesk.com*", und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configure App URL")

4.  Auf der **einmaliges Anmelden bei Freshdesk konfigurieren** Seite, um Ihr Zertifikat herunterzuladen, klicken Sie **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Freshdesk.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configure Single Sign-On")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Freshdesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

7.  In den **Allgemeine Einstellungen** auf **Security**.

    ![Sicherheit](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")

8.  In der **Sicherheit** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")

    1.  Für **einmaliges Anmelden (SSO)**, Option **auf**.
    2.  Wählen Sie **SAML SSO-**.
    3.  In Azure-Portal auf der **einmaliges Anmelden bei Freshdesk konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **SAML-Anmelde-URL** Textfeld.
    4.  In Azure-Portal auf der **einmaliges Anmelden bei Freshdesk konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Abmelde-URL** Textfeld.
    5.  Kopieren der **Fingerabdruck** -Wert aus dem exportierten Zertifikat, und fügen Sie ihn in die **Sicherheitszertifikats** Textfeld.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [wie Fingerabdruckwert des Zertifikats abrufen](http://youtu.be/YKQF266SAxI)

    6.  Klicken Sie auf **Speichern**.

9.  Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configure Single Sign-On")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Freshdesk anmelden können, müssen sie in Freshdesk bereitgestellt werden.  
Im Fall von Freshdesk ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem **Freshdesk** Mandanten.

2.  Klicken Sie im Menü am oberen Rand **Admin**.

    ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3.  In den **Allgemeine Einstellungen** auf **Agents**.

    ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Klicken Sie auf **neuer Agent**.

    ![Neuer Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5.  Führen Sie im Dialogfeld „Agent-Informationen“ die folgenden Schritte aus:

    ![Agent-Informationen](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")

    1.  In der **Vollständiger Name** Textfeld Geben Sie den Namen des Azure AD-Kontos, das Sie bereitstellen möchten.
    2.  In der **E-Mail** Textfeld Typ des Azure AD e-Mail-Adresse des Azure AD-Kontos, das Sie bereitstellen möchten.
    3.  In der **Titel** Textfeld Geben Sie den Titel des Azure AD-Kontos, das Sie bereitstellen möchten.
    4.  Wählen Sie **Agents Rolle**, und klicken Sie dann auf **Zuweisen**.
    5.  Klicken Sie auf **Speichern**.
    
        >[AZURE.NOTE] Der Azure AD-Kontoinhaber erhält eine e-Mail, die einen Link zum Bestätigen des Kontos, bevor es aktiviert wird.

>[AZURE.NOTE] Können Sie alle anderen Freshdesk Benutzerkonten oder APIs von Freshdesk Bereitstellung AAD-Benutzerkonten.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Freshdesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Auf der ** Freshdesk ** anwendungsintegrationsseite, klicken Sie auf **Zuweisen von Benutzern**.

    ![Benutzer zuweisen](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assign Users")

3.  Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


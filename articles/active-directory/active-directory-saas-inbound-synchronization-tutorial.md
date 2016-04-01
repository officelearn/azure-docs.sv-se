<properties 
    pageTitle="Tutorial: Konfigurieren von Workday für eingehende Synchronisierung | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Workday mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Konfigurieren von Workday für eingehende Synchronisierung
>[AZURE.NOTE]Azure Active Directory (AD) Premium steht Kunden in China über die internationale Instanz von Azure AD.    
Azure AD Premium wird derzeit im von 21Vianet in China betriebenen Microsoft Azure-Dienst nicht unterstützt.    

In diesem Tutorial werden die Schritte erläutert, die Sie in Workday und Microsoft Azure AD ausführen müssen, um Personen aus Workday in Microsoft Azure AD zu importieren.    
 Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:  

-   Ein gültiges Azure-Abonnement  
-   Einen Mandanten in Workday  

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:  

1.  Aktivieren der Anwendungsintegration für Workday  
2.  Erstellen eines Integrationssystembenutzers  
3.  Erstellen einer Sicherheitsgruppe  
4.  Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe  
5.  Konfigurieren von Sicherheitsgruppenoptionen  
6.  Aktivieren von Sicherheitsrichtlinienänderungen  
7.  Konfigurieren des Benutzerimports in Microsoft Azure AD  

##Aktivieren der Anwendungsintegration für Workday

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.    

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.    

    ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.    

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.    

    ![Anwendungen](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")  

4.  Zum Öffnen der **Anwendungskatalog**, klicken Sie auf **App hinzufügen**, und klicken Sie dann auf **Hinzufügen einer Anwendung für meine Organisation**.    

    ![Was möchten Sie tun?](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "What do you want to do?")  

5.  In der **Suchfeld**, Typ **Workday**.    

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  

6.  Wählen Sie im Ergebnisbereich **Workday**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.    

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

##Erstellen eines Integrationssystembenutzers

1.  In der **Workday-Workbench**, geben Sie **erstellen Benutzer** in das Suchfeld ein, und klicken Sie dann auf den Link **Integrationssystembenutzer erstellen**.     

    ![Benutzer erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "create user")  

2.  Führen Sie die Aufgabe „Integrationssystembenutzer erstellen“ aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben.  Lassen Sie das Kontrollkästchen „Bei der nächsten Anmeldung neues Kennwort anfordern“ deaktiviert. Dieser Benutzer meldet sich programmgesteuert an.    
    Übernehmen Sie für „Sitzungstimeout in Minuten“ den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden.    

    ![Integrationssystembenutzer erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Create Integration System User")  

##Erstellen einer Sicherheitsgruppe

Für das in diesem Tutorial beschriebene Szenario müssen Sie eine uneingeschränkte Integrationssystem-Sicherheitsgruppe erstellen und den Benutzer dieser Gruppe zuweisen.    

1.  Geben Sie „Sicherheitsgruppe erstellen“ in das Suchfeld ein, und klicken Sie dann auf den Link „Sicherheitsgruppe erstellen“.     

    ![Sicherheitsgruppe erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity Group")  

2.  Führen Sie die Aufgabe „Sicherheitsgruppe erstellen“ aus.  Wählen Sie in der Dropdownliste „Typ der Mandantensicherheitsgruppe“ die Option „Integrationssystem-Sicherheitsgruppe – uneingeschränkt“ aus, um eine Sicherheitsgruppe zu erstellen, der Mitglieder explizit hinzugefügt werden.     

    ![Sicherheitsgruppe erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity Group")  

##Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe

1.  Bearbeiten einer Sicherheitsgruppe in das Suchfeld eingeben, und klicken Sie dann auf den Link  **Sicherheitsgruppe bearbeiten**.     

    ![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Edit Security Group")  

2.  Suchen Sie anhand des Namens nach der neuen Integrationssicherheitsgruppe, und wählen Sie sie aus.    

    ![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Edit Security Group")  

3.  Fügen Sie den neuen Integrationssystembenutzer der neuen Sicherheitsgruppe hinzu.       

    ![Systemsicherheitsgruppe](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System Security Group")  

##Konfigurieren von Sicherheitsgruppenoptionen

In diesem Schritt gewähren Sie der neuen Sicherheitsgruppe Berechtigungen für Get- und Put-Vorgänge für die durch die folgenden Domänensicherheitsrichtlinien geschützten Objekte:  

-   Externe Kontobereitstellung  
-   Mitarbeiterdaten: öffentliche Mitarbeiterberichte  
-   Mitarbeiterdaten: alle Positionen  
-   Mitarbeiterdaten: aktuelle Personalinformationen  
-   Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil  

&nbsp;  

1.  Geben Sie „Domänensicherheitsrichtlinien“ in das Suchfeld ein, und klicken Sie dann auf den Link „Domänensicherheitsrichtlinien für Funktionsbereich“.     

    ![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domain Security Policies")  

2.  Suchen Sie nach „System“, und wählen Sie den Funktionsbereich „System“ aus.  Klicken Sie auf die Schaltfläche „OK“.     

    ![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domain Security Policies")  

3.  Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „System“ den Eintrag „Sicherheitsverwaltung“, und wählen Sie die Domänensicherheitsrichtlinie „Externe Kontobereitstellung“ aus.     

    ![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domain Security Policies")  

4.  Klicken Sie auf die Schaltfläche „Berechtigungen bearbeiten“, und fügen Sie die neue Sicherheitsgruppe auf dem Bildschirm „Berechtigungen bearbeiten“ zur Liste der Sicherheitsgruppen mit Berechtigungen für die Get- und Put-Integration hinzu.     

    ![Berechtigung bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Edit Permission")  

5.  Wiederholen Sie Schritt 1 oben, um zum Bildschirm für die Auswahl der Funktionsbereiche zurückzukehren. Suchen Sie nach „Personal“, wählen Sie den Funktionsbereich „Personal“ aus, und klicken Sie auf die Schaltfläche „OK“.    

    ![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domain Security Policies")  

6.  Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „Personal“ den Eintrag „Mitarbeiterdaten: Personal“, und wiederholen Sie Schritt 4 für die folgenden Sicherheitsrichtlinien:    

    -   Mitarbeiterdaten: öffentliche Mitarbeiterberichte  
    -   Mitarbeiterdaten: alle Positionen  
    -   Mitarbeiterdaten: aktuelle Personalinformationen  
    -   Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil    

    ![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domain Security Policies")  

##Aktivieren von Sicherheitsrichtlinienänderungen

1.  Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“.    

    ![Aktivieren](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activate")  

2.   Geben Sie zum Ausführen der Aufgabe „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“ zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche „OK“.      

    ![Activate Pending Security](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activate Pending Security")  

3.  Führen Sie die Aufgabe auf dem nächsten Bildschirm durch, indem Sie das Kontrollkästchen „Bestätigen“ aktivieren und auf „OK“ klicken.     

    ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activate Pending Security")  

##Konfigurieren des Benutzerimports in Microsoft Azure AD

In diesem Abschnitt wird beschrieben, wie Sie Microsoft Azure AD zum Importieren von Personen aus Workday konfigurieren.    

###So konfigurieren Sie den Benutzerimport in Microsoft Azure AD

1.  Auf der **Workday** AHA klicken Sie auf **Benutzerimport konfigurieren** zum Öffnen der **Konfigurieren der Bereitstellung** Dialogfeld.    

2.  Auf der **Einstellungen und Administratoranmeldeinformationen** Seite führen die folgenden Schritte aus, und klicken Sie dann auf Weiter:    

    ![Einstellungen und Administrator-Anmeldeinformationen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Settings and admin credentials")    

    1.  In der **Administratorbenutzername für Workday** Textfeld Geben Sie den Namen des Benutzers, die Sie, in erstellt haben der [Erstellen eines integrationssystembenutzers](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) Abschnitt.    
    2.  In der **Workday-Administratorkennwort** Textfeld Geben Sie das Kennwort des Benutzers, die Sie, in erstellt haben der [Erstellen eines integrationssystembenutzers](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) Abschnitt.    
    3.  In der **Workday-Mandanten-URL** Textfeld Geben Sie die URL oder Ihres Workday-Mandanten.    

3.  Auf der **Verbindungstest** auf **Test starten** Konnektivität zu bestätigen, und klicken Sie dann auf **Weiter**.    

    ![Verbindung testen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Test connection")  

4.  Auf der **Bereitstellungsoptionen** auf **Weiter**.    

    ![Bereitstellungsoptionen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Provisioning options")  

5.  Auf der **mit der Bereitstellung beginnen** Dialogfeld klicken Sie auf **vollständig**.    

    ![Bereitstellung beginnen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Start provisioning")  

Sie können nun die **Benutzer** hinzu, und überprüfen Sie, ob der Workday-Benutzer importiert wurde.    



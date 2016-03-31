<properties 
    pageTitle="Tutorial: Konfigurieren von Workday für eingehende Synchronisierung | Microsoft Azure" 
    description="Erfahren Sie, wie Workday als Quelle von Identitätsdaten für Azure Active Directory verwendet wird." 
    services="active-directory" 
    authors="MarkusVi"  
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


In diesem Tutorial werden die Schritte erläutert, die Sie in Workday und Azure AD ausführen müssen, um Personen aus Workday in Azure AD zu importieren. 

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure AD Premium-Abonnement
-   Einen Mandanten in Workday
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Workday 


2. Erstellen eines Integrationssystembenutzers 


3. Erstellen einer Sicherheitsgruppe 


4. Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe 


5. Konfigurieren von Sicherheitsgruppenoptionen 


6. Aktivieren von Sicherheitsrichtlinienänderungen 


7. Konfigurieren des Benutzerimports in Azure AD 



##Aktivieren der Anwendungsintegration für Workday
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")

4.  Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.

    ![Anwendung hinzufügen](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Add application")

  
5. Geben Sie in das Suchfeld **Workday**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Add an application from gallerry")

6. Wählen Sie im Ergebnisbereich "Workday" aus, und klicken Sie dann auf "Abschließen", um die Anwendung hinzuzufügen.

    ![Anwendungskatalog](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Application gallery")





## Erstellen eines Integrationssystembenutzers

1. In der **Workday-Workbench**, geben Sie Benutzer in das Suchfeld erstellen, und klicken Sie dann auf **Integrationssystembenutzer erstellen**. <br><br>   ![Benutzer erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Create user")



2. Führen Sie die **Integrationssystembenutzer erstellen** Aufgabe durch einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer bereitstellen.  Lassen Sie das Kontrollkästchen „Bei der nächsten Anmeldung neues Kennwort anfordern“ deaktiviert. Dieser Benutzer meldet sich programmgesteuert an. <br>
Übernehmen Sie für „Sitzungstimeout in Minuten“ den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden. <br><br>   ![Integrationssystembenutzer erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Create Integration System User")
 




## Erstellen einer Sicherheitsgruppe

Für das in diesem Tutorial beschriebene Szenario müssen Sie eine uneingeschränkte Integrationssystem-Sicherheitsgruppe erstellen und den Benutzer dieser Gruppe zuweisen.


1. Geben Sie Sicherheitsgruppe erstellen, in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe erstellen**. <br><br>   ![Gruppe "CreateSecurity"](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity Group")
 

2. Führen Sie die Aufgabe „Sicherheitsgruppe erstellen“ aus.  Wählen Sie in der Dropdownliste „Typ der Mandantensicherheitsgruppe“ die Option „Integrationssystem-Sicherheitsgruppe – uneingeschränkt“ aus, um eine Sicherheitsgruppe zu erstellen, der Mitglieder explizit hinzugefügt werden. <br><br>   ![Sicherheitsgruppe erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity Group")
 



## Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe

1. Bearbeiten einer Sicherheitsgruppe in das Suchfeld eingeben, und klicken Sie dann auf **Sicherheitsgruppe bearbeiten**. <br><br>   ![Bearbeiten einer Sicherheitsgruppe](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edit Security Group")
 
 

2. Suchen Sie anhand des Namens nach der neuen Integrationssicherheitsgruppe, und wählen Sie sie aus. <br><br> ![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Edit Security Group")
Sicherheitsgruppe bearbeiten 

3. Fügen Sie den neuen Integrationssystembenutzer der neuen Sicherheitsgruppe hinzu. <br><br>   ![Systemsicherheitsgruppe](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System Security Group")  




## Konfigurieren von Sicherheitsgruppenoptionen

In diesem Schritt gewähren Sie der neuen Sicherheitsgruppe Berechtigungen für **abrufen** und **setzen** Vorgänge für die Objekte, die durch die folgenden Domänensicherheitsrichtlinien gesichert:

- Externe Kontobereitstellung

- Mitarbeiterdaten: öffentliche Mitarbeiterberichte

- Mitarbeiterdaten: alle Positionen

- Mitarbeiterdaten: aktuelle Personalinformationen

- Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil
 
   

1. Geben Sie „Domänensicherheitsrichtlinien“ in das Suchfeld ein, und klicken Sie dann auf den Link „Domänensicherheitsrichtlinien für Funktionsbereich“.  <br><br>   ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domain Security Policies")  
 

2. Suchen Sie nach System und wählen Sie die **System** Funktionsbereich.  Klicken Sie auf **OK**.  <br><br>   ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domain Security Policies")  


3. Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „System“ den Eintrag „Sicherheitsverwaltung“, und wählen Sie die Domänensicherheitsrichtlinie „Externe Kontobereitstellung“ aus.  <br><br>   ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domain Security Policies")  


4. Klicken Sie auf **Berechtigungen bearbeiten**, und klicken Sie dann auf die **Berechtigungen bearbeiten**Seite fügen Sie die neuen Sicherheitsgruppe zur Liste der Sicherheitsgruppen mit **abrufen** und **setzen** integrationsberechtigungen. <br><br>   ![Berechtigung bearbeiten](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Edit Permission")  

 

5. Wiederholen Sie Schritt 1 oben auf dem Bildschirm für die Auswahl der Funktionsbereiche, zurückgeben und dieses Mal Suche für Personal, wählen Sie den Funktionsbereich aus, und klicken Sie auf **OK**.<br><br>   ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domain Security Policies")  
 

6. Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „Personal“ den Eintrag „Mitarbeiterdaten: Personal“, und wiederholen Sie Schritt 4 für die folgenden Sicherheitsrichtlinien:

     - Mitarbeiterdaten: öffentliche Mitarbeiterberichte

     - Mitarbeiterdaten: alle Positionen

     - Mitarbeiterdaten: aktuelle Personalinformationen

     - Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil


<br><br>   ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domain Security Policies")  







## Aktivieren von Sicherheitsrichtlinienänderungen


1. Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“. <br><br>   ![Aktivieren](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activate") 
 

2. Zu Beginn der ausstehenden Sicherheitsrichtlinienänderungen aktivieren Sie einen Kommentar für Überwachungszwecke eingeben, und klicken Sie dann auf **OK**. <br><br>   ![Aktivieren von ausstehenden Sicherheitszuordnungen](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activate Pending Security")   
 

3. Führen Sie die Aufgabe auf dem nächsten Bildschirm durch Aktivieren des Kontrollkästchens namens bestätigen, und klicken Sie dann auf **OK**. <br><br>   ![Aktivieren von ausstehenden Sicherheitszuordnungen](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activate Pending Security")  





## Konfigurieren des Benutzerimports in Azure AD

In diesem Abschnitt wird beschrieben, wie Sie Azure AD zum Importieren von Personen aus Workday konfigurieren.


### So konfigurieren Sie den Benutzerimport in Azure AD


1. Auf der **Workday** AHA klicken Sie auf **Benutzerimport konfigurieren** zum Öffnen der **Konfigurieren der Bereitstellung** Dialogfeld.


2. auf der **Einstellungen und Administratoranmeldeinformationen** Seite führen die folgenden Schritte aus, und klicken Sie dann auf **Weiter**: <br><br>   ![Einstellungen und Administratoranmeldeinformationen](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Settings and admin credentials")  
 
     2.1. In the Workday admin user name textbox, type the name of the user you have created in the Creating an integration system user section.

     2.2. In the Workday admin password textbox, type the password of the user you have created in the Creating an integration system user section.

     2.3. In the Workday tenant URL textbox, type the URL or your Workday tenant.


3. Auf der **Verbindungstest** auf **Test starten** Konnektivität zu bestätigen, und klicken Sie dann auf **Weiter**. <br><br>   ![Testverbindung](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test connection")  
 

4. Auf der **Provisioning** Seite "Optionen", klicken Sie auf **Weiter**. <br><br>   ![Bereitstellungsoptionen](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Provisioning options")


5. Auf der **mit der Bereitstellung beginnen** Dialogfeld klicken Sie auf **vollständig**. <br><br>   ![Starten der Bereitstellung](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Start provisioning")
 

Sie können nun die **Benutzer** hinzu, und überprüfen Sie, ob der Workday-Benutzer importiert wurde.



## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



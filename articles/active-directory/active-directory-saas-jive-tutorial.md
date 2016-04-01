<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Jive | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Jive mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit Jive

  
In diesem Tutorial wird die Integration von Azure und Jive erläutert.  
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Jive-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jive
2.  Konfigurieren der Benutzerbereitstellung

##Aktivieren der Anwendungsintegration für Jive
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jive aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jive

1.  Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.

    ![Anwendungen](./media/active-directory-saas-jive-tutorial/IC700994.png "Applications")

4.  Zum Öffnen der **Anwendungskatalog**, klicken Sie auf **App hinzufügen**, und klicken Sie dann auf **Hinzufügen einer Anwendung für meine Organisation**.

    ![Was möchten Sie tun?](./media/active-directory-saas-jive-tutorial/IC700995.png "What do you want to do?")

5.  In der **Suchfeld**, Typ **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  Wählen Sie im Ergebnisbereich **Jive**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Jive aktivieren.  
Im Rahmen dieses Verfahrens werden Sie zur Angabe eines Benutzersicherheitstokens aufgefordert, das Sie von „Jive.com“ anfordern müssen.
  
Der folgende Screenshot zeigt ein Beispiel für das entsprechende Dialogfeld in Azure AD:

![Benutzerbereitstellung konfigurieren](./media/active-directory-saas-jive-tutorial/IC698794.png "Configure User Provisioning")

###So konfigurieren Sie die Benutzerbereitstellung

1.  In der Azure-Verwaltungsportal auf die **Jive** AHA klicken Sie auf **Benutzerbereitstellung** zum Öffnen der **Benutzerbereitstellung konfigurieren** Dialogfeld.

2.  Auf der **Jive-Anmeldeinformationen zum Aktivieren automatischer benutzerbereitstellung** Seite, geben Sie die folgenden Konfigurationseinstellungen:

    1.  In der **Jive-Administratorbenutzername** ein Jive-Kontonamen ein, geben die **Systemadministrator** Profil in Jive.com zugewiesen.

    2.  In der **Jive Administratorkennwort** Textfeld Geben Sie das Kennwort für dieses Konto.

    3.  In der **Jive-Mandanten-URL** Textfeld Geben Sie die URL des Jive-Mandanten.

        >[AZURE.NOTE] Die Jive-Mandanten-URL ist die URL, die von Ihrer Organisation zum Anmelden bei Jive verwendet wird.  
        In der Regel die URL weist das folgende Format: **www.\<organization\>.jive.com**.

    4.  Klicken Sie auf **überprüfen** Ihrer Konfiguration.

    5.  Klicken Sie auf die **Weiter** Schaltfläche, um die **Bestätigung** Seite.

3.  Auf der **Bestätigung** Seite, klicken Sie auf das Häkchen, um die Konfiguration zu speichern.
  
Sie können nun ein Testkonto erstellen und nach 10 Minuten überprüfen, ob das Konto mit „Jive.com“ synchronisiert wurde.




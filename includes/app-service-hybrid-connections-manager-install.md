
1. In der **hybridverbindungen** Blatt, klicken Sie auf die hybridverbindung, die Sie gerade erstellt haben, klicken Sie dann auf **Listenereinrichtung**.
    
    ![Listener-Einrichtung](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. Die **hybridverbindungseigenschaften** Blatt wird geöffnet. Unter **lokalen Hybrid Connection Manager**, wählen Sie **herunterladen und manuell konfigurieren**, speichern Sie das heruntergeladene HybridConnectionManager.msi-Paket und kopieren Sie die Gateway-Verbindungszeichenfolge.
    
    ![Zum Installieren hier klicken](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. Geben Sie an der Administratoreingabeaufforderung den folgenden Befehl ein, um das Installationsprogramm zu starten:

        start HybridConnectionManager.msi
 
7. Nachdem das Installationsprogramm ausgeführt wird, klicken Sie auf **nicht jetzt**, navigieren Sie zum Ordner %ProgramFiles%\Microsoft\HybridConnectionManager, HCMConfigWizard.exe ausgeführt, und klicken Sie auf **Ja** in den **User Account Control** Dialogfeld.
        
7. Fügen Sie die hybridverbindungszeichenfolge ein, die Sie zuvor kopiert haben, und klicken Sie auf **OK**. 
    
    ![Installation](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**.
    
    ![Klicken Sie auf "Schließen"](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    Auf der **hybridverbindungen** Blatt die **Status** Spalte zeigt jetzt **verbunden**. 
    
    ![Status Verbunden](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)


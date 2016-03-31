<!--author=SharS last changed: 11/16/15-->

#### So installieren Sie Update 1.2 über Windows PowerShell für StorSimple

1. Führen Sie die folgenden Schritte aus, um das Softwareupdate herunterzuladen.

    1. Starten Sie Internet Explorer, und navigieren Sie zu [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Wenn Sie Erstbenutzer sind, werden Sie aufgefordert, den Microsoft Update-Katalog zu installieren. Klicken Sie auf **Installieren**.
    
        ![Katalog installieren](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Ein Katalogsuchbildschirm wird angezeigt. Geben Sie **3063418** in das Suchfeld ein, und klicken Sie auf **Suche**.

        ![Katalog durchsuchen](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Sehen Sie die **StorSimple Update 1.2 Anwendungsupdate** Bundle. Klicken Sie auf **Hinzufügen**. Das Update wird dem Warenkorb hinzugefügt. 

        ![Updatepaket](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png) 

    5. Klicken Sie auf **Warenkorb anzeigen**.
 
        ![Warenkorb anzeigen](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png) 

    6. Klicken Sie auf **herunterladen**. Geben oder **Durchsuchen** in ein lokales Verzeichnis, in dem den Download angezeigt werden soll. Das Update wird heruntergeladen werden, einem **StorSimple Update 1.2 Anwendungsupdate** (KB3063418) Ordner am ausgewählten Speicherort. Der Ordner kann auch in eine Netzwerkfreigabe kopiert werden, auf die vom Gerät aus zugegriffen werden kann. 
    
    Dieses Verfahren beschreibt, wie Softwaregeräteupdates als Hotfixes, Firmwareupdates für Datenträger vom Microsoft Update-Server und LSI-Treiber und Windows-Updates aus dem klassischen Azure-Portal installiert werden. Sie können jedoch auswählen, Firmwareupdates für Software, Treiber und Datenträger als Hotfixes zu installieren. Sie müssen dann das StorSimple 1.2 SAS Controller Update (KB3043005) und das StorSimple 1.2 Disk Firmware Update (KB3063416) herunterladen, und in denselben gemeinsamen Ordner kopieren. Um Festplatten-Firmware-Updates als Hotfix zu installieren, führen Sie die Schritte [Installieren von Wartungsmodus-Hotfixes über Windows PowerShell für StorSimple](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple).
    
    > [AZURE.NOTE] Der Hotfix muss über beide Controller zum Erkennen von möglichen Fehlermeldungen vom peercontroller zugegriffen werden. 
            
2. Greifen Sie zum Installieren des Softwareupdates über die serielle Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Befolgen Sie die detaillierte Anleitung im [Verwenden von PuTTy für die Verbindung mit der seriellen Konsole](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

3. Drücken Sie an der Befehlszeile **EINGABETASTE**.

4. Wählen Sie **Option 1** melden Sie sich bei dem Gerät mit Vollzugriff.

5. Geben Sie zum Installieren des Updatepakets Folgendes an der Eingabeaufforderung ein:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Verwenden Sie die IP-Adresse anstelle von DNS im Freigabepfad des obigen Befehls. Der Anmeldeinformationsparameter wird nur verwendet, wenn Sie auf eine authentifizierte Freigabe zugreifen. 

    Es wird empfohlen, den Parameter für die Anmeldeinformationen zu verwenden, um auf Freigaben zuzugreifen. Sogar Freigaben, die für "jeden" offen sind, sind dies in der Regel nicht für nicht authentifizierte Benutzer.

    Nachfolgend sehen Sie eine Beispielausgabe.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Typ **Y** um die Hotfixinstallation zu bestätigen.

7. Überwachen Sie das Update mithilfe des `Get-HcsUpdateStatus`-Cmdlets.

    Die folgende Beispielausgabe zeigt den Status des Updates. Der Parameter `RunInprogress` ist `True`, wenn das Update ausgeführt wird.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Die folgende Beispielausgabe zeigt an, dass das Update abgeschlossen ist. Der Parameter `RunInProgress` ist `False`, wenn das Update abgeschlossen ist.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
        

    > [AZURE.NOTE] Gelegentlich die Cmdlet-Berichte `False` Wenn das Update noch in Bearbeitung ist. Um sicherzustellen, dass das Update abgeschlossen ist, warten Sie einige Minuten, führen Sie diesen Befehl erneut aus, und überprüfen Sie, ob `RunInProgress` gleich `False` ist. Wenn dies der Fall ist, wurde das Hotfix abgeschlossen. 
    
8. Überprüfen Sie nach dem Abschließen des Softwareupdates die Versionen der Systemsoftware. Geben Sie den folgenden Befehl ein:

    `Get-HcsSystem`

    Daraufhin sollten die folgenden Versionen angezeigt werden.

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433 
    
    Wenn die Versionsnummern nach dem Anwenden des Updates nicht geändert werden, bedeutet dies, dass der Hotfix nicht angewendet werden konnte. Sollte dies, wenden Sie sich an [Microsoft-Support](storsimple-contact-microsoft-support.md) um Hilfe zu erhalten.
    
9. Sie installieren nun die Firmwareupdates für Datenträger, die störend sind und etwa 30 bis 45 zum Abschließen benötigen. Sie können diese während eines geplanten Wartungsfensters durch Herstellen einer Verbindung mit der seriellen Konsole des Geräts installieren. Um Festplatten-Firmware-Updates zu installieren, führen Sie die Schritte [Installieren Sie Wartungsmodus-Updates über Windows PowerShell für StorSimple](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).  

10. Nachdem die Firmwareupdates für Datenträger erfolgreich installiert wurden und das Gerät den Wartungsmodus verlassen hat, kehren Sie zum klassischen Azure-Portal zurück. Updates im Wartungsmodus werden im Portal nicht aktualisiert, bis 24 Stunden vergangen sind. Sie müssen möglicherweise warten, bevor Sie die verbliebenen unterbrechungsfreien Updates aus dem klassischen Azure-Portal anwenden. 

11. Wenn Sie Updates installieren möchten, navigieren Sie zu **Wartung** Seite, und klicken Sie unten auf der Seite auf **Updates suchen**. Sie werden benachrichtigt, dass Updates verfügbar sind. Dazu gehören die Treiber und die Windows-Updates. Klicken Sie auf **Updates installieren** mit der Installation beginnen. Sie sind fertig, wenn alle Updates erfolgreich installiert wurden. 





 
 




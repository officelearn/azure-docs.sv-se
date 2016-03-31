#### So beenden und starten Sie ein virtuelles Gerät
Um ein virtuelles Gerät zu beenden, klicken Sie auf den Namen, und klicken Sie dann auf **Herunterfahren**. Während das virtuelle Gerät heruntergefahren wird, wird der Status **Beenden**. Nachdem das virtuelle Gerät beendet wurde, wird der Status **beendet**.

Verwenden Sie die folgenden Cmdlets zum Beenden und Starten eines virtuellen Geräts.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### So starten Sie ein virtuelles Gerät neu

Wenn ein virtuelles Gerät ausgeführt wird, und Sie ihn neu starten möchten, klicken Sie auf den Namen, und klicken Sie dann auf **Neustart**. Während des Neustarts des virtuellen Geräts wird der Status ist **neu gestartet**. Wenn das virtuelle Gerät zur Verwendung bereit ist, wird der Status **unter**.

Verwenden Sie die folgenden Cmdlets für den Neustart eines virtuellen Geräts.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`







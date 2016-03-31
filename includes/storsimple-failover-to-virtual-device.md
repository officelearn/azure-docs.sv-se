#### So stellen Sie Ihr physisches Gerät auf dem virtuellen StorSimple-Gerät wieder her

1. Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloudmomentaufnahmen verfügt.

2. Öffnen Sie die **Gerät** Seite, und klicken Sie dann auf die **Volumecontainer** Registerkarte.

3. Wählen Sie einen Volumecontainer für das Failover auf dem virtuellen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb des Containers anzuzeigen. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten** das Volume offline zu schalten. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.

4. Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem virtuellen Gerät ein Failover durchgeführt werden soll.

5. Auf der **Gerät** Seite, wählen Sie das Gerät, die Sie benötigen, um ein Failover aus, und klicken Sie dann auf **Failover** zum Öffnen der **Geräte-Failover** Assistenten.

6. In **volumecontainer für Failover auswählen**, wählen Sie Failover durchführen möchten. In dieser Liste werden nur offline geschaltete Volumecontainer angezeigt, die eine Cloudmomentaufnahme beinhalten. Wenn ein erwarteter Volumecontainer nicht angezeigt wird, brechen Sie den Assistenten ab, und stellen Sie sicher, dass dieser offline ist.

7. Auf der nächsten Seite in **Zielgerät für Volumes** des ausgewählten Containers, wählen Sie das virtuelle Gerät aus der Dropdown-Liste der verfügbaren Geräte. Nur die Geräte, die über die verfügbare Kapazität verfügen, werden in der Liste angezeigt. 

8. Überprüfen Sie die Failover-Einstellungen auf der **-Failover bestätigen** Seite. Wenn diese richtig sind, klicken Sie auf das Häkchensymbol.

Der Failovervorgang wird gestartet. Wechseln Sie nach Abschluss des Failovers zur Seite "Geräte", und wählen Sie das virtuelle Gerät aus, das als Ziel für den Failovervorgang verwendet wurde. Wechseln Sie zur Seite "Volumecontainer". Es sollten alle angezeigt Volumecontainer und Volumes des alten Geräts angezeigt werden.


> [AZURE.SELECTOR]
- [C# auf Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C unter Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C# auf mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Übersicht über das Szenario

In diesem Szenario erstellen Sie ein Gerät, das die folgenden Telemetrie an die Remoteüberwachung sendet [vorkonfigurierte Lösung][lnk-what-are-preconfig-solutions]:

- Externe Temperatur
- Interne Temperatur
- Luftfeuchtigkeit

Zur Vereinfachung erzeugt der Code auf dem Gerät Beispielwerte. Sie sollten das Beispiel jedoch erweitern, indem Sie echte Sensoren an Ihr Gerät anschließen und tatsächliche Telemetriedaten senden.

## Vorbereitung

Bevor Sie Code für Ihr Gerät schreiben, sollten Sie Ihre vorkonfigurierte Lösung für die Remoteüberwachung und anschließend ein Gerät in dieser Lösung bereitstellen.

### Bereitstellen der vorkonfigurierten Lösung für die Remoteüberwachung

Das Gerät, das Sie erstellen, sendet Daten an eine Instanz von der [Remoteüberwachung][lnk-remote-monitoring] Lösung vorkonfiguriert. Besuchen Sie [Erste Schritte mit Azure IoT-Suite][lnk-getstarted] erstellen ein Azure-Konto und IoT-Suite bereitzustellen. Wählen Sie **Remoteüberwachung** beim Erstellen der neuen Projektmappe.

Wenn die remote-Überwachung-Lösung bereitgestellt wurde, klicken Sie auf **Starten** um die Lösung Dashboard zu öffnen.

![][img-dashboard]

### Bereitstellen des Geräts in der Remoteüberwachungslösung

> [AZURE.NOTE] Wenn Sie ein Gerät in der Projektmappe bereits bereitgestellt haben, können Sie diesen Schritt überspringen. Für das Erstellen der Clientanwendung müssen Sie die Anmeldeinformationen des Geräts kennen.

Damit ein Gerät eine Verbindung mit der vorkonfigurierten Lösung herstellen kann, muss es sich mit gültigen Anmeldeinformationen identifizieren können. Sie können die Anmeldeinformationen des Geräts aus dem Lösungsdashboard abrufen und dann in Ihre Clientanwendung einfügen. 

Um Ihrer Remoteüberwachungslösung ein neues Gerät hinzuzufügen, führen Sie folgende Schritte im Lösungsdashboard durch:

1.  Klicken Sie in der linken unteren Ecke des Dashboards auf **Hinzufügen eines Geräts**.

    ![][1]

2.  In der **benutzerdefinierte Gerät** Bereich, klicken Sie auf **Hinzufügen**.

    ![][2]

3.  Wählen Sie **kann ich meine eigene Geräte-ID definieren**, geben Sie z. B. die Geräte-ID **Mydevice**, klicken Sie auf **-ID überprüfen** Überprüfen Sie, ob dieser Name wird nicht verwendet, und klicken Sie dann auf **Erstellen** an das Gerät bereitstellen.

    ![][3]

5. Notieren Sie die Anmeldeinformationen des Geräts (Geräte-ID, IoT Hub-Hostname und Geräteschlüssel). Ihre Clientanwendung benötigt diese Informationen zum Herstellen einer Verbindung von Ihrem Gerät mit der Remoteüberwachungslösung. Klicken Sie dann auf **Fertig**.

    ![][4]

6. Stellen Sie sicher, dass das Gerät im Abschnitt mit den Geräten richtig angezeigt wird. Der Status ist **ausstehende** bis das Gerät eine Verbindung mit der remote-überwachungslösung herstellt.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: http://www.microsoft.com/server-cloud/internet-of-things/getting-started.aspx
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md



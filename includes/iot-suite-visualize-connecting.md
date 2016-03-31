## Anzeigen der Gerätetelemetrie im Dashboard

Das Dashboard in der Remoteüberwachungslösung ermöglicht Ihnen das Anzeigen der Telemetrie, die Ihre Geräte an IoT Hub senden.

1. Klicken Sie in Ihrem Browser zurück zu den remote-Überwachung Lösung Dashboard auf **Geräte** im linken Bereich, zu dem navigiert das **Geräteliste**.

2. In der **Geräteliste**, sicher, dass der Status des Geräts ist **unter**.

    ![][18]

3. Wählen Sie im Dashboard Ihres Geräts in den **Gerät Ansicht** Dropdown-Liste für die Telemetriedaten anzuzeigen. Die Telemetrie der Beispielanwendung ist mit 50 Einheiten für die Innentemperatur, 55 Einheiten für die Außentemperatur und 50 Einheiten für die Luftfeuchtigkeit konfiguriert. Bitte beachten Sie, dass im Dashboard standardmäßig nur die Werte für Temperatur und die Luftfeuchtigkeit angezeigt werden.

    ![][img-telemetry]

## Senden eines Befehls an ein Gerät

Das Dashboard in der Remoteüberwachungslösung ermöglicht es Ihnen, IoT Hub anzuweisen, Befehle an Ihre Geräte zu senden. In der Remoteüberwachungslösung können Sie beispielsweise einen Befehl senden, mit dem die Innentemperatur eines Geräts eingestellt wird.

1. Klicken Sie in der remote-Überwachung Lösung Dashboard auf **Geräte** im linken Bereich, zu dem navigiert das **Geräteliste**.

2. Klicken Sie auf **Geräte-ID** für Ihr Gerät in der **Geräteliste**.

3. In der **Gerätedetails** auf **Befehle**.

    ![][13]

4. In der **Befehl** Dropdown-Liste, wählen **SetTemperature**, und klicken Sie dann im **Temperatur** Geben Sie einen neuen Wert. Klicken Sie auf **Befehl Senden** den Befehl an das Gerät senden.

    ![][14]

    > [AZURE.NOTE] Befehlsverlauf zeigt anfänglich der Befehlsstatus als **ausstehende**. Wenn das Gerät mit dem Befehl bestätigt wird, ändert sich der Status **Erfolg**.

5. Stellen Sie auf dem Dashboard sicher, dass das Gerät jetzt 75 als den neuen Temperaturwert sendet.

## Nächste Schritte

Artikel [Anpassen vorkonfigurierter Lösungen][lnk-customize] Beschreibung einiger Unterschiede, Sie können dieses Beispiel erweitern. Mögliche Erweiterungen umfassen die Verwendung echter Sensoren und die Implementierung zusätzlicher Befehle.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging



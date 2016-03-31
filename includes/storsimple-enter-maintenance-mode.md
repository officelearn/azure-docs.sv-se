<!--author=SharS last changed: 12/01/15-->

#### So wechseln Sie in den Wartungsmodus

1. Wählen Sie im Menü seriellen Konsole Option 1, **Anmeldung mit Vollzugriff**.

2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Kennwort1**.

3. Geben Sie an der Eingabeaufforderung Folgendes ein:

     `Enter-HcsMaintenanceMode`

4. Es wird eine Warnmeldung angezeigt, die Sie darüber informiert, dass durch den Wartungsmodus alle E/A-Anforderungen unterbrochen werden und die Verbindung mit dem klassischen Azure-Portal getrennt wird. Sie werden aufgefordert, dies zu bestätigen. Typ **Y** die in den Wartungsmodus.

    Beide Controller werden neu gestartet. Wenn der Neustart abgeschlossen ist, wird eine andere Meldung angezeigt, die besagt, dass das Gerät in den Wartungsmodus versetzt wird.



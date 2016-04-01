<!--author=SharS last changed: 9/17/15-->

#### So stellen Sie über die serielle Konsole eine Verbindung her

1. Schließen Sie das serielle Kabel (direkt oder über einen seriellen USB-Adapter) an das Gerät an.

2. Öffnen Sie die **Systemsteuerung**, und öffnen Sie dann die **Geräte-Manager**.

3. Identifizieren Sie den COM-Anschluss, wie in der folgenden Abbildung dargestellt.

     ![Herstellen einer Verbindung über die serielle Konsole](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)

4. Starten Sie PuTTY. 

5. Ändern Sie im rechten Bereich die **Verbindungstyp** zu **seriellen**.

6. Geben Sie im rechten Bereich den entsprechenden COM-Anschluss ein. Stellen Sie sicher, dass die Parameter für die serielle Konfiguration wie folgt festgelegt sind:
  - Geschwindigkeit: 115.200
  - Datenbits: 8
  - Stoppbits: 1
  - Parität: Kein(e)
  - Flusssteuerung: Kein(e)

    Diese Einstellungen werden in der folgenden Abbildung gezeigt.

     ![PuTTY-Einstellungen](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 

    > [AZURE.NOTE] Falls der Standardwert für die flusssteuerung nicht funktioniert, versuchen Sie die Flussteuerung auf XON/XOFF.

7. Klicken Sie auf **Öffnen** um eine serielle Sitzung zu starten.
 


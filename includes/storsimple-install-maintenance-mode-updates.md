<!--author=SharS last changed: 9/17/15-->

#### So installieren Sie Wartungsmodus-Updates über Windows PowerShell für StorSimple

1. Wenn Sie dies noch nicht geschehen, Zugriff auf die serielle Konsole des Geräts, und wählen Sie Option 1, **Anmeldung mit Vollzugriff**. 

2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Kennwort1**.

3. Geben Sie an der Eingabeaufforderung Folgendes ein:

     `Get-HcsUpdateAvailability` 
    
4. Sie werden benachrichtigt, wenn Updates verfügbar sind und ob diese Updates mit oder ohne Unterbrechungen installiert werden können. Um Updates anzuwenden, durch die der Betrieb unterbrochen wird, müssen Sie das Gerät in den Wartungsmodus versetzen. Finden Sie unter [Schritt2: Geben Sie Wartungsmodus](storsimple-update-device.md#step2) Anweisungen.

5. Wenn sich Ihr Gerät im Wartungsmodus befindet, geben Sie an der Eingabeaufforderung folgenden Befehl ein: `Start-HcsUpdate`.

6. Sie werden aufgefordert, diesen Schritt zu bestätigen. Nachdem Sie die Updates bestätigt haben, werden sie auf dem Controller installiert, auf den Sie gerade zugreifen. Nachdem die Updates installiert wurden, wird der Controller neu gestartet. 

7. Überwachen Sie den Status der Aktualisierungen. Geben Sie Folgendes ein:

    `Get-HcsUpdateStatus`
    
    Wenn `RunInProgress` `True` lautet, ist das Update noch nicht abgeschlossen. Wenn `RunInProgress``False` lautet, ist das Update abgeschlossen.  

7. Wurde der aktuelle Controller nach der Installation der Aktualisierung neu gestartet, stellen Sie eine Verbindung zu dem anderen Controller her und führen Sie die Schritte 1 bis 6 durch.

8. Nachdem beide Controller aktualisiert wurden, beenden Sie den Wartungsmodus. Finden Sie unter [Schritt 4: Beenden des Wartungsmodus](storsimple-update-device.md#step4) Anweisungen.



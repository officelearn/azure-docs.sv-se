<!--author=SharS last changed: 9/16/15-->

#### Hinzufügen eines Volumecontainers

1. Auf der **Geräte** Seite, wählen Sie das Gerät, doppelklicken Sie darauf, und klicken Sie dann auf die **volumecontainer** Registerkarte.

2. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite. In der **volumecontainer erstellen** Dialogfeld Feld, gehen Sie folgendermaßen vor:

  1. Geben Sie einen eindeutigen **Namen** für Volume-Container. Dieser Name darf maximal 32 Zeichen enthalten.
  2. Wählen Sie eine **Speicherkonto** diesem volumecontainer zugeordnet werden. Sie können ein vorhandenes Speicherkonto innerhalb desselben Abonnements oder Select **Weitere hinzufügen** ein Speicherkonto aus einem anderen Abonnement auswählen. Sie können auch das Speicherkonto auswählen, das beim Erstellen des Diensts zum ersten Mal generiert wurde.
  3. Geben Sie als Bandbreite **unbegrenzt** Wenn Sie die gesamte verfügbare Bandbreite nutzen möchten oder **benutzerdefinierte** um Bandbreite zu steuern. Geben Sie einen Wert zwischen 1 und 1000 MBit/s als benutzerdefinierte Bandbreite an. Um Bandbreite basierend auf einem Zeitplan zuzuordnen, können Sie **Wählen Sie eine bandbreitenvorlage**.
  4. Es wird empfohlen, Sie halten **Cloudspeicherverschlüsselung aktivieren** ausgewählt, um die Daten in der Cloud verschlüsselt werden. Deaktivieren Sie die Verschlüsselung nur dann, wenn Sie andere Verfahren zum Verschlüsseln Ihrer Daten anwenden.
  5. Geben Sie einen **Cloudverschlüsselungsschlüssel** der zwischen 8 und 32 Zeichen enthält. Das Gerät verwendet diesen Schlüssel für den Zugriff auf die verschlüsselten Daten. In der **Cloud-Speicher-Verschlüsselungsschlüssel bestätigen** Geben Sie den Verschlüsselungsschlüssel für Cloud-Speicher erneut aus, um es zu bestätigen.
  6. Klicken Sie auf den Pfeil, um zur nächsten Seite zu wechseln.

    ![Create volume container with bandwidth template 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 

3. Wenn Sie angegeben **Wählen Sie eine bandbreitenvorlage**, wählen Sie aus der Dropdown-Liste der vorhandenen bandbreitenvorlagen. Überprüfen Sie die zeitplaneinstellungen, und klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).

    ![Erstellen eines Volumecontainers mit Bandbreitenvorlage 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

Der volumecontainer wird gespeichert, und der neu erstellte volumecontainer wird auf aufgeführt der **volumecontainer** Seite.
 



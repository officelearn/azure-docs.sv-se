<!--author=SharS last changed: 9/17/15-->

#### So erstellen Sie einen Volumecontainer

1. Das Gerät **Schnellstart** auf **volumecontainer hinzufügen**. Die **Volume-Container erstellen** das Dialogfeld wird angezeigt.

    ![Volumecontainer erstellen](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)

2. In der **volumecontainer erstellen** (Dialogfeld):
  1. Geben Sie einen **Namen** für Volume-Container. Der Name muss 3 bis 32 Zeichen lang sein.
  2. Wählen Sie eine **Speicherkonto** die diesem volumecontainer zugeordnet. Sie können das Standardkonto auswählen, das beim Erstellen des Diensts generiert wird. Sie können auch die **Neu hinzufügen** Option, um ein Speicherkonto anzugeben, die mit diesem Dienstabonnement verknüpft ist.
  3. Wählen Sie **Cloudspeicherverschlüsselung aktivieren** zum Aktivieren der Verschlüsselung der Daten vom Gerät in die Cloud gesendet.
  4. Geben Sie und bestätigen ein **Verschlüsselungsschlüssel für Cloud-Speicher** 8 bis 32 Zeichen lang sein. Dieser Schlüssel wird vom Gerät für den Zugriff auf verschlüsselte Daten verwendet.
  5. Wählen Sie **unbegrenzt** in der **Geben Bandbreite** Dropdown-Liste, wenn Sie die gesamte verfügbare Bandbreite nutzen möchten. Sie können diese Option auch festlegen, um **benutzerdefinierte** Bandbreite zu steuern, und geben Sie einen Wert zwischen 1 und 1.000 MBit/s. 
  Wenn Sie die Informationen zur bandbreitenauslastung haben, können Sie möglicherweise Bandbreite basierend auf einem Zeitplan durch Angabe zuordnen **Wählen Sie eine bandbreitenvorlage**. Eine schrittweise Anleitung finden Sie unter [bandbreitenvorlage hinzufügen](storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
  6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) Um diesen volumecontainer zu speichern und den Assistenten zu beenden. 

  Wird der neu erstellte volumecontainer aufgelistet, auf die **volumecontainer** Seite.

![Video verfügbar](./media/storsimple-create-volume-container/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, die veranschaulicht, wie Sie einen volumecontainer in Ihrer StorSimple-Lösung erstellen, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).


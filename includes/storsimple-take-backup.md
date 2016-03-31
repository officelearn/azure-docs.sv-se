<!--author=alkohli last changed: 9/17/15-->

### So erstellen Sie eine Sicherung

1. Auf dem Gerät **Schnellstart** auf **Hinzufügen einer Sicherungsrichtlinie**. Damit wird der Assistent zum Hinzufügen einer Sicherungsrichtlinie gestartet. 

2. Auf der **Sicherungsrichtlinie definieren** Seite:
  1. Geben Sie für Ihre Sicherungsrichtlinie einen Namen an, der zwischen 3 und 150 Zeichen lang ist.
  2. Wählen Sie die Volumes aus, die gesichert werden sollen. Wenn Sie mehrere Volumes auswählen, werden diese gruppiert, um eine absturzkonsistente Sicherung zu erstellen.
  3. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
  
    ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)

3. Auf der **definieren Sie einen Zeitplan** Seite:
  1. Wählen Sie den Sicherungstyp aus der Dropdownliste aus. Wählen Sie für schnellere Wiederherstellungen **lokale Momentaufnahme**. Wählen Sie für datenresilienz **Cloudmomentaufnahme**.
  2. Geben Sie die Sicherungshäufigkeit in Minuten, Stunden, Tagen oder Wochen an.
  3. Wählen Sie eine Aufbewahrungsdauer aus. Die Aufbewahrungsdauer hängt von der Sicherungshäufigkeit ab. Für eine tägliche Richtlinie kann die Aufbewahrungsdauer z. B. in Wochen angegeben werden, während die Aufbewahrungsdauer für eine monatliche Richtlinie Monate beträgt.
  4. Wählen Sie die Startzeit und das Datum für die Richtlinie aus.
  5. Wählen Sie die **aktivieren** das Kontrollkästchen, um die Sicherungsrichtlinie zu aktivieren. 
  6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-take-backup/HCS_CheckIcon-include.png) um die Richtlinie zu speichern.

    ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
 
     You now have a backup policy that will create scheduled backups of your volume data.

Sie haben die Gerätekonfiguration abgeschlossen. 

![Video verfügbar](./media/storsimple-take-backup/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, die veranschaulicht, wie Sie eine StorSimple Sicherung erstellen, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).


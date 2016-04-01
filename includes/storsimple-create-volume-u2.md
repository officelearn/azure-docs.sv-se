<!--author=alkohli last changed:12/14/15-->

#### So erstellen Sie ein Volume

1. Auf dem Gerät **Schnellstart** auf **Volume hinzufügen**. Dadurch wird der Assistent "Volume hinzufügen" gestartet.

2. Hinzufügen eines Assistenten Volume unter **Grundeinstellungen**:

    4. Geben Sie einen **Namen** für das Volume.
    5. Wählen Sie in der Dropdown-Liste die **Verwendungstyp** für das Volume. Wählen Sie für Arbeitslasten, die lokale Garantien, niedrige Wartezeiten und höhere Leistung erfordern, einen **lokal angeheftet** Volume. Wählen Sie für alle anderen Daten eine **mehrstufigen** Volume. Wenn Sie dieses Volume für archivierte Daten verwenden, überprüfen Sie **Verwenden Sie dieses Volume für weniger häufig abgerufene Archivierungsdaten**. 
    
        Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume lokal auf dem Gerät verbleiben und nicht in die Cloud übergehen.  Wenn Sie ein lokales Volume erstellen, überprüft das Gerät den verfügbaren Speicherplatz auf den lokalen Ebenen, um das Volume in der angeforderten Größe bereitzustellen. Bei der Erstellung eines lokalen Volumes werden vorhandene Daten eventuell vom Gerät in die Cloud übertragen, und das Erstellen des Volumes kann längere Zeit in Anspruch nehmen. Die gesamte Zeit hängt von der Größe des bereitgestellten Volumes, von der verfügbaren Netzwerkbandbreite und von den Daten auf Ihrem Gerät ab. 

        Ein mehrstufiges Volume wird mit schlanker Speicherzuweisung bereitgestellt und kann sehr schnell erstellt werden. Bei Verwendung der mehrstufige Volume für archivierte Daten auswählen **Verwenden Sie dieses Volume für weniger häufig abgerufene Archivierungsdaten** ändert sich die Deduplizierung Segmentgröße für das Volume auf 512 KB. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.

    3. Geben Sie den **bereitgestellte Kapazität** für das Volume. Notieren Sie die Kapazität, die basierend auf dem ausgewählten Volumetyp verfügbar ist. Die angegebene Volumegröße darf den verfügbaren Speicherplatz nicht überschreiten.

        Sie können lokale Volumes von bis zu 9 TB oder mehrstufige Volumes von bis zu 200 TB auf dem 8100-Gerät bereitstellen. Auf dem größeren 8600-Gerät können Sie lokale Volumes von bis zu 24 TB oder mehrstufige Volumes von bis zu 500 TB bereitstellen. Da für das Hosten des Arbeitssatzes mehrstufiger Volumes lokaler Speicher auf dem Gerät erforderlich ist, wirkt sich die Erstellung lokaler Volumes auf den Speicherplatz aus, der für die Bereitstellung mehrstufiger Volumes zur Verfügung steht. Wenn Sie ein lokal angeheftete Volume erstellen, wird daher Speicherplatz für die Erstellung von Volumes in Ebenen reduziert. Wenn einem mehrstufigen Volume erstellt wird, wird entsprechend der verfügbare Speicherplatz für die Erstellung von lokal angeheftete Volumes reduziert. 

        Wenn Sie ein lokales Volume von 9 TB (maximal zulässige Größe) auf dem 8100-Gerät bereitstellen, haben Sie damit den gesamten lokalen Speicherplatz erschöpft, der auf dem Gerät verfügbar ist. Ab diesem Zeitpunkt können Sie keine mehrstufigen Volumes mehr erstellen, da auf dem Gerät kein lokaler Speicherplatz zum Hosten des Arbeitssatzes mehrstufiger Volumes mehr vorhanden ist. Vorhandene mehrstufige Volumes wirken sich ebenfalls auf den verfügbaren Speicherplatz aus. Wenn Sie beispielsweise ein 8100-Gerät verwenden, auf dem bereits mehrstufige Volumes von 100 TB vorhanden sind, stehen nur noch 4,5 TB Speicherplatz für lokale Volumes zur Verfügung.
   
   4. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) um zur nächsten Seite zu gelangen.

        ![Volume hinzufügen](./media/storsimple-create-volume-u2/AddLocalVolume1.png)

3. In der **zusätzliche Einstellungen** Dialogfeld fügen Sie einen neuen Access Control Record (ACR) hinzu:

    1. Geben Sie einen **Namen** für Ihren ACR.
    2. Unter **iSCSI-Initiatorname**, geben Sie den iSCSI-qualifizierten Namen (IQN) des Windows-Hosts. Wenn Sie den IQN nicht haben, fahren Sie mit [Abrufen des IQN eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host).
    3. Klicken Sie unter **standardsicherung für dieses Volume?**, wählen die **aktivieren** das Kontrollkästchen. Die Standardsicherung erstellt eine Richtlinie, die jeden Tag um 22:30 Uhr ausgeführt wird (Uhrzeit des Geräts) und eine Cloud-Momentaufnahme dieses Volumes erstellt.
     
     > [AZURE.NOTE] Nachdem die Sicherung hier aktiviert wurde, kann sie nicht zurückgesetzt werden. Sie müssen das Volume bearbeiten, um diese Einstellung zu ändern.

     ![Volume hinzufügen](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)

4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Ein Volume wird mit den angegebenen Einstellungen erstellt.





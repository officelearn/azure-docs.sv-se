<!--author=SharS last changed: 11/16/15-->

#### So erstellen Sie ein Volume

1. Auf dem Gerät **Schnellstart** auf **Volume hinzufügen**. Dadurch wird der Assistent "Volume hinzufügen" gestartet.

2. Hinzufügen eines Assistenten Volume unter **grundlegende Einstellungen**, gehen Sie folgendermaßen vor:
   1. Geben Sie einen **Namen** für das Volume.
   2. Geben Sie den **bereitgestellte Kapazität** für das Volume in GB oder TB. Die Volumekapazität muss für ein physisches Gerät zwischen 1 GB und 64 TB liegen.
   3. Wählen Sie in der Dropdown-Liste die **Verwendungstyp** für das Volume. 
   4. Wenn Sie dieses Volume für archivierte Daten verwenden, wählen Sie die **Verwenden Sie dieses Volume für weniger häufig abgerufene Archivierungsdaten** das Kontrollkästchen. Wählen Sie für alle anderen Fällen einfach **Ebenen Volume**. (Mehrstufige Volumes wurden früher primäre Volumes genannt).
   4. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) um zur nächsten Seite zu gelangen.

        ![Volume hinzufügen](./media/storsimple-create-volume/AddVolume1-include.png)

3. In der **zusätzliche Einstellungen** Dialogfeld fügen Sie einen neuen Access Control Record (ACR) hinzu:
   1. Geben Sie einen **Namen** für Ihren ACR.
   2. Unter **iSCSI-Initiatorname**, geben Sie den iSCSI-qualifizierten Namen (IQN) des Windows-Hosts. Wenn Sie den IQN nicht haben, fahren Sie mit [Abrufen des IQN eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host).
   3. Es wird empfohlen, eine Sicherung, indem aktivieren die **Standardsicherung für dieses Volume** das Kontrollkästchen. Die Standardsicherung erstellt eine Richtlinie, die jeden Tag um 22:30 Uhr ausgeführt wird (Uhrzeit des Geräts) und eine Cloud-Momentaufnahme dieses Volumes erstellt.

        > [AZURE.NOTE] Nachdem die Sicherung hier aktiviert wurde, kann sie nicht zurückgesetzt werden. Sie müssen das Volume bearbeiten, um diese Einstellung zu ändern.

        ![Volume hinzufügen](./media/storsimple-create-volume/AddVolume2-include.png)

4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Ein Volume wird mit den angegebenen Einstellungen erstellt.

![Video verfügbar](./media/storsimple-create-volume/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, die veranschaulicht, wie ein StorSimple-Volume zu erstellen, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).




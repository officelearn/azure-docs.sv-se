<!--author=alkohli last changed: 9/17/15-->

#### So fügen Sie ein Speicherkonto zur StorSimple 8000-Serie mit Update 1.0 hinzu

1. Wählen Sie auf der StorSimple-Manager-Dienst-Startseite Ihren Dienst aus, und doppelklicken Sie dann darauf. Dadurch gelangen Sie auf die **Schnellstart** Seite. Wählen Sie die **konfigurieren** Seite.

2. Klicken Sie auf **Speicherkonto hinzufügen/bearbeiten**.

3. In der **Speicherkonto hinzufügen/bearbeiten** Dialogfeld klicken Sie auf **Hinzufügen**.

4. In der **Anbieter** aus den entsprechenden Cloud-Dienstanbieter. Die unterstützten Anbieter sind: Azure, Amazon S3, Amazon S3 mit RRS, HP und OpenStack. Geben Sie die Anmeldeinformationen und den Speicherort des Speicherkontos Ihres Clouddienstanbieters an. Die Felder für die Anmeldeinformationen unterscheiden sich in Abhängigkeit vom angegebenen Clouddienstanbieter. 
  - Wenn Sie Azure als clouddienstanbieter ausgewählt haben, geben Sie den **Namen** die primäre **Zugriffstaste** für Ihr Microsoft Azure-Speicherkonto. Der Speicherort wird für ein Azure-Konto automatisch angegeben.

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - Wenn Sie Amazon S3 oder Amazon S3 mit RRS ausgewählt haben, geben Sie einen aussagekräftigen **Speicherkonto**, **Zugriffstaste**, und **Geheimschlüssel**. Bei Amazon S3 und Amazon S3 mit RRS werden die folgenden Speicherorte unterstützt:

        - US Standard
        - US West (Oregon)
        - US West (Northern California)
        - EU (Ireland)
        - Asia Pacific (Singapore)
        - Asia Pacific (Sydney)
        - Asia Pacific (Tokyo)
        - South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
            
 - Wenn Sie HP als clouddienstanbieter ausgewählt haben, geben Sie einen aussagekräftigen **Speicherkontoname**, **Mandanten-ID**, **Benutzername**, und **Kennwort**. Bei HP werden die folgenden Speicherorte unterstützt:

        - US East
        - US West
      
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
            
 - Wenn Sie ausgewählt haben **Openstack** als Ihre Cloud-Anbieter bieten eine **Hostnamen**, **Zugriffstaste**, und **Geheimschlüssel**.

        > [AZURE.NOTE] Alle der Cloud-Dienstanbieter, Azure, ausgenommen ist ein Anzeigename zulässig. Sie können verschiedene Anzeigenamen verwenden und mehr als ein Speicherkonto mit den gleichen Satz von Anmeldeinformationen zu erstellen.

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. Wählen Sie **SSL-Modus aktivieren** um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem Gerät und der Cloud zu erstellen. Deaktivieren der **SSL-Modus aktivieren** Kontrollkästchen nur, wenn Sie in einer privaten Cloud ausgeführt werden.

      > [AZURE.NOTE] Wenn Sie HP als Anbieter verwenden, wird immer SSL aktiviert werden.
        
6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Sie werden benachrichtigt, nachdem das Speicherkonto erfolgreich erstellt wurde.

7. Das neu erstellte Speicherkonto erscheint auf der **konfigurieren** Seite **Speicherkonten**. Klicken Sie auf **Speichern** das neue Speicherkonto zu speichern. Klicken Sie auf **OK** Wenn zur Bestätigung aufgefordert.



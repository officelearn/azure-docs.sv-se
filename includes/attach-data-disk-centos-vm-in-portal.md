1. In der Azure- [Verwaltungsportal](http://manage.windowsazure.com), klicken Sie auf **virtuelle Computer** und wählen Sie dann den virtuellen Computer, die Sie gerade erstellt haben (**Testlinuxvm**).

2. Klicken Sie auf der Befehlsleiste auf **Anfügen** und klicken Sie dann auf **leeren Datenträger anschließen**.

    Die **leeren Datenträger anschließen** das Dialogfeld wird angezeigt.


3. Die **Name des virtuellen Computers**, **-Speicherort**, und **Dateiname** bereits für Sie definiert sind. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Typ **5** in die **Größe** Feld.

    ![Leeren Datenträger einbinden][Image2]

    **Hinweis:** alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

4. Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

5. Klicken Sie auf den Namen des virtuellen Computers, um das Dashboard anzuzeigen; so können Sie überprüfen, ob der Datenträger erfolgreich an den virtuellen Computer angefügt wurde. Die von Ihnen angeschlossene Datenträger wird aufgeführt, der **Datenträger** Tabelle.

    Wenn Sie einen Datenträger anfügen, kann er erst verwendet werden, nachdem Sie sich angemeldet und die Einrichtung abgeschlossen haben.

##Herstellen einer Verbindung mit dem virtuellen Computer mithilfe von SSH oder PuTTY und Abschließen der Einrichtung
Melden Sie sich beim virtuellen Computer an, um die Einrichtung des Datenträgers abzuschließen und ihn zum Speichern von Daten zu nutzen.

1. Nachdem der virtuelle Computer bereitgestellt wird, Verbinden mit SSH oder PuTTY her, und melden Sie sich als **Newuser** (wie in den obigen Schritten beschrieben). 


2. Geben Sie in das SSH- oder PuTTY-Fenster folgenden Befehl ein, und geben Sie dann das Kontokennwort ein:

    `$ sudo grep SCSI /var/log/messages`

    Sie finden den Bezeichner des letzten Datenträgers, die in den Nachrichten hinzugefügt wurde, die angezeigt werden (**Sdc**, in diesem Beispiel).

    ![GREP][Image4]


3. Geben Sie in das SSH oder PuTTY-Fenster den folgenden Befehl zum Partitionieren des Datenträgers **/Dev/Sdc**:

    `$ sudo fdisk /dev/sdc`


4. Geben Sie **n** um eine neue Partition zu erstellen.

    ![FDISK][Image5]


5. Typ **p** um der Partition als primäre Partition festzulegen, geben Sie **1** auf die erste Partition, und geben Sie dann EINGABETASTE, um den Standardwert (1) übernehmen für den Zylinder zu erleichtern.

    ![FDISK][Image6]


6. Typ **p** um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.

    ![FDISK][Image7]


7. Typ **w** um die Einstellungen für den Datenträger schreiben.

    ![FDISK][Image8]


8. Formatieren Sie den neuen Datenträger mit dem **Mkfs** Befehl:

    `$ sudo mkfs -t ext4 /dev/sdc1`

9. Als Nächstes muss ein Verzeichnis zum Einbinden des neuen Dateisystems zur Verfügung stehen. Geben Sie z. B. den folgenden Befehl ein, um ein Verzeichnis zum Anfügen des Laufwerks zu erstellen, und geben Sie anschließend das Kontokennwort ein:

    `sudo mkdir /datadrive`


10. Geben Sie den folgenden Befehl ein, um das Laufwerk einzubinden:

    `sudo mount /dev/sdc1 /datadrive`

    Der Datenträger kann nun als verwenden **/datadrive**.


11. Fügen Sie das neue Laufwerk zu /etc/fstab hinzu:

    Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es zur Datei /etc/fstab hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique IDentifier) in /etc/fstab zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (d. h. auf /dev/sdc1) zu verweisen. Um den UUID des neuen Laufwerks herauszufinden, Sie können, die **Blkid** Hilfsprogramm:
    
        `sudo -i blkid`

    Die Ausgabe sieht etwa wie folgt aus:

        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

    >[AZURE.NOTE] blkid ist nicht in allen Fällen einen Sudo-Zugang, allerdings ist es möglicherweise leichter mit `sudo -i` auf manchen Distributionen, wenn/sbin oder/Usr/sbin sich nicht in Ihrem `$PATH`.

    **Vorsicht:** kann eine falsche Bearbeitung der Datei/Etc/Fstab in einem nicht mehr startfähigen System führen. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Verteilung Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

    Geben Sie in einem Texteditor die Informationen zum neuen Dateisystem am Ende der Datei /etc/fstab ein.  In diesem Beispiel verwenden wir den UUID-Wert für die neue **/Dev/sdc1** Gerät, das in den vorherigen Schritten erstellt, und den Bereitstellungspunkt wurde **/datadrive**:

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in "/etc/fstab" einfügen.

    Sie können nun testen, die das Dateisystem korrekt eingebunden wurde, indem einfach Aufheben der Bereitstellung, und klicken Sie dann erneut das Dateisystem laden, d. h. Verwenden Sie das Beispiel Bereitstellungspunkt `/datadrive` in den vorhergehenden Schritten erstellt: 

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Wenn der zweite Befehl zu einem Fehler führt, prüfen Sie die Datei /etc/fstab auf korrekte Syntax.


    >[AZURE.NOTE] Später einen Datenträger entfernen, ohne fstab könnte die VM neu gestartet. Für den Fall, dass dieses Problem häufiger auftritt, bieten die meisten Distributionen die fstab-Optionen `nofail` und/oder `nobootwait`. Diese erlauben einen Systemstart auch dann, wenn der Datenträger nicht vorhanden ist. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Verteilung.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png




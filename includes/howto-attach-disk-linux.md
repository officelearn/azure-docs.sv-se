
Weitere Informationen zu Datenträgern finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](../articles/virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Gewusst wie: Anfügen eines leeren Datenträgers
Eine einfachere Methode zum Hinzufügen eines Datenträgers besteht im Anfügen eines leeren Datenträgers, weil Azure die VHD-Datei automatisch erstellt und sie im Speicherkonto ablegt.

1.  Öffnen Sie die Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Finden Sie unter [Verbinden
    mit Windows Azure in Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-connect.md) für weitere Details.

2.  Stellen Sie sicher, dass Sie sich im standardmäßigen Azure-Dienstverwaltungsmodus befinden, indem Sie `azure config
    mode asm` eingeben.

3.  Geben Sie, wie nachstehend gezeigt, den Befehl `azure vm disk attach-new` ein, um einen neuen Datenträger zu erstellen und anzufügen. Beachten Sie, dass die
    _UbuntuVMasm_ wird ersetzt durch den Namen des virtuellen Linux-Computer, die Sie in Ihrem Abonnement erstellt haben. Die Zahl 30 ist die Größe des Datenträgers in GB in diesem Beispiel.

        azure vm disk attach-new ubuntuVMasm 30

4.  Nachdem der Datenträger erstellt und angefügt wurde, wird er in der Ausgabe von `azure vm disk list
    <virtual-machine-name>` wie folgt aufgeführt:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Gewusst wie: Anfügen eines vorhandenen Datenträgers

Zum Anfügen eines vorhandenen Datenträgers wird eine VHD-Datei im Speicherkonto benötigt.

1.  Öffnen Sie die Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Finden Sie unter [Verbinden
    mit Windows Azure in Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-connect.md) für weitere Details.

2.  Stellen Sie sicher, dass Sie sich im standardmäßigen Azure-Dienstverwaltungsmodus befinden. Wenn Sie geändert haben
    Modus zum Ressourcen-Manager wiederherstellen einfach durch Eingabe `azure config mode asm`.

3.  Um herauszufinden, ob die VHD, die Sie anfügen möchten, bereits in Ihr Azure-Abonnement hochgeladen wurde, geben Sie Folgendes ein:

        $azure vm disk list
        info:    Executing command vm disk list
        + Fetching disk images
        data:    Name                                          OS
        data:    --------------------------------------------  -----
        data:    myTestVhd                                     Linux
        data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
        data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
        info:    vm disk list command OK

4.  Wenn Sie den Datenträger nicht, den Sie verwenden möchten finden, können Sie eine lokale virtuellen Festplatte zu Ihrem Abonnement mithilfe hochladen
    `azure vm disk create` oder `azure vm disk upload`. Hier ein Beispiel:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
        info:    Executing command vm disk create
        + Retrieving storage accounts
        info:    VHD size : 10 GB
        info:    Uploading 10485760.5 KB
        Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
        info:    Finishing computing MD5 hash, 16% is complete.
        info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
        uploaded successfully
        info:    vm disk create command OK

    Sie können auch den Befehl `azure vm disk upload` zum Hochladen einer VHD in ein bestimmtes Speicherkonto angeben. Weitere Informationen über die Befehle zum Verwalten der Datenträger Ihres virtuellen Azure-Computer [hier](../virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

5.  Geben Sie den folgenden Befehl ein, um die gewünschten hochgeladene VHD an Ihren virtuellen Computer anzufügen:

        $azure vm disk attach ubuntuVMasm myTestVhd
        info:    Executing command vm disk attach
        + Getting virtual machines
        + Adding Data-Disk
        info:    vm disk attach command OK

    Denken Sie daran _UbuntuVMasm_ mit dem Namen des virtuellen Computers, und _MyTestVhd_ mit der gewünschten virtuellen Festplatte.

6.  Sie können mit dem Befehl `azure vm disk list
    <virtual-machine-name>` überprüfen, ob der Datenträger an den virtuellen Computer angefügt ist:

        $azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    1    10        test.VHD
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK


> [AZURE.NOTE]
> Wenn Sie einen Datenträger angefügt haben, müssen Sie sich auf dem virtuellen Computer anmelden und den Datenträger initialisieren, damit der virtuelle Computer ihn zur Speicherung verwenden kann.



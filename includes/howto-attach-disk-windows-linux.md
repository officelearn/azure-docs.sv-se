
Weitere Informationen zu Datenträgern finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](../articles/virtual-machines-disks-vhds.md).

##<a id="attachempty"></a>Anfügen eines leeren Datenträgers

Eine einfachere Methode zum Hinzufügen eines Datenträgers besteht im Anfügen eines leeren Datenträgers, weil Azure die VHD-Datei automatisch erstellt und sie im Speicherkonto ablegt.

1. Klicken Sie auf **virtuelle Computer**, und wählen Sie dann den entsprechenden virtuellen Computer.

2. Klicken Sie auf der Befehlsleiste auf **Anfügen**, und klicken Sie dann auf **leeren Datenträger anschließen**.


    ![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.  Die **Anfügen eines leeren Datenträgers** das Dialogfeld wird angezeigt.


    ![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)


    Do the following:

    - In **File Name**, accept the default name or type another one for the .vhd file, which is used for the disk. The data disk uses an automatically generated name, even if you type another name for the .vhd file.

    - Type the **Size (GB)** of the data disk.

    - Click the check mark to finish.

4.  Nachdem der Datenträger erstellt und angefügt ist, wird er im Dashboard des virtuellen Computers aufgeführt.

    ![Leerer Datenträger erfolgreich angefügt](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)
    
> [AZURE.NOTE]
> Nachdem Sie neuen einen Datenträger hinzugefügt haben, müssen Sie sich am virtuellen Computer anmelden und den Datenträger initialisieren, damit der virtuelle Computer ihn zur Speicherung verwenden kann. 

##<a id="attachexisting"></a>Anfügen eines vorhandenen Datenträgers

Zum Anfügen eines vorhandenen Datenträgers wird eine VHD-Datei im Speicherkonto benötigt. Verwenden der [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) Cmdlet, um die VHD-Datei in das Speicherkonto hochzuladen. Nachdem Sie eine VHD-Datei erstellt und hochgeladen haben, können Sie sie an einen virtuellen Computer anfügen.

1. Klicken Sie auf **virtuelle Computer**, und wählen Sie dann den entsprechenden virtuellen Computer.

2. Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie dann **Datenträger anfügen**.


    ![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

    The **Attach Disk** dialog box appears.



    ![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Wählen Sie den Datenträger aus, den Sie an den virtuellen Computer anfügen möchten.

4. Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

5.  Nachdem der Datenträger angefügt ist, wird er im Dashboard des virtuellen Computers aufgeführt.


    ![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)





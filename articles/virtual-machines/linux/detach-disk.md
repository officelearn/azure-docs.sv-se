---
title: Koppla från en datadisk från en virtuell Linux-dator – Azure
description: Lär dig hur du kopplar från en datadisk från en virtuell dator i Azure med hjälp av Azure CLI eller Azure Portal.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036375"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Så här kopplar du från en datadisk från en virtuell Linux-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Disken tas bort från den virtuella datorn, men den tas inte bort från lagrings platsen. I den här artikeln arbetar vi med en Ubuntu LTS 16,04-distribution. Om du använder en annan distribution kan det vara olika instruktioner för att demontera disken.

> [!WARNING]
> Om du kopplar från en disk tas den inte bort automatiskt. Om du prenumererar på Premium Storage kommer du att fortsätta att betala lagrings avgifter för disken. Mer information finns i [priser och fakturering när du använder Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Anslut till den virtuella datorn för att demontera disken

Innan du kan koppla från disken med CLI eller portalen måste du demontera disken och ta bort referenser till om från din fstab-fil.

Anslut till den virtuella datorn. I det här exemplet är den offentliga IP-adressen för den virtuella datorn *10.0.1.4* med namnet *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Börja med att leta upp den datadisk som du vill koppla från. I följande exempel används dmesg för att filtrera på SCSI-diskar:

```bash
dmesg | grep SCSI
```

Utdata ser ut ungefär så här:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Här är *SDC* den disk som vi vill koppla från. Du bör också ta tag i diskens UUID.

```bash
sudo -i blkid
```

Utdata ser ut ungefär som i följande exempel:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Redigera */etc/fstab* -filen för att ta bort referenser till disken. 

> [!NOTE]
> Felaktig redigering av **/etc/fstab** -filen kan leda till ett system som inte kan startas. Om du vill veta mer om hur du redigerar den här filen i distributionens dokumentation. Vi rekommenderar också att du skapar en säkerhets kopia av/etc/fstab-filen innan du redigerar.

Öppna */etc/fstab* -filen i en text redigerare enligt följande:

```bash
sudo vi /etc/fstab
```

I det här exemplet måste följande rad tas bort från */etc/fstab* -filen:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Använd `umount` för att demontera disken. I följande exempel demonteras */dev/sdc1* -partitionen från */datadrive* -monterings punkten:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Koppla från en datadisk med Azure CLI 

Det här exemplet kopplar bort *myDataDisk* -disken från den virtuella datorn med namnet *myVM* i *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.


## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. På den vänstra menyn väljer du **Virtual Machines**.
2. Välj den virtuella dator som innehåller den datadisk som du vill koppla från och klicka på **stoppa** för att frigöra den virtuella datorn.
3. I fönstret virtuell dator väljer du **diskar**.
4. Överst i fönstret **diskar** väljer du **Redigera**.
5. I rutan **diskar** längst till höger på den datadisk som du vill koppla från, klickar du på knappen ![koppla bort knapp bild](./media/detach-disk/detach.png) koppla från.
5. När disken har tagits bort klickar du på Spara längst upp i fönstret.
6. I fönstret virtuell dator klickar du på **Översikt** och sedan på **Start** -knappen längst upp i fönstret för att starta om den virtuella datorn.

Disken finns kvar i lagrings utrymmet men är inte längre kopplad till en virtuell dator.



## <a name="next-steps"></a>Nästa steg
Om du vill återanvända data disken kan du bara [ansluta den till en annan virtuell dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


---
title: Koppla bort en datadisk från en Virtuell Linux-dator – Azure
description: Lär dig att ta bort en datadisk från en virtuell dator i Azure med Azure CLI eller Azure-portalen.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74036375"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Så här kopplar du bort en datadisk från en virtuell Linux-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men tar inte bort den från lagring. I den här artikeln arbetar vi med en Ubuntu LTS 16.04 distribution. Om du använder en annan distribution kan instruktionerna för att avmontera disken vara olika.

> [!WARNING]
> Om du kopplar från en disk tas den inte bort automatiskt. Om du prenumererar på Premium-lagring fortsätter du att debiteras lagringsavgifter för disken. Mer information finns i [Priser och fakturering när du använder Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Anslut till den virtuella datorn för att avmontera disken

Innan du kan koppla bort disken med cli eller portalen måste du avmontera disken och ta bort referenser till om från fstab-filen.

Anslut till den virtuella datorn. I det här exemplet är den offentliga IP-adressen för den virtuella datorn *10.0.1.4* med användarnamnet *azureuser:* 

```bash
ssh azureuser@10.0.1.4
```

Leta först reda på den datadisk som du vill koppla från. I följande exempel används dmesg för att filtrera på SCSI-diskar:

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

Här är *SDC* den disk som vi vill koppla bort. Du bör också ta UUID på disken.

```bash
sudo -i blkid
```

Utdata liknar följande exempel:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Redigera filen */etc/fstab* för att ta bort referenser till disken. 

> [!NOTE]
> Felaktig redigering av **/etc/fstab-filen** kan resultera i ett system som inte kan booterbara. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Det rekommenderas också att en säkerhetskopia av filen /etc/fstab skapas före redigering.

Öppna filen */etc/fstab* i en textredigerare enligt följande:

```bash
sudo vi /etc/fstab
```

I det här exemplet måste följande rad tas bort från filen */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Används `umount` för att avmontera disken. I följande exempel avmonteras *partitionen /dev/sdc1* från monteringspunkten */datadrive:*

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Koppla bort en datadisk med Azure CLI 

Det här exemplet kopplar bort *myDataDisk-disken* från VM med namnet *myVM* i *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disken lagras men är inte längre ansluten till en virtuell dator.


## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. Välj **Virtuella datorer**på den vänstra menyn .
2. Välj den virtuella datorn som har den datadisk som du vill koppla bort och klicka på **Stoppa** för att frigöra den virtuella datorn.
3. Välj **Diskar**i fönstret Virtuell dator .
4. Välj **Redigera**högst upp i fönstret **Diskar** .
5. Klicka **på** knappen Koppla ![](./media/detach-disk/detach.png) från knappen Koppla från längst till höger på den datadisk som du vill koppla från i fönstret Diskar.
5. När disken har tagits bort klickar du på Spara högst upp i fönstret.
6. Klicka på **Översikt** i fönstret Virtuell dator och klicka sedan på **Start-knappen** högst upp i fönstret för att starta om den virtuella datorn.

Disken lagras men är inte längre ansluten till en virtuell dator.



## <a name="next-steps"></a>Nästa steg
Om du vill återanvända datadisken kan du bara [koppla den till en annan virtuell dator.](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


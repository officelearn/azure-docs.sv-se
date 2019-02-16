---
title: Koppla ifrån en datadisk från en Linux VM – Azure | Microsoft Docs
description: Lär dig att koppla ifrån en datadisk från en virtuell dator i Azure med hjälp av Azure CLI eller Azure-portalen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: b6fda5d16b3f694cb1d0a1a9407cda5c2a73fcbc
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330638"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Hur du koppla ifrån en datadisk från en Linux-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men tas inte bort från storage. I den här artikeln har arbetar vi med ett Ubuntu LTS, 16.04-distribution. Om du använder en annan distributionsplats kan anvisningarna för att demontera disken vara annorlunda.

> [!WARNING]
> Om du koppla bort en disk som den inte tas bort automatiskt. Om du har prenumererat på Premium-lagring, fortsätter att kosta lagringsavgifter för disken. Mer information finns i [priser och fakturering när du använder Premiumlagring](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Ansluta till den virtuella datorn att demontera disken

Innan du kan koppla bort disken med CLI eller portalen måste du demontera disken och tagit bort referenser till om från din fstab-filen.

Anslut till den virtuella datorn. I det här exemplet, offentliga IP-adressen för den virtuella datorn är *is 10.0.1.4* med användarnamnet *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Ta först reda datadisken som du vill koppla från. I följande exempel används dmesg för att filtrera på SCSI-diskar:

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

Här kan *sdc* är den disk som vi vill koppla från. Du bör också hämta diskens UUID.

```bash
sudo -i blkid
```

Utdata ser ut ungefär så här:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Redigera den */etc/fstab* filen för att ta bort referenser till disken. 

> [!NOTE]
> Felaktigt redigerar den **/etc/fstab** filen kan resultera i ett system som inte kan startas. Om du är osäker, finns det distribution dokumentationen för information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av/etc/fstab-filen har skapats innan du redigerar.

Öppna den */etc/fstab* filen i en textredigerare på följande sätt:

```bash
sudo vi /etc/fstab
```

I det här exemplet följande rad måste tas bort från den */etc/fstab* fil:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Använd `umount` demontera disken. I följande exempel demonteras den */dev/sdc1* partition från den */datadrive* monteringspunkt:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Koppla ifrån en datadisk med hjälp av Azure CLI 

Det här exemplet kopplar från den *myDataDisk* disk från virtuell dator med namnet *myVM* i *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disken kvar i lagring, men är inte längre kopplad till en virtuell dator.


## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. I den vänstra menyn väljer du **virtuella datorer**.
2. Välj den virtuella dator som har datadisken som du vill koppla från och klickar på **stoppa** att frigöra den virtuella datorn.
3. I den virtuella dator i rutan Välj **diskar**.
4. Överst på den **diskar** väljer **redigera**.
5. I den **diskar** i fönstret till höger i datadisken som du vill koppla från, klickar du på den ![koppla från knappbild](./media/detach-disk/detach.png) koppla från knappen.
5. När disken har tagits bort, klickar du på Spara överst i fönstret.
6. I den virtuella dator i rutan klickar du på **översikt** och klicka sedan på den **starta** längst upp i fönstret för att starta om den virtuella datorn.

Disken kvar i lagring, men är inte längre kopplad till en virtuell dator.



## <a name="next-steps"></a>Nästa steg
Om du vill återanvända datadisken kan du enkelt [koppla den till en annan virtuell dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


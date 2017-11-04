---
title: Hantera Azure-diskarna med Azure CLI | Microsoft Docs
description: "Självstudiekurs – hantera Azure-diskarna med Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 882446ba32252490e27056c7c5c9a8f755e26ee6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Hantera Azure-diskarna med Azure CLI

Virtuella Azure-datorer använder diskar för att lagra virtuella datorer operativsystem, program och data. När du skapar en virtuell dator är det viktigt att välja en diskstorleken och konfiguration som är lämpligt att förväntade arbetsbelastningen. Den här kursen ingår distribuerar och hanterar Virtuella diskar. Du lär dig mer om:

> [!div class="checklist"]
> * OS-diskar och tillfällig diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Koppla och förbereda datadiskar
> * Ändra storlek på diskar
> * Diskbilder


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Standard Azure-diskar

När en virtuell Azure-dator har skapats är automatiskt två diskar kopplade till den virtuella datorn. 

**Operativsystemdisken** -drift systemdiskar kan storleksändras upp till 1 terabyte och är värd för operativsystemet för virtuella datorer. OS-disken är märkt */dev/sda* som standard. Disken cachelagring konfigurationen av OS-disken har optimerats för OS-prestanda. På grund av den här konfigurationen, OS-disk **bör inte** värd för program eller data. Använd datadiskar som beskrivs senare i den här artikeln för program och data. 

**Diskutrymme** -tillfälliga diskar använder ett SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporär diskar har hög performant och kan användas för åtgärder som till exempel temporär databearbetning. Om den virtuella datorn flyttas till en ny värd bort data som lagrats på en tillfällig disk. Storleken på den tillfälliga disken bestäms av VM-storlek. Tillfällig diskar är märkta */dev/sdb* och har en monteringspunkt för */mnt*.

### <a name="temporary-disk-sizes"></a>Tillfällig diskstorlekar

| Typ | VM-storlek | Maxstorlek för temporär disk (GB) |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A och D-serien | 800 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 800 |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md) | D och G serien | 6144 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md) | Serie L | 5630 |
| [GPU](sizes-gpu.md) | N serien | 1440 |
| [Hög prestanda](sizes-hpc.md) | A och H serien | 2000 |

## <a name="azure-data-disks"></a>Azure datadiskar

Ytterligare datadiskar kan läggas till för att installera program och lagra data. Datadiskar som ska användas i en situation där varaktiga och känslig datalagring önskas. Varje datadisk har en maximal kapacitet för 1 terabyte. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator. För varje VM-core kan två diskar kopplas. 

### <a name="max-data-disks-per-vm"></a>Maximalt antal datadiskar per VM

| Typ | VM-storlek | Maximalt antal datadiskar per VM |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A och D-serien | 32 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 32 |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md) | D och G serien | 64 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md) | Serie L | 64 |
| [GPU](sizes-gpu.md) | N serien | 48 |
| [Hög prestanda](sizes-hpc.md) | A och H serien | 32 |

## <a name="vm-disk-types"></a>VM-disktyper

Azure tillhandahåller två typer av disk.

### <a name="standard-disk"></a>Standard disk

Standard Storage stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar är idealiskt för ett kostnadseffektivt dev och testa arbetsbelastning.

### <a name="premium-disk"></a>Premium-disk

Premiumdiskar backas upp av SSD-baserad hög prestanda, låg latens disk. Perfekt för virtuella datorer som kör produktion arbetsbelastning. Premium-lagring stöder DS-serien, DSv2-serien GS-serien och FS-serien virtuella datorer. Premiumdiskar finns i tre olika typer (P10 P20, P30) och storleken på disken fastställer typ av disk. När du väljer, avrundat diskstorleken värdet till nästa typen. Om diskens storlek är mindre än 128 GB är disktypen P10. Om diskens storlek är mellan 129 och 512 GB, är en P20. Något över 512 GB storleken är en P30.

### <a name="premium-disk-performance"></a>Premium-diskprestanda

|Premium-lagring disktyp | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Diskens storlek (avrunda uppåt) | 128 GB | 512 GB | 1 024 GB (1 TB) |
| Högsta IOPS per disk | 500 | 2,300 | 5,000 |
Dataflöde per disk | 100 MB/s | 150 MB/s | 200 MB/s |

När tabellen ovan identifierar högsta IOPS per disk, kan en högre nivå av prestanda uppnås genom striping flera datadiskar. Exempelvis kan en virtuell dator Standard_GS5 uppnå högst 80 000 IOPS. Detaljerad information om högsta IOPS per VM finns [Linux VM-storlekar](sizes.md).

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Datadiskar kan skapas och fäst vid VM skapandet eller till en befintlig virtuell dator.

### <a name="attach-disk-at-vm-creation"></a>Bifoga disken på Skapa en virtuell dator

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Skapa en virtuell dator med hjälp av [az vm skapa]( /cli/azure/vm#create) kommando. Den `--datadisk-sizes-gb` argument används för att ange att en annan disk skapas och ansluten till den virtuella datorn. Skapa och koppla mer än en disk, Använd en mellanslags-avgränsad lista med värden för disk-storlek. I följande exempel skapas en virtuell dator med två diskar, både 128 GB. Eftersom storlekar för diskar är 128 GB, diskarna båda är konfigurerade som P10s som ger högsta 500 IOPS per disk.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Koppla disk till en befintlig virtuell dator

Skapa och koppla en ny disk till en befintlig virtuell dator, Använd den [az vm disk bifoga](/cli/azure/vm/disk#attach) kommando. I följande exempel skapar en premium disk, 128 GB i storlek, och kopplar den till den virtuella datorn skapas i det sista steget.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Förbereda datadiskar

När en disk har kopplats till den virtuella datorn, måste operativsystemet konfigureras för att använda disken. I följande exempel visar hur du manuellt konfigurera en disk. Den här processen kan också automatiseras med hjälp av molnet initiering, som beskrivs i en [senare kursen](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Manuell konfiguration

Skapa en SSH-anslutning med den virtuella datorn. Ersätt den IP-adressen med offentliga IP-Adressen för den virtuella datorn.

```azurecli-interactive 
ssh 52.174.34.95
```

Partitionera disk med `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Skriva ett filsystem till partition med hjälp av den `mkfs` kommando.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montera den nya disken så att den är tillgänglig i operativsystemet.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Disken kan nu nås via de *datadrive* monteringspunkt, som kan verifieras genom att köra den `df -h` kommando. 

```bash
df -h
```

Utdata visar den nya enheten monteras på */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

För att säkerställa att enheten är på nytt efter en omstart, måste den läggas till i */etc/fstab* fil. Gör du genom att hämta UUID för disken med den `blkid` verktyget.

```bash
sudo -i blkid
```

Utdata visar UUID för enheten, `/dev/sdc1` i det här fallet.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Lägga till en rad som liknar följande för att den */etc/fstab* fil. Även Observera att skriva barriärer kan inaktiveras med *barriären = 0*, den här konfigurationen kan förbättra diskprestanda. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Nu när disken har konfigurerats, Stäng SSH-session.

```bash
exit
```

## <a name="resize-vm-disk"></a>Ändra storlek på virtuell disk

När en virtuell dator har distribuerats, kan operativsystemdisken eller eventuella anslutna hårddiskar ökas i storlek. Det är bra att öka storleken på en disk när du behöver mer lagringsutrymme eller högre prestanda (P10, P20, P30). Observera att diskar inte kan minskas i storlek.

Innan du ökar diskstorleken krävs Id eller diskens namn. Använd den [az Disklista](/cli/azure/disk#az_disk_list) kommando för att returnera alla diskar i en resursgrupp. Anteckna namnet på disk som du vill ändra storlek på.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

Den virtuella datorn måste också frigöras. Använd den [az vm frigöra]( /cli/azure/vm#deallocate) kommando för att stoppa och ta bort den virtuella datorn.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Använd den [az disk uppdatering](/cli/azure/vm/disk#update) kommando för att ändra storlek på disken. Det här exemplet ändrar storlek på en disk med namnet *myDataDisk* till 1 terabyte.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Starta den virtuella datorn när åtgärden Ändra storlek har slutförts.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Om du har ändrat storlek operativsystemdisk utökas automatiskt partitionen. Om du har ändrat storlek en datadisk, måste alla aktuella partitioner som ska expanderas i operativsystemet för virtuella datorer.

## <a name="snapshot-azure-disks"></a>Ögonblicksbild Azure-diskar

Ta en ögonblicksbild av disk skapar en skrivskyddad, men i tidpunkt kopia av disken. Azure VM ögonblicksbilder är användbara för att snabbt spara tillståndet för en virtuell dator innan du gör ändringar i konfigurationen. I den händelse att konfigurationsändringarna som visar sig vara oönskade, kan tillstånd för virtuell dator återställas med hjälp av ögonblicksbilden. När en virtuell dator har mer än en disk, tas en ögonblicksbild för varje disk oberoende av de andra. För att programmet du konsekvent säkerhetskopieringar bör stoppa den virtuella datorn innan du tar diskbilder. Du kan också använda den [Azure Backup-tjänsten](/azure/backup/), vilket gör att du utför automatiserade säkerhetskopieringar medan den virtuella datorn körs.

### <a name="create-snapshot"></a>Skapa en ögonblicksbild

Innan du skapar en ögonblicksbild av en virtuell dator disk krävs-Id eller diskens namn. Använd den [az vm visa](https://docs.microsoft.com/en-us/cli/azure/vm#az_vm_show) kommando för att returnera disk-id. I det här exemplet lagras disk-id i en variabel så att den kan användas i ett senare steg.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Nu när du har id för den virtuella disken skapar följande kommando en ögonblicksbild av disken.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Skapa disk från en ögonblicksbild

Den här ögonblicksbilden kan sedan konverteras till en disk som kan användas för att återskapa den virtuella datorn.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Återställa en virtuell dator från en ögonblicksbild

Ta bort den befintliga virtuella datorn för att demonstrera återställning av virtuell dator. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Skapa en ny virtuell dator från disken ögonblicksbild.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Återanslut datadisk

Alla datadiskarna måste kopplas till den virtuella datorn.

Först söka efter data disk namn med den [az Disklista](https://docs.microsoft.com/cli/azure/disk#az_disk_list) kommando. Det här exemplet placerar diskens namn i en variabel med namnet *datadisk*, som används i nästa steg.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Använd den [az vm disk bifoga](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) kommando för att ansluta disken.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om Virtuella diskar ämnen som:

> [!div class="checklist"]
> * OS-diskar och tillfällig diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Koppla och förbereda datadiskar
> * Ändra storlek på diskar
> * Diskbilder

Gå vidare till nästa kurs mer information om hur du automatiserar VM-konfiguration.

> [!div class="nextstepaction"]
> [Automatisera konfiguration av virtuella datorer](./tutorial-automate-vm-deployment.md)

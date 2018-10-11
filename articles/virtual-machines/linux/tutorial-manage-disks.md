---
title: Självstudier – Hantera Azure-diskar med Azure CLI | Microsoft Docs
description: I den här självstudien lär du dig hur du använder Azure CLI för att skapa och hantera Azure-diskar för virtuella datorer
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 04fad24b17d7f74211deae53c0d044f2049660f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978326"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Självstudiekurs – hantera Azure-diskar med Azure CLI

Virtuella Azure-datorer (VM) använder diskar för att lagra de virtuella datorernas operativsystem, program och data. När du skapar en virtuell dator är det viktigt att du väljer en diskstorlek och konfiguration som motsvarar den förväntade arbetsbelastningen. Den här kursen visar hur du distribuerar och hanterar diskar för virtuella datorer. Du får lära dig om:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar
> * Ändrar storlek på diskar
> * Ögonblicksbilder

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="default-azure-disks"></a>Azure-standarddiskar

När en virtuell Azure-dator skapas kopplas två diskar automatiskt till den virtuella datorn.

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 2 TB och innehåller de virtuella datorernas operativsystem. Operativsystemdisken kallas som standard */dev/sda*. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. Därför bör OS-disken **inte** användas för program eller data. För program och data använder du datadiskar som beskrivs senare i den här guiden.

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporära diskar har höga prestanda och kan användas för åtgärder som till exempel tillfällig databearbetning. Men om den virtuella datorn flyttas till en ny värd tas alla data som är lagrade på den temporära disken bort. Storleken på den temporära disken bestäms av VM-storleken. Temporära diskar kallas */dev/sdb* och har monteringspunkten */mnt*.

### <a name="temporary-disk-sizes"></a>Storlekar för temporära diskar

| Typ | Normala storlekar | Maxstorlek för temporär disk (GiB) |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A-, B- och D-serien | 1600 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 576 |
| [Minnesoptimerad](sizes-memory.md) | D-, E-, G- och M-serien | 6144 |
| [Lagringsoptimerad](sizes-storage.md) | L-serien | 5630 |
| [GPU](sizes-gpu.md) | N-serien | 1440 |
| [Höga prestanda](sizes-hpc.md) | A- och H-serien | 2000 |

## <a name="azure-data-disks"></a>Azure-datadiskar

Du kan lägga till ytterligare datadiskar om du behöver installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Varje datadisk har en maxkapacitet på 4 TB. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator. Två datadiskar kan kopplas för varje VM vCPU.

### <a name="max-data-disks-per-vm"></a>Maximalt antal datadiskar per VM

| Typ | Storlek på virtuell dator | Maximalt antal datadiskar per VM |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A-, B- och D-serien | 64 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 64 |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md) | D-, E- och G-serien | 64 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md) | L-serien | 64 |
| [GPU](sizes-gpu.md) | N-serien | 64 |
| [Höga prestanda](sizes-hpc.md) | A- och H-serien | 64 |

## <a name="vm-disk-types"></a>VM-disktyper

Azure tillhandahåller två disktyper.

### <a name="standard-disk"></a>Standarddiskar

Standard Storage stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

### <a name="premium-disk"></a>Premiumdiskar

Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien GS-serien och FS-serien. När du väljer en diskstorlek, avrundas värdet uppåt till nästa typ. Om diskstorleken till exempel är mindre än 128 GB är disktypen P10. Om disktypen är mellan 129 GB och 512 GB är storleken P20. Över 512 GB är storleken en P30.

### <a name="premium-disk-performance"></a>Premiumdiskprestanda

|Premium Storage-disktyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Diskens storlek (avrundas uppåt) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Högsta IOPS per disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Dataflöde per disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. En Standard_GS5 virtuell dator kan till exempel uppnå maximalt 80 000 IOPS. Mer information om högsta IOPS per VM finns i [Storlekar för virtuella Linux-datorer](sizes.md).

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Datadiskar kan skapas och kopplas när den virtuella datorn skapas eller kopplas till en befintlig virtuell dator.

### <a name="attach-disk-at-vm-creation"></a>Koppla diskar när den virtuella datorn skapas

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az-vm-create). Följande exempel skapar virtuell dator med namnet *myVM*, lägger till ett användarkonto med namnet *azureuser* och genererar SSH-nycklar om de inte redan finns. Argumentet `--datadisk-sizes-gb` används för att ange att ytterligare en disk ska skapas och kopplas till den virtuella datorn. Om du vill skapa och koppla fler än en disk använder du en lista med diskstorlekar separerade med mellanslag. I följande exempel skapas en virtuell dator med två datadiskar som båda är 128 GB. Eftersom diskstorleken är 128 GB konfigureras båda diskarna som P10, vilket ger maximalt 500 IOPS per disk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Koppla diskar till befintliga virtuella datorer

Skapa och koppla en ny disk till en befintlig virtuell dator med kommandot [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). Följande exempel skapar en premiumdisk på 128 gigabyte och kopplar den till den virtuella dator som skapades i föregående steg.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Förbereda datadiskar

När en disk har kopplats till den virtuella datorn måste operativsystemet konfigureras för att använda disken. I följande exempel visas hur du manuellt konfigurerar en disk. Den här processen kan även automatiseras med cloud-init som beskrivs i en [senare självstudiekurs](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Manuell konfiguration

Skapa en SSH-anslutning med den virtuella datorn. Ersätt exempel-IP-adressen med den offentliga IP-adressen för den virtuella datorn.

```azurecli-interactive
ssh azureuser@52.174.34.95
```

Partitionera disken med `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Skapa ett filsystem på partitionen med kommandot `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montera den nya disken så den är tillgänglig i operativsystemet.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Disken kan nu kommas åt via monteringspunkten *datadrive* som kan verifieras med kommandot `df -h`.

```bash
df -h
```

Utdata visar att den nya disken är monterad på */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

För att säkerställa att disken monteras efter en omstart måste den läggas till i filen */etc/fstab*. För att göra detta hämtar du diskens UUID med verktyget `blkid`.

```bash
sudo -i blkid
```

Utdata visar diskens UUID, i det här fallet `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Lägg till en rad som liknar denna i filen */etc/fstab*.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Disken har konfigurerats, stäng SSH-sessionen.

```bash
exit
```

## <a name="resize-vm-disk"></a>Ändra storlek på VM-disk

När en virtuell dator distribuerats går det att öka storleken på operativsystemdisken och kopplade datadiskar. Att öka diskstorleken är ett bra alternativ när du behöver mer lagringsutrymme eller högre prestanda (till exempel P10, P20, P30). Det går inte att göra diskar mindre.

Innan du ökar storleken på en disk behöver du dess ID eller namn. Använd kommandot [az disk list](/cli/azure/disk#az-disk-list) som returnerar alla diskar i en resursgrupp. Anteckna namnet på disken du vill öka storlek på.

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

Den virtuella datorn måste frigöras för att kunna utföra förändringen. Använd kommandot [az vm deallocate](/cli/azure/vm#az-vm-deallocate) för att stoppa och frigöra den virtuella datorn.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Använd kommandot [az disk update](/cli/azure/vm/disk#az-vm-disk-update) för att ändra diskens storlek. Det här exemplet ändrar storleken på en disk med namnet *myDataDisk* till 1 terabyte.

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Starta den virtuella datorn när du utfört storleksändringen.

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

Om du har ändrat storlek på operativsystemdisken utökas partitionen automatiskt. Om du har ändrat storlek på en datadisk måste alla befintliga partitioner utökas i den virtuella datorns operativsystem.

## <a name="snapshot-azure-disks"></a>Ögonblicksbilder av Azure-diskar

När du tar en ögonblicksbild skapar Azure en skrivskyddad kopia av disken vid en viss tidpunkt. Ögonblicksbilder av virtuella Azure-datorer är användbara för att snabbt spara tillståndet hos en virtuell dator innan ändringar i konfigurationen. Om konfigurationsändringarna inte fungerar som de ska kan den virtuella datorn återställas med ögonblicksbilden. När en virtuell dator har mer än en disk tas en ögonblicksbild av varje disk oberoende av de andra. Om programkonsekventa säkerhetskopior krävs bör den virtuella datorn stoppas innan ögonblicksbilder tas. Ett annat alternativ är att använda [Azure Backup-tjänsten](/azure/backup/) som ger möjlighet till automatiserade säkerhetskopior medan den virtuella datorn körs.

### <a name="create-snapshot"></a>Skapa en ögonblicksbild

Du behöver diskens ID eller namn för att skapa en ögonblicksbild av en virtuell dator. Använd kommandot [az vm show](/cli/azure/vm#az-vm-show) för att hämta diskens ID. I det här exemplet sparas diskens ID i en variabel så det kan användas i ett senare steg.

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

När du har diskens ID skapar du en ögonblicksbild av disken med följande kommando.

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Skapa en disk från en ögonblicksbild

Ögonblicksbilden kan omvandlas till en disk som kan användas för att återskapa den virtuella datorn.

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Återställa en virtuell dator från en ögonblicksbild

För att demonstrera återställning av en virtuell dator tar du bort den virtuella datorn.

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Skapa en ny virtuell dator från ögonblicksbilddisken.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Koppla datadisken

Alla datadiskar måste kopplas till den virtuella datorn.

Ta först reda på datadiskens namn med kommandot [az disk list](/cli/azure/disk#az-disk-list). Det här exemplet sparar diskens namn i variabeln *datadisk* som används i nästa steg.

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Använd kommandot [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) för att koppla disken till den virtuella datorn.

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig mer om VM-diskar, till exempel:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar
> * Ändrar storlek på diskar
> * Ögonblicksbilder

Gå vidare till nästa självstudie om du vill lära dig hur du automatiserar konfigurationen av virtuella datorer.

> [!div class="nextstepaction"]
> [Automatisera konfiguration av virtuella datorer](./tutorial-automate-vm-deployment.md)

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
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: da70b77edeb483cae0e74400e739f018f78d0993
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370813"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Självstudiekurs – hantera Azure-diskar med Azure CLI

Virtuella Azure-datorer (VM) använder diskar för att lagra de virtuella datorernas operativsystem, program och data. När du skapar en virtuell dator, är det viktigt att välja en diskstorlek och konfiguration som motsvarar den förväntade arbetsbelastningen. Den här kursen visar hur du distribuerar och hanterar diskar för virtuella datorer. Du får lära dig om:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar
> * Ändrar storlek på diskar
> * Ögonblicksbilder


## <a name="default-azure-disks"></a>Azure-standarddiskar

När en virtuell Azure-dator skapas kopplas två diskar automatiskt till den virtuella datorn.

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 2 TB och innehåller de virtuella datorernas operativsystem. Operativsystemdisken kallas som standard */dev/sda*. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. Därför bör OS-disken **inte** användas för program eller data. För program och data använder du datadiskar som beskrivs senare i den här guiden.

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporära diskar har höga prestanda och kan användas för åtgärder som till exempel tillfällig databearbetning. Men om den virtuella datorn flyttas till en ny värd tas alla data som är lagrade på den temporära disken bort. Storleken på den temporära disken bestäms av VM-storleken. Temporära diskar kallas */dev/sdb* och har monteringspunkten */mnt*.


## <a name="azure-data-disks"></a>Azure-datadiskar

Du kan lägga till ytterligare datadiskar om du behöver installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Varje datadisk har en maxkapacitet på 4 TB. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator. Fyra datadiskar kan kopplas för varje VM-vCPU.


## <a name="vm-disk-types"></a>VM-disktyper

Azure har två typer av diskar, Standard och Premium.

### <a name="standard-disk"></a>Standarddiskar

Standard Storage stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

### <a name="premium-disk"></a>Premiumdiskar

Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien GS-serien och FS-serien. När du väljer en diskstorlek, avrundas värdet uppåt till nästa typ. Om diskstorleken till exempel är mindre än 128 GB är disktypen P10. Om disktypen är mellan 129 GB och 512 GB är storleken P20. Över 512 GB är storleken en P30.

### <a name="premium-disk-performance"></a>Premiumdiskprestanda
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. En Standard_GS5 virtuell dator kan till exempel uppnå maximalt 80 000 IOPS. Mer information om högsta IOPS per VM finns i [Storlekar för virtuella Linux-datorer](sizes.md).

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell, Välj **prova** från det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

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


Skapa en SSH-anslutning med den virtuella datorn. Ersätt exempel-IP-adressen med den offentliga IP-adressen för den virtuella datorn.

```azurecli-interactive
ssh 10.101.10.10
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

## <a name="take-a-disk-snapshot"></a>Ta en ögonblicksbild

När du tar en ögonblicksbild skapar Azure en skrivskyddad kopia av disken vid en viss tidpunkt. Ögonblicksbilder av virtuella Azure-datorer är användbara för att snabbt spara tillståndet hos en virtuell dator innan ändringar i konfigurationen. Om det uppstår ett problem eller fel kan den virtuella datorn återställas med en ögonblicksbild. När en virtuell dator har mer än en disk tas en ögonblicksbild av varje disk oberoende av de andra. Om programkonsekventa säkerhetskopior krävs bör den virtuella datorn stoppas innan ögonblicksbilder tas. Ett annat alternativ är att använda [Azure Backup-tjänsten](/azure/backup/) som ger möjlighet till automatiserade säkerhetskopior medan den virtuella datorn körs.

### <a name="create-snapshot"></a>Skapa en ögonblicksbild

Du behöver diskens ID eller namn för att skapa en ögonblicksbild av en virtuell dator. Använd kommandot [az vm show](/cli/azure/vm#az-vm-show) för att hämta diskens ID. I det här exemplet sparas diskens ID i en variabel så det kan användas i ett senare steg.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

När du har diskens ID skapar du en ögonblicksbild av disken med följande kommando.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Skapa en disk från en ögonblicksbild

Ögonblicksbilden kan omvandlas till en disk som kan användas för att återskapa den virtuella datorn.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Återställa en virtuell dator från en ögonblicksbild

För att demonstrera återställning av en virtuell dator tar du bort den virtuella datorn.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
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
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Använd kommandot [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) för att koppla disken till den virtuella datorn.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --disk $datadisk
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

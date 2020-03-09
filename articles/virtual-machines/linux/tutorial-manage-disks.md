---
title: Självstudie – Hantera Azure-diskar med Azure CLI
description: I den här självstudien får du lära dig hur du använder Azure CLI för att skapa och hantera Azure-diskar för virtuella datorer
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: dc987fa1a3476b81b198726350d56333b53c795f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385009"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Självstudie – Hantera Azure-diskar med Azure CLI

Virtuella Azure-datorer använder diskar för att lagra operativ systemet, program och data. När du skapar en virtuell dator är det viktigt att välja en disk storlek och konfiguration som är lämplig för den förväntade arbets belastningen. Den här självstudien visar hur du distribuerar och hanterar virtuella dator diskar. Du får lära dig mer om:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Data diskar
> * Standard-och Premium-diskar
> * Disk prestanda
> * Ansluta och förbereda data diskar
> * Ändra storlek på diskar
> * Ögonblicks bilder av disk


## <a name="default-azure-disks"></a>Standard Azure-diskar

När en virtuell Azure-dator skapas kopplas två diskar automatiskt till den virtuella datorn.

Operativ system **disk** -operativ system diskar kan vara upp till 2 TB och vara värd för operativ systemet för virtuella datorer. OS-disken är märkt */dev/SDA* som standard. Konfigurationen av disk-cachen för OS-disken är optimerad för operativ systemets prestanda. På grund av den här konfigurationen bör OS-disken **inte** användas för program eller data. För program och data använder du data diskar som beskrivs senare i den här självstudien.

**Temporär disk** – temporära diskar använder en solid-state-enhet som finns på samma Azure-värd som den virtuella datorn. Temporära diskar är mycket presterande och kan användas för åtgärder som till exempel tillfällig data bearbetning. Men om den virtuella datorn flyttas till en ny värd tas alla data som lagras på en temporär disk bort. Storleken på den temporära disken bestäms av storleken på den virtuella datorn. Temporära diskar är märkta med */dev/SDB* och har en monterings punkt på */mnt*.

## <a name="azure-data-disks"></a>Azure-datadiskar

Om du vill installera program och lagra data kan du lägga till ytterligare data diskar. Data diskar bör användas i alla situationer där varaktig och effektiv data lagring önskas. Storleken på den virtuella datorn avgör hur många data diskar som kan kopplas till en virtuell dator.

## <a name="vm-disk-types"></a>VM disk typer

Azure tillhandahåller två typer av diskar, standard och Premium.

### <a name="standard-disk"></a>Standard disk

Standard lagring backas upp av hård diskar och levererar kostnads effektiv lagring samtidigt som du fortfarande genomför. Standard diskar är idealiska för kostnads effektiv utveckling och testning av arbets belastningar.

### <a name="premium-disk"></a>Premium disk

Premium diskar backas upp av SSD-baserade diskar med låg latens med hög prestanda. Perfekt för virtuella datorer som kör produktions arbets belastning. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien, GS-serien och FS-serien. När du väljer en disk storlek avrundas värdet uppåt till nästa typ. Om disk storleken till exempel är mindre än 128 GB är disk typen P10. Om disk storleken är mellan 129 GB och 512 GB är storleken en P20. Över, 512 GB är storleken en P30.

### <a name="premium-disk-performance"></a>Prestanda för Premium-diskar
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Tabellen ovan anger högsta IOPS per disk, men en högre prestanda nivå kan uppnås genom att Stripa flera data diskar. En Standard_GS5 VM kan till exempel uppnå högst 80 000 IOPS. Detaljerad information om högsta IOPS per virtuell dator finns i [storlekar för virtuella Linux-datorer](sizes.md).

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och kon figurer ATS för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat flik i webbläsaren genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/bash). Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på RETUR för att köra den.

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Data diskar kan skapas och bifogas när en virtuell dator skapas eller till en befintlig virtuell dator.

### <a name="attach-disk-at-vm-creation"></a>Koppla disk vid skapande av virtuell dator

Skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group#az-group-create) .

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Skapa en virtuell dator med kommandot [AZ VM Create](/cli/azure/vm#az-vm-create) . Följande exempel skapar en virtuell dator med namnet *myVM*, lägger till ett användar konto med namnet *AZUREUSER*och genererar SSH-nycklar om de inte redan finns. Argumentet `--datadisk-sizes-gb` används för att ange att ytterligare en disk ska skapas och kopplas till den virtuella datorn. Om du vill skapa och koppla mer än en disk använder du en blankstegsavgränsad lista med disk storleks värden. I följande exempel skapas en virtuell dator med två data diskar, både 128 GB. Eftersom disk storlekarna är 128 GB är dessa diskar båda konfigurerade som P10s, vilket ger maximalt 500 IOPS per disk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Koppla disk till befintlig virtuell dator

Om du vill skapa och ansluta en ny disk till en befintlig virtuell dator använder du kommandot [AZ VM disk Attach](/cli/azure/vm/disk#az-vm-disk-attach) . I följande exempel skapas en Premium-disk, 128 gigabyte i storlek och bifogas den till den virtuella datorn som skapades i det sista steget.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Förbereda data diskar

När en disk har kopplats till den virtuella datorn måste operativ systemet konfigureras för att använda disken. I följande exempel visas hur du konfigurerar en disk manuellt. Den här processen kan också automatiseras med Cloud-Init, som beskrivs i en [senare självstudie](./tutorial-automate-vm-deployment.md).


Skapa en SSH-anslutning till den virtuella datorn. Ersätt IP-exemplet med den offentliga IP-adressen för den virtuella datorn.

```azurecli-interactive
ssh 10.101.10.10
```

Partitionera disken med `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Skriv ett fil system till partitionen med kommandot `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montera den nya disken så att den är tillgänglig i operativ systemet.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Disken kan nu nås via *datadrive* -monterings punkt, som kan verifieras genom att köra kommandot `df -h`.

```bash
df -h
```

Utdata visar den nya enhet som är monterad på */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

För att säkerställa att enheten monteras om efter en omstart måste den läggas till i */etc/fstab* -filen. Det gör du genom att hämta diskens UUID med `blkid`-verktyget.

```bash
sudo -i blkid
```

I utdata visas enhetens UUID, `/dev/sdc1` i det här fallet.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Lägg till en rad som liknar följande i */etc/fstab* -filen.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Stäng SSH-sessionen nu när disken har kon figurer ATS.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Ta en ögonblicks bild av disken

När du tar en ögonblicks bild av disken skapar Azure en skrivskyddad, tidpunkts kopia av disken. Ögonblicks bilder av virtuella Azure-datorer är användbara för att snabbt spara statusen för en virtuell dator innan du gör konfigurations ändringar. Om ett ärende eller fel inträffar kan den virtuella datorn återställas med en ögonblicks bild. När en virtuell dator har fler än en disk tas en ögonblicks bild av varje disk oberoende av de andra. Överväg att stoppa den virtuella datorn innan du tar ögonblicks bilder av program för att göra programkonsekventa säkerhets kopieringar. Du kan också använda [Azure Backup tjänsten](/azure/backup/), som gör att du kan utföra automatiserade säkerhets kopieringar medan den virtuella datorn körs.

### <a name="create-snapshot"></a>Skapa ögonblicks bild

Innan du skapar en ögonblicks bild av en virtuell dator måste du ange ett ID eller namnet på disken. Använd kommandot [AZ VM show](/cli/azure/vm#az-vm-show) för att returnera disk-ID: t. I det här exemplet lagras disk-ID i en variabel så att det kan användas i ett senare steg.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Nu när du har ID: t för den virtuella dator disken skapar följande kommando en ögonblicks bild av disken.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Skapa disk från ögonblicks bild

Den här ögonblicks bilden kan sedan konverteras till en disk som kan användas för att återskapa den virtuella datorn.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Återställ virtuell dator från ögonblicks bild

Ta bort den befintliga virtuella datorn för att demonstrera återställning av virtuell dator.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Skapa en ny virtuell dator från ögonblicks bild disken.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Återanslut datadisk

Alla data diskar måste kopplas till den virtuella datorn.

Ta först reda på data diskens namn med kommandot [AZ disk List](/cli/azure/disk#az-disk-list) . I det här exemplet placeras namnet på disken i en variabel med namnet *datadisk*, som används i nästa steg.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Använd kommandot [AZ VM disk Attach](/cli/azure/vm/disk#az-vm-disk-attach) för att ansluta disken.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om de virtuella dator diskarna, till exempel:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Data diskar
> * Standard-och Premium-diskar
> * Disk prestanda
> * Ansluta och förbereda data diskar
> * Ändra storlek på diskar
> * Ögonblicks bilder av disk

Gå vidare till nästa självstudie om du vill lära dig hur du automatiserar konfigurationen av virtuella datorer.

> [!div class="nextstepaction"]
> [Automatisera VM-konfiguration](./tutorial-automate-vm-deployment.md)

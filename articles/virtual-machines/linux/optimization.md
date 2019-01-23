---
title: Optimera din virtuella Linux-dator på Azure | Microsoft Docs
description: Lär dig tips optimering för att kontrollera att du har konfigurerat din Linux-VM för optimala prestanda på Azure
keywords: Linux-dator, VM linux ubuntu-dator
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.component: disks
ms.openlocfilehash: 6cccf31842825c3e4d50aa67165d19f8ac471695
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478169"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimera din virtuella Linux-dator på Azure
Det är enkelt att göra från kommandoraden eller från portalen att skapa en Linux-dator (VM). Den här självstudien Lär dig att se till att du har konfigurerat den för att optimera prestanda på Microsoft Azure-plattformen. Det här avsnittet använder en dator med Ubuntu Server, men du kan också skapa Linux virtuell dator med hjälp av [dina egna avbildningar som mallar](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet förutsätter att du redan har ett aktivt Azure-prenumeration ([kostnadsfria registreringsstegen](https://azure.microsoft.com/pricing/free-trial/)) och redan har etablerat en virtuell dator på Azure-prenumerationen. Se till att du har senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in på Azure-prenumerationen med [az-inloggning](/cli/azure/reference-index#az_login) innan du [skapa en virtuell dator](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS-Disk
När du skapar en Linux VM i Azure har två diskar som är kopplade till den. **/ dev/sda** är din OS-disk, **/dev/sdb** din temporär disk.  Använd inte den huvudsakliga OS-disken (**/dev/sda**) för något annat än operativsystemet som det är optimerat för snabb VM boot-tid och ger inte bra prestanda för dina arbetsbelastningar. Du vill koppla en eller flera diskar till den virtuella datorn ska hämta beständiga och optimerad lagring för dina data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Att lägga till diskar för storlek och mål
Baserat på virtuella datorstorlek, kan du lägga till upp till 16 ytterligare diskar på en A-serien, 32 diskar på en D-serien och 64 diskar på en G-serien machine - varje upp till 1 TB i storlek. Du kan lägga till extra diskar som behövs per dina utrymme och IOps-krav. Varje disk har ett mål för prestanda på 500 IOps för Standard-lagring och upp till 5000 IOps per disk för Premium Storage.  Läs mer om Premium-lagringsdiskar, [Premium Storage: Lagring med höga prestanda för virtuella Azure-datorer](../windows/premium-storage.md)

Att uppnå högsta IOps på Premium-lagringsdiskar där cacheinställningarna har ställts in på antingen **ReadOnly** eller **ingen**, måste du inaktivera **hinder** vid montering filsystem i Linux. Du behöver inte hinder eftersom skrivningar till Premium-lagringsresurs som backas upp diskar finns under cacheinställningarna.

* Om du använder **reiserFS**, inaktivera hinder med alternativet montera `barrier=none` (för att aktivera hinder, Använd `barrier=flush`)
* Om du använder **ext3/ext4**, inaktivera hinder med alternativet montera `barrier=0` (för att aktivera hinder, Använd `barrier=1`)
* Om du använder **XFS**, inaktivera hinder med alternativet montera `nobarrier` (Använd alternativet för att aktivera hinder `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Överväganden för ohanterade lagring-konto
Standardåtgärden när du skapar en virtuell dator med Azure CLI är att använda Azure Managed Disks.  De här diskarna hanteras av Azure-plattformen och kräver inte någon förberedelser eller plats för att lagra dem.  Ohanterade diskar kräver ett lagringskonto och har några ytterligare prestandaöverväganden.  Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md).  I följande avsnitt beskrivs prestandaöverväganden bara när du använder ohanterade diskar.  Igen, standard och rekommenderade lagringslösning är att använda hanterade diskar.

Om du skapar en virtuell dator med ohanterade diskar, se till att koppla diskar från storage-konton som finns i samma region som den virtuella datorn ska kontrollera nära varandra och minimerar svarstiden i nätverket.  Varje lagringskonto av standardtyp har högst 20 k IOps och en kapacitet på 500 TB storlek.  Den här gränsen fungerar till cirka 40 hårt belastat diskar både för OS-disken och eventuella datadiskar som du skapar. Det finns ingen högsta IOps-gräns för Premium Storage-konton, men det finns en storleksgräns för 32 TB. 

När hantera hög IOps arbetsbelastningar och du har valt standardlagring för diskarna kan behöva du dela upp diskarna över flera lagringskonton för att se till att du inte har nått 20 000 IOps-gränsen för lagringskonton av standardtyp. Den virtuella datorn kan innehålla en blandning av diskar från mellan olika lagringskonton och storage-kontotyper att uppnå optimala konfigurationen.
 

## <a name="your-vm-temporary-drive"></a>Den virtuella datorn tillfälligt enheten
Som standard när du skapar en virtuell dator, erbjuder Azure dig en OS-disk (**/dev/sda**) och en tillfällig disk (**/dev/sdb**).  Alla ytterligare diskar som du lägger till visa upp som **/dev/sdc**, **/dev/sdd**, **/dev/sde** och så vidare. Alla data på den temporära disken (**/dev/sdb**) är inte beständiga och kan gå förlorad om specifika händelser som virtuell dator vid storleksändring, ny distribution eller underhåll tvingar en omstart av den virtuella datorn.  Storlek och typ för den temporära disken är relaterat till virtuella datorstorlek som du har valt vid tidpunkten för distribution. Alla premium ändra storlek på virtuella datorer (DS, G och DS_V2-serien) den temporära enheten backas upp av en lokal SSD för att ytterligare prestanda med upp till 48 kB IOps. 

## <a name="linux-swap-file"></a>Växlingsfil för Linux
Om din Azure VM från en Ubuntu eller CoreOS-avbildning, kan du använda CustomData för att skicka en molnkonfiguration till cloud-init. Om du [ladda upp en anpassad Linux-avbildning](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som använder cloud-init kan du också konfigurera växling partitioner med cloud-init.

På Ubuntu Cloud-avbildningar, måste du använda cloud-init för att konfigurera swap-partitionen. Mer information finns i [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

För bilder utan stöd för cloud-init har VM-avbildningar som distribueras från Azure Marketplace en virtuell dator på en Linux-agenten som integreras med Operativsystemet. Den här agenten kan den virtuella datorn att interagera med olika Azure-tjänster. Om vi antar att du har distribuerat en standardiserad avbildning från Azure Marketplace, måste du göra följande för att konfigurera korrekt Linux växlingsfilens inställningar:

Leta upp och ändra två poster i den **/etc/waagent.conf** fil. De kontrollera förekomsten av en dedikerad växlingsfil och storleken på växlingsfilen. De parametrar som du vill ändra är `ResourceDisk.EnableSwap=N` och `ResourceDisk.SwapSizeMB=0` 

Ändra parametrarna för följande inställningar:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size i MB för att uppfylla dina behov} 

När du har gjort ändringen, måste du starta om waagent eller din Linux-VM för att återspegla dessa ändringar.  Du vet ändringarna har genomförts och en växlingsfil har skapats när du använder den `free` kommando för att visa ledigt utrymme. I följande exempel har en 512MB växlingsfil som skapas när du ändrar den **waagent.conf** fil:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/o-schemaläggning algoritm för Premium Storage
Med 2.6.18 Linux-kerneln, standard-i/o schemaläggning algoritmen har ändrats från tidsgräns till CFQ (helt fair queuing algoritmen). För direktåtkomst i/o-mönster finns det försumbar skillnaden i skillnader i prestanda mellan CFQ och tidsgräns.  Växla tillbaka till algoritmen NOOP eller tidsgräns kan SSD-baserade diskar där disk-i/o-mönster är främst sekventiella få bättre i/o-prestanda.

### <a name="view-the-current-io-scheduler"></a>Visa aktuella i/o-Schemaläggaren
Ange följande kommando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Du kan se följande utdata, vilket indikerar den aktuella scheduler.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/ dev/sda) i/o schemaläggning algoritmen
Använd följande kommandon:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Tillämpa den här inställningen för **/dev/sda** fristående är inte användbart. Ange alla datadiskar där sekventiella i/o dominerar i/o-mönster.  

Du bör se följande utdata, vilket betyder att **grub.cfg** har återskapats och som standard scheduler har uppdaterats till NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Red Hat distribution-familjen behöver du bara följande kommando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Med programvaru-RAID för att få högre jag / Ops
Om dina arbetsbelastningar kräver fler IOps än en enskild disk kan ge kan behöva du använda en programvara RAID-konfiguration av flera diskar. Eftersom Azure utför redan disk återhämtning i lokala fabric-lagret, kan du uppnå den högsta möjliga prestanda från en RAID-0 striping konfiguration.  Etablera och skapa diskar i Azure-miljön och koppla dem till din Linux-VM före partitionering, formatera och montera enheterna.  Mer information om hur du konfigurerar en RAID konfiguration av programvara på din Linux-VM i azure finns i den **[konfigurera programvaru-RAID på Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentet.

## <a name="next-steps"></a>Nästa steg
Kom ihåg att med alla optimering diskussioner som du behöver utföra tester före och efter varje ändring att mäta effekten ändringen har.  Optimering är en steg-för-steg-process som har olika resultat på olika datorer i din miljö.  Vad som fungerar för en konfiguration kanske inte fungerar för andra.

Vissa användbara länkar till ytterligare resurser: 

* [Premium Storage: Lagring med höga prestanda för arbetsbelastningar för virtuella Azure-datorer](premium-storage.md)
* [Användarhandbok för Azure Linux-Agent](../extensions/agent-linux.md)
* [Optimera MySQL-prestandan på virtuella Azure Linux-datorer](classic/optimize-mysql.md)
* [Konfigurera programvaru-RAID på Linux](configure-raid.md)

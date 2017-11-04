---
title: Optimera din virtuella Linux-dator i Azure | Microsoft Docs
description: "Läs tips optimering för att se till att du har lagt upp ditt Linux VM för optimala prestanda på Azure"
keywords: Linux-dator, virtuella linux ubuntu virtuell dator
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
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
ms.openlocfilehash: e63b50e06ae280819aea88f61bf9f25b6e44eac7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimera din virtuella Linux-dator på Azure
Det är enkelt att göra från kommandoraden eller från portalen att skapa en Linux-dator (VM). Den här kursen visar hur du se till att du har konfigurerat den för att optimera prestandan på Microsoft Azure-plattformen. Det här avsnittet använder en virtuell Ubuntu Server-dator, men du kan också skapa Linux virtuella datorer med hjälp av [egna avbildningar som mallar](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har ett aktivt Azure-prenumeration ([gratis utvärderingsversion registrering](https://azure.microsoft.com/pricing/free-trial/)) och redan har etablerat en virtuell dator i din Azure-prenumeration. Se till att du har senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad på Azure-prenumerationen med [az inloggningen](/cli/azure/#login) innan du [skapa en virtuell dator](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS-Disk
När du skapar en Linux VM i Azure har två diskar som är kopplade till den. **/ dev/sda** är OS-disken **/dev/sdb** är tillfällig disken.  Använd inte den huvudsakliga OS-disken (**/dev/sda**) för något annat än operativsystemet som det är optimerad för snabb VM starttiden och ger inte bra prestanda för din arbetsbelastning. Du vill koppla en eller flera diskar till den virtuella datorn få beständiga och optimerad lagring för dina data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lägga till diskar för mål storlek och prestanda
Baserat på VM-storlek, kan du bifoga upp till 16 ytterligare diskar på en A-serien, 32 diskar på D-serien och datorn 64 diskar på G-serien - varje upp till 1 TB i storlek. Du kan lägga till extra diskar som behövs per utrymme krav på och IOps. Varje disk har ett mål för prestanda för 500 IOps för standardlagring och upp till 5 000 IOps per disk för Premium-lagring.  Läs mer om Premium-lagring diskar [Premium-lagring: högpresterande lagring för virtuella Azure-datorer](../windows/premium-storage.md)

Att uppnå högsta IOps på Premium-lagring diskar där cacheinställningarna har ställts in på antingen **ReadOnly** eller **ingen**, måste du inaktivera **barriärer** vid montering filsystemet i Linux. Du behöver inte barriärer eftersom skrivningar till diskar som backas upp av Premium-lagring är beständig för dessa inställningar för cachelagring.

* Om du använder **reiserFS**, inaktivera barriärer med alternativet montera `barrier=none` (Använd för att aktivera barriärer `barrier=flush`)
* Om du använder **ext3/ext4**, inaktivera barriärer med alternativet montera `barrier=0` (Använd för att aktivera barriärer `barrier=1`)
* Om du använder **XFS**, inaktivera barriärer med alternativet montera `nobarrier` (Använd alternativet för att aktivera barriärer `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Ohanterad lagring överväganden för användarkonton
Standardåtgärden när du skapar en virtuell dator med Azure CLI 2.0 är att använda Azure hanterade diskar.  Diskarna ska hanteras av Azure-plattformen och behöver inte alla förberedelse eller plats att lagra dem.  Ohanterad diskar krävs ett lagringskonto och har vissa ytterligare prestandaöverväganden.  Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md).  I följande avsnitt beskrivs prestandaöverväganden bara när du använder ohanterade diskar.  Igen, standard och rekommenderade lagringslösning ska du använda hanterade diskar.

Om du skapar en virtuell dator med ohanterad diskar, se till att du bifoga diskar från storage-konton som finns i samma region som den virtuella datorn för att säkerställa nära och minimerar Nätverksfördröjningen.  Varje standardlagringskonto har högst 20 k IOps och kapaciteten 500 TB storlek.  Den här gränsen fungerar ungefär 40 hårt belastat diskar inklusive både operativsystemdisken och datadiskar som du skapar. Det finns ingen gräns för högsta IOps för Premium Storage-konton, men det finns en storleksgräns för 32 TB. 

När med hög IOps arbetsbelastningar och du har valt standardlagring för diskarna kan behöva du dela diskarna på flera lagringskonton för att kontrollera att du inte har nått 20 000 IOps-gräns för Standard Storage-konton. Den virtuella datorn kan innehålla en blandning av diskar från över olika lagringskonton och lagringskontotyper att uppnå optimal konfigurationen.
 

## <a name="your-vm-temporary-drive"></a>Den tillfälliga Virtuella enheten
Som standard när du skapar en virtuell dator Azure ger dig en OS-disk (**/dev/sda**) och en tillfällig disk (**/dev/sdb**).  Alla ytterligare diskar som du lägger till visa upp **/dev/sdc**, **/dev/sdd**, **/dev/sde** och så vidare. Alla data på hårddisken tillfälliga (**/dev/sdb**) är inte beständiga och kan gå förlorad om specifika händelser som VM storleksändring Omdistributionen, eller underhåll tvingar fram en omstart av den virtuella datorn.  Storlek och typ av tillfälliga disken är relaterad till VM-storlek som du väljer vid tidpunkten för distribution. Alla premium storlek på virtuella datorer (DS, G och DS_V2-serien) den tillfälliga enheten backas upp av en lokal SSD för ytterligare prestanda för upp till 48k IOps. 

## <a name="linux-swap-file"></a>Växlingsfil för Linux
Om din Azure VM är från en Ubuntu eller virtuell CoreOS-avbildning, kan du använda CustomData för att skicka en moln-config till molnet initiering. Om du [överföra en anpassad avbildning Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som använder molnet init, du också konfigurera växlingen partitioner som använder molnet initiering.

På Ubuntu molnet bilder, måste du använda molnet init för att konfigurera växla partition. Mer information finns i [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

För bilder utan stöd för molnet init har VM-avbildningar som distribueras från Azure Marketplace en virtuell Linux-agenten integrerat med Operativsystemet. Den här agenten kan den virtuella datorn att interagera med olika Azure-tjänster. Under förutsättning att du har distribuerat en standardiserad avbildning från Azure Marketplace, måste du göra följande för att konfigurera Linux växlingsfilens inställningar på rätt sätt:

Leta upp och ändra två poster i den **/etc/waagent.conf** fil. De styr förekomsten av en dedikerad växlingsfil och storleken på växlingsfilen. De parametrar som du vill ändra är `ResourceDisk.EnableSwap=N` och`ResourceDisk.SwapSizeMB=0` 

Ändra parametrarna för följande inställningar:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size i MB för att uppfylla dina behov} 

När du har gjort ändringen måste du starta om waagent eller din Linux VM för att spegla ändringarna.  Du vet att ändringarna som har genomförts och en växlingsfil har skapats när du använder den `free` kommando för att visa ledigt utrymme. I följande exempel har en 512MB växlingsfil som skapas när du ändrar den **waagent.conf** fil:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/o-schemaläggning algoritm för Premium-lagring
Med 2.6.18 Linux kernel, standard-i/o schemaläggning algoritmen har ändrats från tidsgräns till CFQ (helt verkliga queuing algoritmen). Det finns försumbar skillnad i prestanda skillnader mellan CFQ och tidsgräns för direktåtkomst i/o-mönster.  För SSD-baserad diskar där mönstret för disk-i/o är huvudsakligen sekventiella, uppnå växla tillbaka till algoritmen NOOP eller tidsgräns högre i/o-prestanda.

### <a name="view-the-current-io-scheduler"></a>Visa aktuella i/o-Schemaläggaren
Ange följande kommando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Du kan se följande utdata som visar aktuella Schemaläggaren.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/ dev/sda) av i/o schemaläggning algoritm
Använd följande kommandon:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Tillämpa den här inställningen för **/dev/sda** fristående är inte användbar. Ange på alla datadiskar där sekventiella i/o dominerar i/o-mönster.  

Du bör se följande utdata som visar att **grub.cfg** har återskapats och som standard Schemaläggaren har uppdaterats till NOOP.  

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

Redhat distribution-familjen behöver du bara följande kommando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Med hjälp av programvara RAID för att uppnå högre I / Ops
Om dina arbetsbelastningar kräver fler IOps än vad en enda disk, måste du använda en programvara RAID-konfiguration av flera diskar. Eftersom Azure utför redan disk återhämtning i lokala fabric-lagret, kan du få högsta möjliga prestanda från en konfiguration med RAID-0 striping.  Etablera och skapa diskar i Azure-miljön och koppla dem till din Linux VM före partitionering, formatering och montera enheterna.  Mer information om hur du konfigurerar en RAID konfiguration av programvara på din Linux VM i azure finns i den  **[konfigurera programvara RAID på Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**  dokumentet.

## <a name="next-steps"></a>Nästa steg
Kom ihåg att du behöver utföra tester före och efter varje ändring av åtgärdens påverkan ändringen har med alla optimering diskussioner.  Optimering är en steg-för-steg-process som har olika resultat på olika datorer i din miljö.  Vad fungerar för en konfiguration fungerar inte för andra.

Vissa länkar till ytterligare resurser: 

* [Premium Storage: Lagring med höga prestanda för Azure Virtual Machines-arbetsbelastningar](../windows/premium-storage.md)
* [Användarhandboken för Azure Linux-Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Optimera MySQL prestanda på virtuella Azure Linux-datorer](classic/optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Konfigurera programvarubaserad RAID på Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


---
title: Optimera din virtuella Linux-dator på Azure
description: Lär dig några optimerings tips för att se till att du har konfigurerat din virtuella Linux-dator för optimala prestanda på Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 1e3551834e7664d5036fa8a5e0497e5a37f61c2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498514"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimera din virtuella Linux-dator på Azure
Det är enkelt att skapa en virtuell Linux-dator (VM) från kommando raden eller från portalen. Den här självstudien visar hur du ser till att du har konfigurerat för att optimera prestandan på Microsoft Azures plattformen. I det här avsnittet används en virtuell Ubuntu-Server, men du kan också skapa en virtuell Linux-dator med [dina egna avbildningar som mallar](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet förutsätter att du redan har en fungerande Azure-prenumeration ([kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/)) och redan har etablerad en virtuell dator till din Azure-prenumeration. Kontrol lera att du har det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat i din Azure-prenumeration med [AZ-inloggning](/cli/azure/reference-index) innan du [skapar en virtuell dator](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS-disk
När du har skapat en virtuell Linux-dator i Azure har den två diskar kopplade till sig. **/dev/SDA** är din OS-disk, **/dev/SDB** är den tillfälliga disken.  Använd inte **/dev/SDA**(Main OS disk) för något annat än operativ systemet eftersom det är optimerat för snabb start av virtuell dator och inte ger dig höga prestanda för dina arbets belastningar. Du vill koppla en eller flera diskar till din virtuella dator för att få beständig och optimerad lagring för dina data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lägga till diskar för storleks-och prestanda mål
Baserat på storleken på den virtuella datorn kan du ansluta upp till 16 ytterligare diskar på en A-serien, 32 diskar på en D-serie och 64-diskar på en dator i G-serien – varje upp till 32 TB i storlek. Du lägger till extra diskar efter behov enligt dina krav på ditt space och IOps. Varje disk har ett prestanda mål på 500 IOps för standard lagring och upp till 20 000 IOps per disk för Premium Storage.

För att uppnå högsta IOps på Premium Storage diskar där deras cacheinställningar har angetts till antingen **ReadOnly** eller **ingen**, måste du inaktivera **barriärer** när du monterar fil systemet i Linux. Du behöver inte hinder eftersom skrivningarna till Premium Storage de säkerhetskopierade diskarna är varaktiga för dessa cacheinställningar.

* Om du använder **reiserFS** inaktiverar du barriärer med hjälp av monterings alternativet `barrier=none` (för att aktivera hinder, användning `barrier=flush` )
* Om du använder **EXT3/Ext4** inaktiverar du barriärer med hjälp av monterings alternativet `barrier=0` (för att aktivera hinder, användning `barrier=1` )
* Om du använder **xfs** inaktiverar du barriärer med hjälp av monterings alternativet `nobarrier` (för att aktivera hinder använder du alternativet `barrier` )

## <a name="unmanaged-storage-account-considerations"></a>Överväganden vid ohanterat lagrings konto
Standard åtgärden när du skapar en virtuell dator med Azure CLI är att använda Azure Managed Disks.  Diskarna hanteras av Azure-plattformen och kräver inte någon förberedelse eller plats för att lagra dem.  Ohanterade diskar kräver ett lagrings konto och några ytterligare prestanda överväganden.  Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../managed-disks-overview.md).  I följande avsnitt beskrivs prestanda överväganden bara när du använder ohanterade diskar.  Standardvärdet och den rekommenderade lagrings lösningen är att använda hanterade diskar.

Om du skapar en virtuell dator med ohanterade diskar, se till att du kopplar diskar från lagrings konton som finns i samma region som din virtuella dator för att säkerställa nära närhet och minimera nätverks fördröjningen.  Varje standard lagrings konto har högst 20 000 IOps och 500 TB storleks kapacitet.  Den här gränsen fungerar på ungefär 40 mycket använda diskar, inklusive både OS-disken och alla data diskar som du skapar. För Premium Storage-konton finns det ingen maximal IOps-gräns, men det finns en storleks gräns på 32 TB. 

När du hanterar höga IOps-arbetsbelastningar och du har valt standard lagring för diskarna kan du behöva dela upp diskarna över flera lagrings konton för att se till att du inte har nått 20 000 IOps-gränsen för standard lagrings konton. Din virtuella dator kan innehålla en blandning av diskar från olika lagrings konton och lagrings konto typer för att uppnå den optimala konfigurationen.
 

## <a name="your-vm-temporary-drive"></a>VM-temporär enhet
Som standard när du skapar en virtuell dator ger Azure dig en OS-disk (**/dev/SDA**) och en tillfällig disk (**/dev/SDB**).  Alla ytterligare diskar som du lägger till visas som **/dev/SDC**, **/dev/SDD**, **/dev/SDE** och så vidare. Alla data på din temporära disk (**/dev/SDB**) är inte varaktiga och kan gå förlorade om vissa händelser som storleks ändring av virtuella datorer, omdistribution eller underhåll tvingar fram en omstart av den virtuella datorn.  Den temporära diskens storlek och typ är relaterad till den VM-storlek som du valde vid distributions tillfället. Alla virtuella datorer med Premium storlek (DS, G och DS_V2 serien) den tillfälliga enheten backas upp av en lokal SSD för ytterligare prestanda på upp till 48k IOps. 

## <a name="linux-swap-partition"></a>Linux-växlings partition
Om din virtuella Azure-dator är från en Ubuntu-eller Core-avbildning kan du använda CustomData för att skicka en Cloud-config till Cloud-init. Om du har [överfört en anpassad Linux-avbildning](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som använder Cloud-Init kan du också konfigurera växla partitioner med Cloud-init.

Du kan inte använda **/etc/waagent.conf** -filen för att hantera växling för alla avbildningar som tillhandahålls och stöds av Cloud-init. En fullständig lista över avbildningar finns i [använda Cloud-Init](using-cloud-init.md). 

Det enklaste sättet att hantera växling för de här avbildningarna är att slutföra de här stegen:

1. I mappen **/var/lib/Cloud/scripts/per-Boot** skapar du en fil med namnet **create_swapfile. sh**:

   **$ sudo touch/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

1. Lägg till följande rader i filen:

   **$ sudo vi/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

   ```
   #!/bin/sh
   if [ ! -f '/mnt/swapfile' ]; then
   fallocate --length 2GiB /mnt/swapfile
   chmod 600 /mnt/swapfile
   mkswap /mnt/swapfile
   swapon /mnt/swapfile
   swapon -a ; fi
   ```

   > [!NOTE]
   > Du kan ändra värdet efter behov och utifrån det tillgängliga utrymmet på resurs disken, beroende på vilken VM-storlek som används.

1. Gör filen körbar:

   **$ sudo chmod + x/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

1. Skapa swapfile genom att köra skriptet direkt efter det sista steget:

   **$ sudo/var/lib/Cloud/scripts/per-Boot/./create_swapfile. sh**

För avbildningar utan stöd för Cloud-Init har VM-avbildningar som distribueras från Azure Marketplace en VM Linux-Agent integrerad med operativ systemet. Den här agenten gör det möjligt för den virtuella datorn att samverka med olika Azure-tjänster. Förutsatt att du har distribuerat en standard avbildning från Azure Marketplace måste du göra följande för att konfigurera inställningarna för Linux-växlings filen på rätt sätt:

Leta upp och ändra två poster i **/etc/waagent.conf** -filen. De styr förekomsten av en dedikerad växlings fil och storlek på växlings filen. De parametrar du behöver verifiera är `ResourceDisk.EnableSwap` och `ResourceDisk.SwapSizeMB` 

Om du vill aktivera en korrekt aktive rad disk och monterad växlings fil kontrollerar du att parametrarna har följande inställningar:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {size i MB för att uppfylla dina behov} 

När du har gjort ändringen måste du starta om waagent eller starta om den virtuella Linux-datorn för att avspegla ändringarna.  Du vet att ändringarna har implementerats och att växlings filen har skapats när du använder `free` kommandot för att Visa ledigt utrymme. I följande exempel finns en växlings fil med 512 MB som skapats som ett resultat av en ändring av filen **waagent. conf** :

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O-schemaläggnings algoritm för Premium Storage
Med 2.6.18 Linux-kärnan ändrades standardalgoritmen för I/O-schemaläggning från tids gränsen till CFQ (helt korrekt kö). För slumpmässiga åtkomst-I/O-mönster är det försumbar skillnad i prestanda skillnader mellan CFQ och tids gräns.  För SSD-baserade diskar där diskens I/O-mönster är sekventiellt sekventiella, kan du få bättre I/O-prestanda om du växlar tillbaka till NOOP eller tids gräns algoritmen.

### <a name="view-the-current-io-scheduler"></a>Visa den aktuella I/O-Schemaläggaren
Ange följande kommando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Du ser följande utdata, som anger den aktuella Schemaläggaren.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/dev/SDA) för I/O-schemaläggnings algoritmen
Använd följande kommandon:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Det är inte praktiskt att använda den här inställningen för **/dev/SDA** ensamt. Ange på alla data diskar där sekventiella I/O dominerar i/O-mönstret.  

Du bör se följande utdata, vilket indikerar att **grub. cfg** har återskapats och att standard Scheduler har uppdaterats till Noop.  

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

För Red Hat-distributions familjen behöver du bara följande kommando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Ubuntu 18,04 med Azure-justerade kernel använder I/O-scheman för flera köer. I det scenariot `none` är det lämpligt val i stället för `noop` . Mer information finns i [Ubuntu i/O-schemaläggare](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>Använda programvaru-RAID för att uppnå högre I/OPS
Om dina arbets belastningar kräver mer IOps än en enskild disk kan du behöva använda en RAID-konfiguration på flera diskar. Eftersom Azure redan utför disk återhämtning på det lokala Fabric-lagret, uppnår du den högsta prestanda nivån från en konfiguration för RAID-0-skiktning.  Etablera och skapa diskar i Azure-miljön och koppla dem till din virtuella Linux-dator innan partitionering, formatering och montering av enheterna.  Mer information om hur du konfigurerar en programvaru-RAID-installation på din virtuella Linux-dator i Azure finns i **[Konfigurera programvaru-RAID på Linux](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** -dokument.

Som ett alternativ till en traditionell RAID-konfiguration kan du också välja att installera Logical Volume Manager (LVM) för att konfigurera ett antal fysiska diskar i en enda stripe-volym för logisk lagring. I den här konfigurationen distribueras läsningar och skrivningar till flera diskar som ingår i volym gruppen (liknar RAID0). Av prestanda skäl är det troligt att du vill ta bort dina logiska volymer så att läsningar och skrivningar använder alla dina anslutna data diskar.  Mer information om hur du konfigurerar en striped logisk volym på din virtuella Linux-dator i Azure finns i **[Konfigurera LVM på en virtuell Linux-dator i Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** -dokument.

## <a name="next-steps"></a>Nästa steg
Kom ihåg, precis som med alla optimerings diskussioner, måste du utföra tester före och efter varje ändring för att mäta effekten av ändringen.  Optimering är en steg-för-steg-process som har olika resultat på olika datorer i din miljö.  Det som fungerar för en konfiguration kanske inte fungerar för andra.

Några användbara länkar till ytterligare resurser:

* [Användarguide för Azure Linux-agenten](../extensions/agent-linux.md)
* [Konfigurera programvaru-RAID på Linux](/previous-versions/azure/virtual-machines/linux/configure-raid)
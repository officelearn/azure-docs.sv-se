---
title: Optimera din virtuella Linux-dator på Azure
description: Lär dig några optimeringstips för att se till att du har konfigurerat din Virtuella Linux-dator för optimal prestanda på Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 87776c14e45ff4bb3cce6661323d74a1315c8ab2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757100"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimera din virtuella Linux-dator på Azure
Det är enkelt att skapa en virtuell Linux-dator (VM) från kommandoraden eller från portalen. Den här självstudien visar hur du säkerställer att du har konfigurerat den för att optimera prestanda på Microsoft Azure-plattformen. Det här avsnittet använder en virtuell Ubuntu Server-dator, men du kan också skapa virtuella Linux-datorer med [dina egna avbildningar som mallar](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har en fungerande Azure-prenumeration ([kostnadsfri utvärderingskorrigering)](https://azure.microsoft.com/pricing/free-trial/)och redan har etablerat en virtuell dator i din Azure-prenumeration. Se till att du har den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på din Azure-prenumeration med [az-inloggning](/cli/azure/reference-index) innan du [skapar en virtuell dator](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS-disk
När du har skapat en Virtuell Linux-dator i Azure har den två diskar som är associerade med den. **/dev/sda** är din OS-disk, **/dev/sdb** är din temporära disk.  Använd inte huvud-OS-disken (**/dev/sda**) för något annat än operativsystemet eftersom det är optimerat för snabb vm-starttid och inte ger bra prestanda för dina arbetsbelastningar. Du vill koppla en eller flera diskar till den virtuella datorn för att få beständig och optimerad lagring för dina data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lägga till diskar för storleks- och prestandamål
Baserat på den virtuella datorns storlek kan du koppla upp till 16 ytterligare diskar på en A-serie, 32 diskar på en D-serie och 64 diskar på en G-seriemaskin – vardera upp till 32 TB i storlek. Du lägger till extra diskar efter behov enligt ditt space och IOps krav. Varje disk har ett prestandamål på 500 IOps för standardlagring och upp till 20 000 IOps per disk för Premium Storage.

För att uppnå de högsta IOps på Premium Storage-diskar där deras cacheinställningar har ställts in på antingen **ReadOnly** eller **Ingen**måste du inaktivera **hinder** när du monterar filsystemet i Linux. Du behöver inga hinder eftersom skrivningarna till Premium Storage-säkerhetskopierade diskar är hållbara för dessa cacheinställningar.

* Om du använder **reiserFS**inaktiverar `barrier=none` du hinder med monteringsalternativet (För att aktivera hinder, använd) `barrier=flush`
* Om du använder **ext3/ext4**inaktiverar `barrier=0` du hinder med monteringsalternativet (För att aktivera hinder, använd `barrier=1`)
* Om du använder **XFS**inaktiverar `nobarrier` du hinder med monteringsalternativet `barrier`(Använd alternativet )

## <a name="unmanaged-storage-account-considerations"></a>Överväganden för ohanterat lagringskonto
Standardåtgärden när du skapar en virtuell dator med Azure CLI är att använda Azure Managed Disks.  Dessa diskar hanteras av Azure-plattformen och kräver inga förberedelser eller platser för att lagra dem.  Ohanterat diskar kräver ett lagringskonto och har några ytterligare prestandaöverväganden.  Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md).  I följande avsnitt beskrivs prestandaöverväganden endast när du använder ohanterade diskar.  Återigen är standard- och rekommenderade lagringslösningen att använda hanterade diskar.

Om du skapar en virtuell dator med ohanterat diskar kontrollerar du att du kopplar diskar från lagringskonton som finns i samma region som den virtuella datorn för att säkerställa närhet och minimera nätverksfördröjningen.  Varje standardlagringskonto har högst 20 k IOps och en kapacitet på 500 TB.  Den här gränsen fungerar till cirka 40 hårt använda diskar, inklusive både OS-disken och alla datadiskar som du skapar. För Premium Storage-konton finns det ingen maximal IOps-gräns, men det finns en storleksgräns på 32 TB. 

När du hanterar hög IOps-arbetsbelastning och du har valt Standardlagring för dina diskar kan du behöva dela diskarna över flera lagringskonton för att se till att du inte har nått gränsen på 20 000 IOps för standardlagringskonton. Den virtuella datorn kan innehålla en blandning av diskar från olika lagringskonton och lagringskontotyper för att uppnå din optimala konfiguration.
 

## <a name="your-vm-temporary-drive"></a>Tillfällig hårddisk för den virtuella datorn
Som standard när du skapar en virtuell dator, azure ger dig en OS-disk (**/dev/sda**) och en tillfällig disk (**/dev/sdb**).  Alla ytterligare diskar som du lägger till visas som **/dev/sdc**, **/dev/sdd**, **/dev/sde** och så vidare. Alla data på den temporära disken (**/dev/sdb**) är inte hållbara och kan gå förlorade om specifika händelser som storleksändring, omfördelning av virtuella datorer eller underhåll tvingar en omstart av den virtuella datorn.  Storleken och typen på den tillfälliga disken är relaterad till den virtuella datorstorlek som du valde vid distributionen. Alla virtuella premiumstorleks-datorer (DS, G och DS_V2-serien) den tillfälliga enheten backas upp av en lokal SSD för ytterligare prestanda på upp till 48 k IOps. 

## <a name="linux-swap-partition"></a>Linux Swap Partition
Om din virtuella Azure-dator kommer från en Ubuntu- eller CoreOS-avbildning kan du använda CustomData för att skicka en molnkonfiguration till cloud-init. Om du [har laddat upp en anpassad Linux-avbildning](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som använder cloud-init konfigurerar du även växlingspartitioner med cloud-init.

På Ubuntu Cloud Images måste du använda cloud-init för att konfigurera växlingspartitionen. Mer information finns i [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

För avbildningar utan molninitstöd har VM-avbildningar som distribueras från Azure Marketplace en VM Linux-agent integrerad med operativsystemet. Med den här agenten kan den virtuella datorn interagera med olika Azure-tjänster. Förutsatt att du har distribuerat en standardavbildning från Azure Marketplace måste du göra följande för att konfigurera inställningarna för Linux-växlingsfilen på rätt sätt:

Leta upp och ändra två poster i filen **/etc/waagent.conf.** De kontrollerar förekomsten av en särskild växlingsfil och storleken på växlingsfilen. De parametrar du behöver `ResourceDisk.EnableSwap` verifiera är och`ResourceDisk.SwapSizeMB` 

Om du vill aktivera en korrekt aktiverad disk och monterad växlingsfil kontrollerar du att parametrarna har följande inställningar:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={storlek i MB för att uppfylla dina behov} 

När du har gjort ändringen måste du starta om waagent eller starta om din Virtuella Linux-dator för att återspegla dessa ändringar.  Du vet att ändringarna har implementerats och en växlingsfil har skapats när du använder `free` kommandot för att visa ledigt utrymme. Följande exempel har en 512 MB växlingsfil som skapats som ett resultat av att ändra **filen waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O-schemaläggningsalgoritm för Premium Storage
Med Linux-kärnan på 2.6.18 ändrades standardalgoritmen för I/O-schemaläggning från Deadline till CFQ (Helt rättvis köalgoritm). För I/O-mönster med slumpmässig åtkomst finns det försumbara skillnader i prestandaskillnader mellan CFQ och Deadline.  För SSD-baserade diskar där disk-I/O-mönstret huvudsakligen är sekventiellt kan växla tillbaka till NOOP- eller Deadline-algoritmen uppnå bättre I/O-prestanda.

### <a name="view-the-current-io-scheduler"></a>Visa aktuell I/O-schemaläggare
Ange följande kommando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Du ser följande utdata, vilket indikerar den aktuella schemaläggaren.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Ändra den aktuella enheten (/dev/sda) för I/O-schemaläggningsalgoritm
Använd följande kommandon:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Det är inte användbart att använda den här inställningen för **/dev/sda.** Ställ in på alla datadiskar där sekventiell I/O dominerar I/O-mönstret.  

Du bör se följande utdata, vilket anger att **grub.cfg** har byggts om och att standardschemaläggaren har uppdaterats till NOOP.  

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

För Red Hat-distributionsfamiljen behöver du bara följande kommando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Använda Software RAID för att uppnå högre I/Ops
Om dina arbetsbelastningar kräver fler IOps än en enda disk kan ge, måste du använda en programvara RAID-konfiguration av flera diskar. Eftersom Azure redan utför diskåtersåtershantering på det lokala infrastrukturlagret uppnår du högsta prestandanivå från en RAID-0-striping-konfiguration.  Etablera och skapa diskar i Azure-miljön och koppla dem till din Virtuella Linux-dator innan du partitionerar, formaterar och monterar enheterna.  Mer information om hur du konfigurerar en programvara RAID-installation på din Linux VM i Azure finns i **[konfigurera Software RAID på Linux-dokument.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Som ett alternativ till en traditionell RAID-konfiguration kan du också välja att installera Logisk volymhanterare (LVM) för att konfigurera ett antal fysiska diskar i en enda randig logisk lagringsvolym. I den här konfigurationen distribueras läsningar och skrivningar till flera diskar i volymgruppen (liknande RAID0). Av prestandaskäl är det troligt att du kommer att vilja randa dina logiska volymer så att läser och skriver använder alla dina bifogade datadiskar.  Mer information om hur du konfigurerar en stripe-logisk volym på din Virtuella Linux-dator i Azure finns i **[Konfigurera LVM på en Virtuell Linux-dator i](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** Azure-dokument.

## <a name="next-steps"></a>Efterföljande moment
Kom ihåg, som med alla optimeringsdiskussioner, måste du utföra tester före och efter varje ändring för att mäta effekten ändringen har.  Optimering är en steg för steg process som har olika resultat mellan olika datorer i din miljö.  Vad som fungerar för en konfiguration kanske inte fungerar för andra.

Några användbara länkar till ytterligare resurser:

* [Användarguide för Azure Linux-agenten](../extensions/agent-linux.md)
* [Konfigurera Software RAID på Linux](configure-raid.md)

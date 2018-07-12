---
title: Skapa och ladda upp en VHD för Linux i Azure
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller en Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972357"
---
# <a name="information-for-non-endorsed-distributions"></a>Information om icke-godkända distributioner
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure-plattformen serviceavtalet gäller för virtuella datorer som kör Linux-operativsystem endast när en av de [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) används. Linux-avbildningar tillhandahålls på Azure Marketplace med önskad konfiguration för dessa distributioner som stöds.

* [Linux på Azure - godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alla distributioner som körs på Azure måste du uppfylla vissa krav för att få möjligheten att fungera på plattformen.  Den här artikeln är inte menat omfattande eftersom varje distribution är annorlunda, och det är möjligt att du behöver betydligt justera din Linux-systemet för att säkerställa att den körs korrekt på plattformen även om du uppfyller alla villkor som nedan.

Det är därför som vi rekommenderar att du börjar med en [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) när det är möjligt. I följande artikel vägleder dig genom hur du förbereder de olika godkända Linux-distributioner som stöds på Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Resten av den här artikeln fokuserar på allmänna riktlinjer för att köra Linux-distribution på Azure.

## <a name="general-linux-installation-notes"></a>Allmän Linux installationsinformation
* VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten convert-vhd. Om du använder VirtualBox innebär det att välja **fast storlek** istället för standard dynamiskt allokerade när du skapar disken.
* Azure stöder bara virtuella datorer i generation 1. Du kan konvertera en virtuell dator i generation 1 från VHDX till VHD-format och dynamiskt expanderande till en fast storlek disk. Men du kan inte ändra generering av en virtuell dator. Mer information finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Den maximala storleken som tillåts för den virtuella Hårddisken är 1,023 GB.
* När du installerar Linux-systemet är det *rekommenderas* att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar.
* Kernel-stöd för att montera filsystem för UDF krävs. Vid första start på Azure skickas etableringskonfiguration för Linux-VM via UDF-formaterad medier som är kopplad till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux-kernel-versioner under 2.6.37 stöder inte NUMA på Hyper-V med större storlekar för Virtuella datorer. I detta fall främst påverkar äldre distributioner som använder den överordnade Red Hat 2.6.32 kernel och åtgärdades i RHEL 6.6 (kernel-2.6.32-504). System som kör anpassade kernlar som är äldre än 2.6.37 eller RHEL-baserade kernlar som är äldre än 2.6.32-504 måste ange parametern Start `numa=off` på kommandoraden i grub.conf kernel. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Mer information finns i stegen nedan.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installera kernel moduler utan Hyper-V
Azure körs på Hyper-V-hypervisor så Linux kräver att vissa kernel-moduler är installerade för att köra i Azure. Om du har en virtuell dator som skapats utanför Hyper-V kanske Linux-installationsprogram inte med drivrutiner för Hyper-V inledande ramdisk (initrd eller initramfs) såvida inte den upptäcker att den körs på en Hyper-V-miljö. När du använder ett annat virtualiseringssystem (det vill säga Virtualbox, KVM osv.) för att förbereda din Linux-avbildning, kan du behöva återskapa initrd för att säkerställa att minst `hv_vmbus` och `hv_storvsc` kernel-moduler är tillgängliga på den första ramdisk.  Det här är ett känt problem minst på system som baseras på den överordnade Red Hat-distributionen.

Mekanism för att återskapa initrd eller initramfs avbildningen kan variera beroende på vilken distribution. Rätt proceduren finns i dokumentation eller stöd för din distribution.  Följande är ett exempel att återskapa initrd med hjälp av den `mkinitrd` verktyget:

Börja säkerhetskopiera den befintliga initrd-avbildningen:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Därefter återskapa initrd med den `hv_vmbus` och `hv_storvsc` kernel-moduler:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Ändra storlek på virtuella hårddiskar
VHD-avbildningar på Azure måste ha en virtuell storlek justeras till 1 MB.  Normalt bör redan VHD: er som skapats med hjälp av Hyper-V anpassas korrekt.  Om den virtuella Hårddisken inte är justerad korrekt, kan du få ett felmeddelande som liknar följande när du försöker skapa en *bild* från en virtuell Hårddisk:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Du kan åtgärda problemet genom att ändra storlek på den virtuella datorn med Hyper-V Manager-konsolen eller [storleksändring-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell-cmdlet.  Om du inte kör i en Windows-miljö, rekommenderas att använda qemu img för att konvertera (vid behov) och ändra storlek på den virtuella Hårddisken.

> [!NOTE]
> Det finns ett känt fel i qemu img versioner > = 2.2.1 som resulterar i en felaktigt formaterad virtuell Hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda qemu img 2.2.0 eller lägre eller uppdatera 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Ändra storlek på den virtuella Hårddisken direkt med hjälp av verktyg som `qemu-img` eller `vbox-manage` kan resultera i ett offlineläge VHD.  Därför rekommenderar vi att du första konvertera den virtuella Hårddisken till en rå diskavbildning.  Om avbildningen har redan skapats som RAW diskavbildning (standard för vissa hypervisor-program, till exempel KVM) kan du hoppa över det här steget:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Beräkna den nödvändiga storleken av diskavbildningen att säkerställa att den virtuella storleken justeras till 1 MB.  Följande bash shell-skript kan hjälpa dig med detta.  Skriptet använder ”`qemu-img info`” att fastställa virtuell storlek på disk image och beräknar sedan storleken till nästa 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Ändra storlek på raw disken med hjälp av $rounded_size som angetts i skriptet ovan:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Nu kan konvertera den RÅA disken till en virtuell Hårddisk med fast storlek:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Eller, med qemu version **2.6 +** inkluderar den `force_size` alternativet:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux-Kernel-krav
Linux Integration Services (LIS)-drivrutiner för Hyper-V och Azure tillhandahålls direkt till den överordnade Linux-kerneln. Många distributioner som innehåller en ny Linux-kernel-version (d.v.s. 3.x) har redan de här drivrutinerna som är tillgängliga eller annars har anpassats versioner av de här drivrutinerna med sina kernlar.  De här drivrutinerna uppdateras ständigt i överordnade kerneln med nya korrigeringar och funktioner, så när det är möjligt rekommenderar vi att du kör en [godkända distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som innehåller dessa korrigeringar och uppdateringar.

Om du kör en variant av Red Hat Enterprise Linux-versioner **6.0 6.3**, måste du installera de senaste drivrutinerna för LIS för Hyper-V. Drivrutinerna finns [på den här platsen](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Från och med RHEL **6.4 +** (och derivat) drivrutinerna LIS ingår redan i kerneln och därför inga ytterligare Installationspaketen behövs för att köra dessa system på Azure.

Om en anpassad kernel krävs, rekommenderar vi att du använder en nyare kernel-version (d.v.s. **3.8 +**). För dessa distributioner eller leverantörer som hanterar sina egna kernel möda krävs för att regelbundet backport LIS drivrutiner från den överordnade kerneln din anpassade kerneln.  Även om du redan kör en relativt nytt kernel-version, rekommenderas att hålla reda på eventuella överordnade korrigeringar med LIS drivrutiner och backport dem efter behov. LIS drivrutinens källfiler finns tillgängliga i den [sköter underhåll SJÄLVA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) filen i trädet Linux-kernel källa:

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

Minst följande korrigeringar inte har rapporterats kan orsaka problem i Azure och så de måste inkluderas i kerneln. Den här listan är inte menat uttömmande eller fullständig för alla distributioner:

* [ata_piix: Skjut upp diskar till Hyper-V-drivrutiner som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: konto för transit paket i sökvägen för återställning](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: undvika användningen av WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: inaktivera skriva samma för RAID och drivrutiner för nätverkskort för virtuell värd](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL-markören avreferera-korrigering](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer fel kan resultera i i/o-låsning](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: skydda mot dubbla körningen av __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux-agenten
Den [Azure Linux Agent](../extensions/agent-linux.md) (waagent) krävs för att korrekt etablera en Linux-dator i Azure. Du kan hämta den senaste versionen, filen problem eller skicka in pull-begäranden på den [Linux-agenten GitHub-lagringsplatsen](https://github.com/Azure/WALinuxAgent).

* Linux-agenten ingår i Apache 2.0-licens. Många distributioner anger redan RPM- eller deb paket för agenten och så i vissa fall kan detta kan du installera och uppdatera utan besvär.
* Azure Linux Agent kräver Python v2.6 +.
* Agenten kräver också python-pyasn1-modulen. De flesta distributioner tillhandahåller detta som ett separat paket som kan installeras.
* I vissa fall kanske Azure Linux Agent inte är kompatibla med NetworkManager. Många av de RPM/Deb-paket som tillhandahålls av distributioner konfigurera NetworkManager som en konflikt i waagent-paket och därmed avinstalleras NetworkManager när du installerar Linux-agenten.
* Azure Linux Agent måste vara ovanför den lägsta versionen som stöds, se den här artikeln för [information](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Systemkrav för allmänna Linux

* Ändra i kernel boot line i GRUB eller GRUB2 för att inkludera följande parametrar. Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem med:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem.
  
    Förutom ovanstående rekommenderar vi att du *ta bort* följande parametrar om de finns:
  
        rhgb quiet crashkernel=auto
  
    Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Den `crashkernel` alternativet kanske vänster konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på mindre storlekar för Virtuella datorer.

* Installera Azure Linux-agenten
  
    Azure Linux Agent krävs för att etablera en Linux-avbildning på Azure.  Många distributioner ange agenten som en RPM- eller Deb-paketet (paketet kallas vanligtvis ”WALinuxAgent' eller 'walinuxagent').  Agenten kan också installeras manuellt genom att följa stegen i den [Linux-agenten Guide](../extensions/agent-linux.md).

* Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.

* Skapa inte växlingsutrymme på OS-disken
  
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Kör följande kommandon för att avetablera den virtuella datorn som ett sista steg:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > På Virtualbox kan du se följande fel efter körning ”waagent-force - avetablering ': `[Errno 5] Input/output error`. Det här felmeddelandet är inte viktigt och kan ignoreras.
  > 
  > 

* Stäng av den virtuella datorn och överför den virtuella Hårddisken till Azure.


---
title: Skapa och ladda upp en Linux VHD i Azure
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
ms.openlocfilehash: 5d08c752af23845a31428b16ee3de594044b4129
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="information-for-non-endorsed-distributions"></a>Information om icke-godkända distributioner
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure-plattformen SLA gäller för virtuella datorer som kör Linux OS bara när en av de [-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) används. Linux-avbildningar finns i Azure Marketplace med nödvändig konfiguration för dessa påtecknade distributioner.

* [Linux på Azure - godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alla distributioner som körs på Azure måste uppfylla vissa krav för att prova att fungera på plattformen.  Den här artikeln är inte menat omfattande som varje distribution är olika. och det är fullt möjligt att även om du uppfyller alla följande villkor du fortfarande behöver justera avsevärt Linux-system för att säkerställa att den körs korrekt på plattformen.

Det är därför som vi rekommenderar att du börjar med en av våra [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) när det är möjligt. I följande artiklar hjälper dig att förbereda de olika påtecknade Linux-distributioner som stöds i Azure:

* **[CentOS-based Distributions](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Resten av den här artikeln fokuserar på allmänna riktlinjer för att köra Linux-distribution på Azure.

## <a name="general-linux-installation-notes"></a>Allmän Linux installationsinformation
* VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet convert-vhd. Om du använder VirtualBox innebär detta att välja **fast storlek** till skillnad från standardvärdet dynamiskt allokerade när skapas.
* Azure har endast stöd för virtuella datorer i generation 1. Du kan konvertera en virtuell dator i generation 1 från VHDX till VHD-format och dynamiskt expanderande till en disk med fast storlek. Men du kan inte ändra en virtuell dator generation. Mer information finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Den maximala storleken för den virtuella Hårddisken är 1,023 GB.
* När du installerar Linux-system är *rekommenderas* att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar.
* Kernel-stöd för att montera UDF-filsystem krävs. Vid första start på Azure skickas etablering konfigurationen för Linux-VM via UDF-formaterad media som är anslutna till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux kernel-versioner än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar huvudsakligen äldre distributioner med hjälp av den överordnade Red Hat 2.6.32 kernel och åtgärdades i RHEL 6.6 (kernel-2.6.32-504). System som kör anpassade kernlar som är äldre än 2.6.37 eller RHEL-baserade kernlar som är äldre än 2.6.32-504 måste ange parametern Start `numa=off` på kommandoraden i grub.conf kernel. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en byte-partition på OS-disk. Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB innan konverteringen. Mer information finns i stegen nedan.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installera kernel moduler utan Hyper-V
Azure körs på Hyper-V-hypervisor så Linux kräver att vissa kernel-moduler är installerade för att kunna köras i Azure. Om du har en virtuell dator som har skapats utanför Hyper-V, Linux-installationsprogram kanske inte med drivrutiner för Hyper-V inledande ramdisk (initrd eller initramfs) såvida inte den upptäcker att den körs en Hyper-V-miljö. När du använder ett system med olika virtualisering (d.v.s. Virtualbox, KVM osv.) för att förbereda avbildningen Linux, du kan behöva återskapa initrd för att säkerställa att åtminstone `hv_vmbus` och `hv_storvsc` kernel-moduler är tillgängliga på den första ramdisk.  Detta är ett känt problem minst på system baserat på den överordnade Red Hat-distributionen.

Mekanism för att återskapa initrd eller initramfs avbildningen kan variera beroende på distributionsplatsen. Dokumentationen för din distribution eller stöd för på rätt sätt.  Här är ett exempel på hur du återskapa den initrd med hjälp av den `mkinitrd` verktyget:

Först säkerhetskopiera den befintliga initrd avbildningen:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Därefter återskapa initrd med den `hv_vmbus` och `hv_storvsc` kernel moduler:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Ändra storlek på virtuella hårddiskar
VHD-avbildningar i Azure måste ha en virtuell storlek justeras till 1MB.  Normalt ska redan virtuella hårddiskar som skapats med hjälp av Hyper-V justeras korrekt.  Om den virtuella Hårddisken inte är korrekt justerat så att du får ett felmeddelande liknande följande när du försöker skapa en *bild* från den virtuella Hårddisken:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

För att åtgärda detta kan du ändra storlek på den virtuella datorn med Hyper-V Manager-konsolen eller [storleksändring VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell-cmdlet.  Om du inte körs i en Windows-miljö bör det att använda qemu img konvertera (om det behövs) och ändra storlek på den virtuella Hårddisken.

> [!NOTE]
> Det finns ett känt fel i qemu img versioner > = 2.2.1 som resulterar i en felaktigt formaterad virtuell Hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda qemu img 2.2.0 eller lägre eller uppdatera till 2.6 eller senare. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Ändra storlek på den virtuella Hårddisken direkt med hjälp av verktyg som `qemu-img` eller `vbox-manage` kan resultera i en VHD som inte kan startas.  Därför rekommenderas att första konvertera den virtuella Hårddisken till en rå diskavbildning.  Om VM-avbildning har redan skapats som RÅDATA diskavbildning (standard för vissa hypervisor-program, till exempel KVM) kan du hoppa över det här steget:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Beräkna den nödvändiga storleken för diskavbildning så att den virtuella storleken justeras till 1MB.  Du följande kommandoskript bash kan hjälpa till med den här.  Skriptet använder ”`qemu-img info`” att identifiera virtuella storleken på disk image och beräknar sedan storleken till nästa 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Ändra storlek på raw disken med $rounded_size som angetts i skriptet ovan:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Nu kan konvertera RÅDATA disken till en virtuell Hårddisk med fast storlek:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Eller med qemu version **2.6 +** inkluderar den `force_size` alternativ:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux-Kernel-krav
Linux Integration Services (LIS)-drivrutiner för Hyper-V och Azure bidragit direkt till den överordnade Linux-kerneln. Många distributioner som innehåller de senaste Linux kernel-version (d.v.s. 3.x) redan har dessa drivrutiner eller annat sätt tillhandahålla anpassats versioner av drivrutinerna med deras kärnor.  De här drivrutinerna som uppdateras kontinuerligt i överordnad kernel med nya korrigeringar och funktioner, så när det är möjligt bör köra en [godkända distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som innehåller dessa korrigeringar och uppdateringar.

Om du använder en variant av Red Hat Enterprise Linux versioner **6.0 6.3**, måste du installera de senaste drivrutinerna för LIS för Hyper-V. Drivrutinerna kan hittas [på den här platsen](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Från och med RHEL **6.4 +** (och derivat) drivrutinerna LIS ingår redan i kernel och därför inga ytterligare Installationspaketen behövs för att köra dessa system på Azure.

Om en anpassad kernel krävs, rekommenderas att använda en nyare version av kernel (d.v.s. **3.8 +**). För dessa distributioner eller leverantörer som hanterar sina egna kernel möda kommer att behöva regelbundet backport LIS drivrutiner från överordnad kerneln till din anpassade kernel.  Även om du redan kör en relativt nya kernel-version, rekommenderas att hålla koll på eventuella överordnade korrigeringar i LIS drivrutiner och backport dem efter behov. LIS drivrutinens källfiler finns tillgängliga i den [sköter underhåll SJÄLVA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) filen i trädet Linux kernel källa:

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

Vid en mycket minsta avsaknaden av följande korrigeringsfiler har rapporterats orsaka problem på Azure och så de måste inkluderas i kernel. Den här listan är inte menat fullständig eller fullständig för alla distributioner:

* [ata_piix: skjuta upp diskar i Hyper-V-drivrutiner som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: kontot för transit paket i sökvägen för återställning](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: undvika användningen av WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: inaktivera skriva samma för RAID och drivrutiner för nätverkskort för virtuell värddator](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL-pekare avreferera-korrigering](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer fel kan resultera i i/o-låsning](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: skydda dig mot dubbla körning av __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux-agenten
Den [Azure Linux-agenten](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) krävs för att korrekt etablera en virtuell Linux-dator i Azure. Du kan hämta den senaste versionen, filen problem eller skicka pull-förfrågningar på den [Linux-agenten GitHub-repo-](https://github.com/Azure/WALinuxAgent).

* Linux-agenten släpps under Apache 2.0-licens. Många distributioner ange redan RPM eller deb paket för agenten och så i vissa fall detta kan du installera och uppdatera visningsläge.
* Azure Linux-agenten kräver Python v2.6 +.
* Agenten kräver också pyasn1 python-modulen. De flesta distributioner ger detta som ett separat paket som kan installeras.
* Azure Linux-agenten kanske inte kompatibelt med NetworkManager i vissa fall. Många av de RPM/Deb-paket som tillhandahålls av distributioner konfigurera NetworkManager som en konflikt i waagent-paketet och därmed kommer att avinstallera NetworkManager när du installerar Linux-agenten.
* Azure Linux-agenten måste vara ovanför den lägsta versionen som stöds, finns i den här artikeln [information](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Systemkrav för allmänna Linux

* Ändra raden kernel Start i GRUB eller GRUB2 för att inkludera följande parametrar. Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Förutom de som nämns ovan, rekommenderas att *ta bort* om de finns följande parametrar:
  
        rhgb quiet crashkernel=auto
  
    Grafisk och tyst Start inte är användbara i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Den `crashkernel` alternativet kan vara left konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på de virtuella datorn är mindre.

* Installera Azure Linux-Agent
  
    Azure Linux-agenten krävs för att etablera en Linux-avbildning på Azure.  Många distributioner ger agenten som ett RPM eller Deb-paket (paketet kallas vanligtvis 'WALinuxAgent' eller 'walinuxagent').  Agenten kan också installeras manuellt genom att följa stegen i den [Linux-agenten Guide](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.

* Skapa inte växlingsutrymme på OS-disk
  
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Kör följande kommandon för att ta bort etableringen av den virtuella datorn som ett sista steg:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > På Virtualbox kan du se följande fel när du har kört ' waagent-force - deprovision': `[Errno 5] Input/output error`. Det här felmeddelandet är inte viktigt och kan ignoreras.
  > 
  > 

* Du måste sedan att stänga av den virtuella datorn och överför den virtuella Hårddisken till Azure.


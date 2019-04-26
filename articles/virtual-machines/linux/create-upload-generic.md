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
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: e032f9a9772232d3a57a9672dc6c601354ecad43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327974"
---
# <a name="information-for-non-endorsed-distributions"></a>Information om icke-godkända distributioner
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure-plattformen serviceavtalet gäller för virtuella datorer som kör Linux-operativsystem endast när en av de [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) används. För dessa distributioner som stöds finns förkonfigurerade Linux-avbildningar i Azure Marketplace.

* [Linux på Azure - godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alla distributioner som körs på Azure har ett antal krav. Den här artikeln får inte vara omfattande, eftersom varje distribution är olika. Även om du uppfyller alla följande villkor kan du behöva avsevärt justera din Linux-systemet för att den ska fungera korrekt.

Vi rekommenderar att du börjar med en av de [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). I följande artiklar visar hur du förbereder de olika godkända Linux-distributioner som stöds på Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Den här artikeln handlar om allmänna riktlinjer för att köra Linux-distribution på Azure.

## <a name="general-linux-installation-notes"></a>Allmän Linux installationsinformation
* Hyper-V virtuell hårddisk (VHDX)-format stöds inte i Azure, endast *fast virtuell Hårddisk*.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) cmdlet. Om du använder VirtualBox väljer **fast storlek** i stället för standardvärdet (dynamiskt allokerad) när du skapar disken.
* Azure stöder bara virtuella datorer i generation 1. Du kan konvertera en virtuell dator i generation 1 från VHDX till VHD-format och dynamiskt expanderande till en fast storlek disk. Du kan inte ändra generering av en virtuell dator. Mer information finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Den maximala storleken som tillåts för den virtuella Hårddisken är 1,023 GB.
* När du installerar Linux-system rekommenderar vi att du använder standard partitioner i stället för logiska Volume Manager (LVM) som är standard för många installationer. Med hjälp av standard partitioner undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin är ansluten till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar.
* Kernel-stöd för att montera filsystem för UDF krävs. Vid första start på Azure skickas etableringskonfiguration för Linux-VM med hjälp av UDF-formaterad media som är kopplad till gästen. Azure Linux-agent måste montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux-kernel-versioner stöder tidigare än 2.6.37 inte NUMA på Hyper-V med större storlekar för Virtuella datorer. I detta fall främst påverkar äldre distributioner som använder den överordnade Red Hat 2.6.32 kernel och åtgärdades i Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). System som kör anpassade kernlar som är äldre än 2.6.37 eller RHEL-baserade kernlar som är äldre än 2.6.32-504 måste ange parametern Start `numa=off` kernel från kommandoraden i grub.conf. Mer information finns i [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk, enligt beskrivningen i följande steg.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1 MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1 MB före omvandlingen, enligt beskrivningen i följande steg.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installera kernel moduler utan Hyper-V
Azure körs på Hyper-V-hypervisor så Linux kräver vissa kernel-moduler för att köra i Azure. Om du har en virtuell dator som skapats utanför Hyper-V kanske Linux-installationsprogram inte med drivrutiner för Hyper-V inledande ramdisk (initrd eller initramfs), såvida inte den virtuella datorn identifierar att den körs på en Hyper-V-miljö. När du använder ett annat virtualiseringssystem (till exempel Virtualbox KVM och så vidare) för att förbereda din Linux-avbildning kan du behöva återskapa initrd så som på minst hv_vmbus och hv_storvsc kernel modulerna är tillgängliga på den första ramdisk.  Den här känt problem är för system baserat på den överordnade Red Hat-distributionen och eventuellt andra.

Mekanism för att återskapa initrd eller initramfs avbildningen kan variera beroende på vilken distribution. Rätt proceduren finns i dokumentation eller stöd för din distribution.  Följande är ett exempel för att återskapa initrd med hjälp av den `mkinitrd` verktyget:

1. Säkerhetskopiera den befintliga initrd-avbildningen:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Återskapa initrd med kernel-moduler hv_vmbus och hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Ändra storlek på virtuella hårddiskar
VHD-avbildningar på Azure måste ha en virtuell storlek justeras till 1 MB.  Virtuella hårddiskar som skapats med hjälp av Hyper-V är vanligtvis justerade korrekt.  Om den virtuella Hårddisken inte är justerad korrekt, får du ett felmeddelande som liknar följande när du försöker skapa en avbildning från en virtuell Hårddisk.

* VHD- http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd har en som inte stöds virtuella storlek 21475270656 byte. Storleken måste vara ett heltal (i MB).

I det här fallet, ändra storlek på den virtuella datorn med Hyper-V Manager-konsolen eller [storleksändring-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell-cmdlet.  Om du inte kör i en Windows-miljö, bör du använda `qemu-img` att konvertera (vid behov) och ändra storlek på den virtuella Hårddisken.

> [!NOTE]
> Det finns en [känt fel i qemu img](https://bugs.launchpad.net/qemu/+bug/1490611) versioner > = 2.2.1 som resulterar i en felaktigt formaterad virtuell Hårddisk. Problemet har åtgärdats i QEMU 2.6. Vi rekommenderar att du använder antingen `qemu-img` 2.2.0 eller lägre eller 2.6 eller senare.
> 

1. Ändra storlek på den virtuella Hårddisken direkt med hjälp av verktyg som `qemu-img` eller `vbox-manage` kan resultera i ett offlineläge VHD.  Vi rekommenderar att du först konvertera den virtuella Hårddisken till en rå diskavbildning.  Om avbildningen har skapats som en rå diskavbildning (standard för vissa hypervisor-program, till exempel KVM), kan du hoppa över det här steget.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Beräkna den nödvändiga storleken för diskavbildningen så att den virtuella storleken justeras till 1 MB.  Följande bash shell-skript använder `qemu-img info` fastställa virtuell storlek på diskavbildningen och beräknar sedan storleken till nästa 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Ändra storlek på raw disk med hjälp av `$rounded_size` som angetts ovan.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Nu kan konvertera den RÅA disken till en virtuell Hårddisk med fast storlek.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Eller, med qemu version 2.6 +, med den `force_size` alternativet.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux-Kernel-krav

Linux Integration Services (LIS)-drivrutiner för Hyper-V och Azure tillhandahålls direkt till den överordnade Linux-kerneln. Många distributioner som innehåller en ny Linux-kernel-version (till exempel 3.x) har redan de här drivrutinerna som är tillgängliga eller annars har anpassats versioner av de här drivrutinerna med sina kernlar.  De här drivrutinerna uppdateras ständigt i överordnade kerneln med nya korrigeringar och funktioner, så när det är möjligt rekommenderar vi att köra en [godkända distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som innehåller dessa korrigeringar och uppdateringar.

Om du kör en variant av Red Hat Enterprise Linux-versioner 6.0 6.3 och sedan måste du installera den [senaste drivrutiner för LIS för Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Från och med RHEL 6.4 + (och derivat) drivrutinerna LIS ingår redan i kerneln och paket så ingen ytterligare information behövs.

Om det krävs en anpassad kernel, rekommenderar vi en ny kernel-version (till exempel 3.8 +). För distributioner eller leverantörer som hanterar sina egna kernel, behöver du regelbundet backport LIS drivrutinerna från den överordnade kerneln din anpassade kerneln.  Även om du redan kör en relativt nytt kernel-version, rekommenderar vi att ha koll på någon uppströms korrigeringar med LIS drivrutiner och backport dem efter behov. Platserna för LIS drivrutinens källfiler anges i den [sköter underhåll SJÄLVA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) filen i trädet Linux-kernel källa:
```
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
```
Följande korrigeringar måste inkluderas i kerneln. Den här listan får inte vara klar för alla distributioner.

* [ata_piix: Skjut upp diskar till Hyper-V-drivrutiner som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Konto för transit paket i sökvägen för återställning](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: undvika användningen av WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Inaktivera skriva samma för RAID och drivrutiner för nätverkskort för virtuell värd](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL-pekare avreferera-korrigering](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer fel kan resultera i i/o-låsning](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: skydda mot dubbla körningen av __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux-agenten
Den [Azure Linux Agent](../extensions/agent-linux.md) `waagent` etablerar en Linux-dator i Azure. Du kan hämta den senaste versionen, filen problem eller skicka in pull-begäranden på den [Linux-agenten GitHub-lagringsplatsen](https://github.com/Azure/WALinuxAgent).

* Linux-agenten ingår i Apache 2.0-licens. Många distributioner anger redan RPM- eller deb paket för agenten och dessa paket kan enkelt installeras och uppdateras.
* Azure Linux Agent kräver Python v2.6 +.
* Agenten kräver också python-pyasn1-modulen. De flesta distributioner kan du ange den här modulen som ett separat paket som ska installeras.
* I vissa fall kanske Azure Linux Agent inte är kompatibla med NetworkManager. Många av de RPM/Deb-paket som tillhandahålls av distributioner konfigurera NetworkManager som en konflikt i waagent-paket. I dessa fall kan avinstalleras NetworkManager när du installerar Linux-agenten.
* Azure Linux Agent måste vara vid eller över den [lägsta version som stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>General Linux System Requirements

1. Ändra i kernel boot line i GRUB eller GRUB2 för att inkludera följande parametrar, så att alla konsolmeddelanden skickas till den första seriella porten. Dessa meddelanden kan hjälpa Azure support med felsökning av problem.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Vi rekommenderar också *tar bort* följande parametrar om de finns.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafiska och tyst start är inte användbart i en molnmiljö, där vi vill att alla loggar som skickas till den seriella porten. Den `crashkernel` alternativet kanske vänster som konfigureras vid behov, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med minst 128 MB, vilket kan vara problematiskt för mindre VM-storlekar.

1. Installera Azure Linux-agenten.
  
    Azure Linux Agent krävs för att etablera en Linux-avbildning på Azure.  Många distributioner ange agenten som en RPM- eller Deb-paketet (paketet kallas vanligtvis WALinuxAgent eller walinuxagent).  Agenten kan också installeras manuellt genom att följa stegen i den [Linux-agenten Guide](../extensions/agent-linux.md).

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Den här konfigurationen används vanligtvis som standard.

1. Skapa inte växlingsutrymme på OS-disken.
  
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. Ändra följande parametrar i /etc/waagent.conf när du har installerat Azure Linux Agent (steg 2 ovan).
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Kör följande kommandon för att avetablera den virtuella datorn.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > På Virtualbox kan du se följande fel efter att ha kört `waagent -force -deprovision` som säger `[Errno 5] Input/output error`. Det här felmeddelandet är inte viktigt och kan ignoreras.

* Stäng av den virtuella datorn och överför den virtuella Hårddisken till Azure.


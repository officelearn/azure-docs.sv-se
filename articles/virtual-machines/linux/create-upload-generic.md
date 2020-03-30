---
title: Skapa och ladda upp en Virtuell Linux-hårddisk
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Linux-operativsystem.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: f700dec6486bad9e7024d7c908a70dd0ff2b342c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066766"
---
# <a name="information-for-non-endorsed-distributions"></a>Information för icke-godkända distributioner

Azure-plattformenSLA gäller för virtuella datorer som kör Linux OS endast när en av de [godkända distributionerna](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) används. För dessa godkända distributioner finns förkonfigurerade Linux-avbildningar på Azure Marketplace.

* [Linux på Azure - Signerade distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alla distributioner som körs på Azure har ett antal förutsättningar. Den här artikeln kan inte vara omfattande, eftersom varje distribution är annorlunda. Även om du uppfyller alla kriterier nedan, kan du behöva avsevärt justera ditt Linux-system för att det ska fungera korrekt.

Vi rekommenderar att du börjar med en av [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Följande artiklar visar hur du förbereder de olika godkända Linuxdistributionerna som stöds på Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & öppnaSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Den här artikeln fokuserar på allmän vägledning för att köra din Linux-distribution på Azure.

## <a name="general-linux-installation-notes"></a>Allmänna installationsanteckningar för Linux
* VhdX-formatet (Hyper-V Virtual Hard Disk) stöds inte i Azure, bara *fast virtuell hårddisk*.  Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten [Konvertera-VHD.](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) Om du använder VirtualBox väljer du **Fast storlek** i stället för standard (dynamiskt allokerat) när du skapar disken.
* Azure stöder Gen1 (BIOS boot) & Gen2 (UEFI boot) virtuella datorer.
* Den maximala storleken för den virtuella hårddisken är 1 023 GB.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner, snarare än Logisk volymhanterare (LVM) som är standard för många installationer. Om du använder standardpartitioner undviks LVM-namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin är ansluten till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar.
* Kernel-stöd för montering av UDF-filsystem är nödvändigt. Vid första start på Azure skickas etableringskonfigurationen till Linux-vm med hjälp av UDF-formaterade media som är kopplat till gästen. Azure Linux-agenten måste montera UDF-filsystemet för att läsa dess konfiguration och etablera den virtuella datorn.
* Linux-kärnversioner tidigare än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med hjälp av uppströms Red Hat 2.6.32-kärnan och åtgärdades i Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). System som kör anpassade kärnor äldre än 2.6.37 eller RHEL-baserade kärnor äldre än 2.6.32-504 måste ange startparametern `numa=off` på kärnkommandoraden i grub.conf. Mer information finns i [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en växlingspartition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken, enligt beskrivningen i följande steg.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att rådiskstorleken är en multipel av 1 MB före konvertering, enligt beskrivningen i följande steg.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installera kärnmoduler utan Hyper-V
Azure körs på Hyper-V hypervisor, så Linux kräver vissa kärnmoduler för att köras i Azure. Om du har en virtuell dator som har skapats utanför Hyper-V kanske Linux-installationsprogrammet inte innehåller drivrutinerna för Hyper-V i den första ramdisken (initrd eller initramfs), såvida inte den virtuella datorn upptäcker att den körs på en Hyper-V-miljö. När du använder ett annat virtualiseringssystem (till exempel VirtualBox, KVM och så vidare) för att förbereda din Linux-avbildning kan du behöva återskapa initrd så att åtminstone hv_vmbus och hv_storvsc kärnmoduler är tillgängliga på den första ramdisken.  Detta kända problem är för system som bygger på uppströms Red Hat distribution, och eventuellt andra.

Mekanismen för att återskapa initrd eller initramfs bilden kan variera beroende på fördelningen. Läs dokumentationen eller supporten för distributionen för att få rätt procedur.  Här är ett exempel för att bygga `mkinitrd` om initrd med hjälp av verktyget:

1. Säkerhetskopiera den befintliga initrd-bilden:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Återskapa initrd med hv_vmbus och hv_storvsc kärnmoduler:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Ändra storlek på virtuella hårddiskar
VHD-avbildningar på Azure måste ha en virtuell storlek justerad till 1 MB.  Vanligtvis justeras virtuella hårddiskar som skapas med Hyper-V korrekt.  Om den virtuella hårddisken inte är korrekt justerad kan ett felmeddelande som liknar följande visas när du försöker skapa en bild från den virtuella hårddisken.

* DEN VHD\//\<http: mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd har en icke-stödd virtuell storlek på 21475270656 byte. Storleken måste vara ett heltal (i MBs).

I det här fallet ändrar du storlek på den virtuella datorn med hjälp av antingen Hyper-V [Manager-konsolen eller PowerShell-cmdleten Resize-VHD.](https://technet.microsoft.com/library/hh848535.aspx)  Om du inte kör i en Windows-miljö rekommenderar vi att du använder `qemu-img` för att konvertera (om det behövs) och ändra storlek på den virtuella hårddisken.

> [!NOTE]
> Det finns en [känd bugg i qemu-img-versioner](https://bugs.launchpad.net/qemu/+bug/1490611) >=2.2.1 som resulterar i en felaktigt formaterad virtuell hårddisk. Problemet har åtgärdats i QEMU 2.6. Vi rekommenderar `qemu-img` att du använder antingen 2.2.0 eller lägre, eller 2,6 eller högre.
> 

1. Ändra storlek på den virtuella hårddisken direkt med hjälp av verktyg som `qemu-img` eller `vbox-manage` kan resultera i en okovanlig virtuell hårddisk.  Vi rekommenderar att du först konverterar den virtuella hårddisken till en RAW-skivavbild.  Om vm-avbildningen skapades som en RAW-skivavbildning (standard för vissa hypervisorer, till exempel KVM), kan du hoppa över det här steget.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Beräkna diskavbildningens storlek så att den virtuella storleken justeras till 1 MB.  Följande bash shell-skript använder `qemu-img info` för att bestämma den virtuella storleken på diskavbildningen och beräknar sedan storleken till nästa 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Ändra storlek på den `$rounded_size` råa disken med som anges ovan.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Nu konvertera RAW-disken tillbaka till en fast storlek VHD.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Eller, med qemu version 2.6+, inkludera alternativet. `force_size`

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Krav på Linux-kärna

LIS-drivrutinerna (Linux Integration Services) för Hyper-V och Azure bidrar direkt till linuxkärnan uppströms. Många distributioner som innehåller en nyligen Linux-kärnversion (till exempel 3.x) har dessa drivrutiner redan tillgängliga, eller på annat sätt ger bakåtporterade versioner av dessa drivrutiner med sina kärnor.  Dessa drivrutiner uppdateras ständigt i uppströmskärnan med nya korrigeringar och funktioner, så när det är möjligt rekommenderar vi att du kör en [godkänd distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som innehåller dessa korrigeringar och uppdateringar.

Om du kör en variant av Red Hat Enterprise Linux versioner 6.0 till 6.3, måste du installera de [senaste LIS-drivrutinerna för Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Från och med RHEL 6.4+ (och derivat) ingår LIS-drivrutinerna redan i kärnan och därför behövs inga ytterligare installationspaket.

Om en anpassad kärna krävs rekommenderar vi en ny kärnversion (till exempel 3,8+). För distributioner eller leverantörer som underhåller sin egen kärna måste du regelbundet backportera LIS-drivrutinerna från uppströmskärnan till din anpassade kärna.  Även om du redan kör en relativt ny kärnversion rekommenderar vi starkt att du håller reda på eventuella uppströmskorrigeringar i LIS-drivrutinerna och bakåtportar dem efter behov. Platserna för LIS-drivrutinens källfiler anges i [MAINTAINERS-filen](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) i Linux-kärnkällans träd:
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
Följande korrigeringsfiler måste inkluderas i kärnan. Den här listan kan inte slutföras för alla distributioner.

* [ata_piix: skjut upp diskar till Hyper-V-drivrutinerna som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Konto för paket under överföring i RESET-sökvägen](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: undvik användning av WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Inaktivera SKRIV SAME för RAID- och virtuella värdkortsdrivrutiner](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NOLL-pekarens avreferencefix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ringbuffertfel kan resultera i I/O-frysning](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: skydda mot dubbel avrättning av __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux-agenten
[Azure Linux Agent](../extensions/agent-linux.md) `waagent` etablerar en virtuell Linux-dator i Azure. Du kan hämta den senaste versionen, filproblem eller skicka pull-begäranden på [Linux Agent GitHub-repo.](https://github.com/Azure/WALinuxAgent)

* Linux-agenten släpps under Apache 2.0-licensen. Många distributioner tillhandahåller redan RPM- eller .deb-paket för agenten, och dessa paket kan enkelt installeras och uppdateras.
* Azure Linux Agent kräver Python v2.6+.
* Agenten kräver också python-pyasn1-modulen. De flesta distributioner tillhandahåller den här modulen som ett separat paket som ska installeras.
* I vissa fall kanske Azure Linux-agenten inte är kompatibel med NetworkManager. Många av RPM/deb-paketen som tillhandahålls av distributioner konfigurerar NetworkManager som en konflikt med waagent-paketet. I dessa fall avinstalleras NetworkManager när du installerar Linux-agentpaketet.
* Azure Linux Agent måste vara på eller över den [minsta versionen som stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Allmänna krav på Linux-system

1. Ändra kärnstartlinjen i GRUB eller GRUB2 för att inkludera följande parametrar, så att alla konsolmeddelanden skickas till den första seriella porten. Dessa meddelanden kan hjälpa Azure-stöd med felsökning av eventuella problem.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Vi rekommenderar också att *du tar bort* följande parametrar om de finns.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafisk och tyst start är inte användbart i en molnmiljö, där vi vill att alla loggar skickas till serieporten. Alternativet `crashkernel` kan lämnas konfigurerat om det behövs, men observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med minst 128 MB, vilket kan vara problematiskt för mindre vm-storlekar.

1. Installera Azure Linux-agenten.
  
    Azure Linux-agenten krävs för att etablera en Linux-avbildning på Azure.  Många distributioner ger agenten som ett RPM- eller .deb-paket (paketet kallas vanligtvis WALinuxAgent eller walinuxagent).  Agenten kan också installeras manuellt genom att följa stegen i [Linux Agent Guide](../extensions/agent-linux.md).

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Den här konfigurationen är vanligtvis standard.

1. Skapa inte växlingsutrymme på OS-disken.
  
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (steg 2 ovan) ändrar du följande parametrar i /etc/waagent.conf efter behov.
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
   > På Virtualbox kan du se `waagent -force -deprovision` följande `[Errno 5] Input/output error`fel efter att ha kört som säger . Det här felmeddelandet är inte kritiskt och kan ignoreras.

* Stäng av den virtuella datorn och ladda upp den virtuella hårddisken till Azure.


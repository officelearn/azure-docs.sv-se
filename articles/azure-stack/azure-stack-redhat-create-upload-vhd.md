---
title: Skapa och ladda upp en Red Hat Enterprise Linux-VHD för användning i Azure Stack | Microsoft Docs
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller en Red Hat Linux-operativsystem.
services: azure-stack
documentationcenter: ''
author: JeffGoldner
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: ad0419cee3fc5c838d6d81adf9040432b9feaf07
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242237"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Förbered en Red Hat-baserad virtuell dator för Azure Stack

I den här artikeln får lära du dig att förbereda en virtuell dator för Red Hat Enterprise Linux (RHEL) för användning i Azure Stack. RHEL-versioner som beskrivs i den här artikeln är 7.1 +. Hypervisorer för förberedelse av som beskrivs i den här artikeln är Hyper-V, kernel-baserad virtuell dator (KVM) och VMware.

Red Hat Enterprise Linux supportinformation i [Red Hat och Azure Stack: Vanliga frågor och svar](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Förbered en Red Hat-baserad virtuell dator från Hyper-V Manager

Det här avsnittet förutsätter att du redan har en ISO-fil från Red Hat-webbplats och installerade RHEL-avbildning till en virtuell hårddisk (VHD). Mer information om hur du använder Hyper-V Manager för att installera en operativsystemavbildning finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>RHEL-installationsinformation

* Azure Stack har inte stöd för VHDX-formatet. Azure stöder endast fast virtuell Hårddisk. Du kan använda Hyper-V Manager för att konvertera disken till VHD-format eller du kan använda cmdleten convert-vhd. Om du använder VirtualBox välja **fast storlek** istället för standard dynamiskt allokerad alternativet när du skapar disken.
* Azure Stack stöder endast generering 1 virtuell datorer. Du kan konvertera en virtuell dator i generation 1 från VHDX till VHD-format och dynamiskt expanderande till en disk med fast storlek. Du kan inte ändra generering av en virtuell dator. Mer information finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Den maximala storleken som tillåts för den virtuella Hårddisken är 1,023 GB.
* När du installerar Linux-operativsystem rekommenderar vi att du använder standard partitioner i stället för logiska Volume Manager (LVM), vilket ofta är standard för många installationer. Denna praxis undviker LVM namnet står i konflikt med den klonade virtuella datorer, särskilt om du behöver att koppla en operativsystemdisk till en annan identisk virtuell dator för felsökning.
* Kernel-stöd för att montera filsystem för Universal Disk (UDF) krävs. Vid första start skickar UDF-formaterad mediet som är kopplad till gästen etablering konfigurationen till Linux-datorn. Azure Linux Agent måste montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Konfigurera inte swap-partition på operativsystemdisken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk. Mer information finns i följande steg.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1 MB. Vid konvertering från en rå disk till virtuell Hårddisk, måste du kontrollera att rådata diskens storlek är en multipel av 1 MB före omvandlingen. Mer information finns i stegen nedan.
* Azure Stack stöder inte cloud-init. Den virtuella datorn måste konfigureras med en version som stöds av Windows Azure Linux Agent (WALA).

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Förbered en virtuell RHEL 7-dator från Hyper-V Manager

1. Välj den virtuella datorn i Hyper-V Manager.

1. Klicka på **Connect** att öppna ett konsolfönster för den virtuella datorn.

1. Skapa eller redigera den `/etc/sysconfig/network` filen och Lägg till följande text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Skapa eller redigera den `/etc/sysconfig/network-scripts/ifcfg-eth0` filen och Lägg till följande text efter behov:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Se till att nätverkstjänsten startar när datorn startas genom att köra följande kommando:

    ```bash
    sudo systemctl enable network
    ```

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Om du vill göra den här ändringen, öppna `/etc/default/grub` i en textredigerare och ändra den `GRUB_CMDLINE_LINUX` parametern. Exempel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Den här konfigurationen ger också inaktiverar de nya namnkonventionerna för RHEL 7 för nätverkskort.

   Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Du kan lämna den `crashkernel` alternativet konfigureras om så önskas. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer. Vi rekommenderar att du tar bort följande parametrar:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. När du är klar redigering `/etc/default/grub`, kör följande kommando för att återskapa grub-konfigurationen:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas, som vanligtvis är standard. Ändra `/etc/ssh/sshd_config` att inkludera följande rad:

    ```sh
    ClientAliveInterval 180
    ```

1. Paketets WALinuxAgent `WALinuxAgent-<version>`, har överförts till lagringsplatsen för Red Hat-tillägg. Aktivera tillägg databasen genom att köra följande kommando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installera Azure Linux Agent genom att köra följande kommando:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisk som är kopplat till den virtuella datorn när den virtuella datorn har etablerats på Azure. Lokal resurs-disken är en tillfällig disk och kan tömmas när virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående steg kan du ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Om du vill avregistrera prenumerationen, kör du följande kommando:

    ```bash
    sudo subscription-manager unregister
    ```

1. Om du använder ett system som har distribuerats med hjälp av en företagscertifikatutfärdare, den virtuella RHEL-datorn inte litar på rotcertifikatet för Azure Stack. Du måste placera som i arkivet Betrodda rotcertifikatutfärdare. Se [att lägga till betrodda rotcertifikat till servern](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Klicka på **åtgärd** > **stänga** i Hyper-V Manager.

1. Konvertera den virtuella Hårddisken till en virtuell Hårddisk med funktionen Hyper-V Manager ”redigera disk” eller Convert-VHD PowerShell-kommandot med fast storlek. VHD för Linux är nu redo att överföras till Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Förbered en Red Hat-baserad virtuell dator från KVM

1. Ladda ned KVM-bild av RHEL 7 från Red Hat-webbplatsen. Den här proceduren använder RHEL 7 som exempel.

1. Ange ett rotlösenord.

    Generera ett krypterat lösenord och kopiera kommandots utdata:

    ```bash
    openssl passwd -1 changeme
    ```

   Ange ett rotlösenord med guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Ändra andra fält i rotanvändare från ””! till det krypterade lösenordet.

1. Skapa en virtuell dator i KVM från qcow2-avbildning. Ange typ av disk **qcow2**, och ange enhetsmodell för virtuellt nätverksgränssnittet till **virtio**. Sedan starta den virtuella datorn och logga in som rot.

1. Skapa eller redigera den `/etc/sysconfig/network` filen och Lägg till följande text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Skapa eller redigera den `/etc/sysconfig/network-scripts/ifcfg-eth0` filen och Lägg till följande text:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Se till att nätverkstjänsten startar när datorn startas genom att köra följande kommando:

    ```bash
    sudo systemctl enable network
    ```

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-lagringsplatsen genom att köra följande kommando:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Om du vill göra den här konfigurationen, öppna `/etc/default/grub` i en textredigerare och ändra den `GRUB_CMDLINE_LINUX` parametern. Exempel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Det här kommandot innebär också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Kommandot också stängs av de nya namnkonventionerna för RHEL 7 för nätverkskort

   Grafiska och tyst start är inte användbart i en molnmiljö där alla loggar skickas till den seriella porten. Du kan lämna den `crashkernel` alternativet konfigureras om så önskas. Den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer. Vi rekommenderar att du tar bort följande parametrar:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. När du är klar redigering `/etc/default/grub`, kör följande kommando för att återskapa grub-konfigurationen:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Lägg till Hyper-V-modulerna i initramfs.

    Redigera `/etc/dracut.conf` och lägga till innehåll:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Återskapa initramfs:

    ```bash
    dracut -f -v
    ```

1. Avinstallera cloud-init:

    ```bash
    yum remove cloud-init
    ```

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas:

    ```bash
    systemctl enable sshd
    ```

    Ändra /etc/ssh/sshd_config för att inkludera följande rader:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Paketets WALinuxAgent `WALinuxAgent-<version>`, har överförts till lagringsplatsen för Red Hat-tillägg. Aktivera tillägg databasen genom att köra följande kommando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installera Azure Linux Agent genom att köra följande kommando:

    ```bash
    yum install WALinuxAgent
    ```

    Aktivera tjänsten waagent:

    ```bash
    systemctl enable waagent.service
    ```

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisk som är kopplat till den virtuella datorn när den virtuella datorn har etablerats på Azure. Lokal resurs-disken är en tillfällig disk och kan tömmas när virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående steg kan du ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Avregistrera prenumerationen (vid behov) genom att köra följande kommando:

    ```bash
    subscription-manager unregister
    ```

1. Om du använder ett system som har distribuerats med hjälp av en företagscertifikatutfärdare, den virtuella RHEL-datorn inte litar på rotcertifikatet för Azure Stack. Du måste placera som i arkivet Betrodda rotcertifikatutfärdare. Se [att lägga till betrodda rotcertifikat till servern](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Stäng av den virtuella datorn i KVM.

1. Konvertera qcow2 avbildningen till VHD-format.

    > [!NOTE]
    > Det finns ett känt fel i qemu img versioner > = 2.2.1 som resulterar i en felaktigt formaterad virtuell Hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda qemu img 2.2.0 eller lägre eller uppdatera 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.

    Först konvertera avbildningen till obearbetat format:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Se till att storleken på raw-bild stämmer överens med 1 MB. I annat fall avrunda uppåt storlek för att anpassas till 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertera den råa disken till en fast storlek på virtuell Hårddisk:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Eller, med qemu version **2.6 +** inkluderar den `force_size` alternativet:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Förbered en Red Hat-baserad virtuell dator från VMware

Det här avsnittet förutsätter att du redan har installerat en RHEL-dator i VMware. Mer information om hur du installerar ett operativsystem i VMware finns i [installationsguide för VMware Guest Operating System](https://partnerweb.vmware.com/GOSIG/home.html).

* När du installerar Linux-operativsystem rekommenderar vi att du använder standard partitioner i stället för LVM, vilket ofta är standard för många installationer. På så sätt undviker LVM namnet står i konflikt med den klonade virtuella datorn, särskilt om en operativsystemdisk någonsin måste kopplas till en annan virtuell dator för felsökning. LVM eller RAID kan användas på datadiskar om du så föredrar.
* Konfigurera inte swap-partition på operativsystemdisken. Du kan konfigurera Linux-agenten för att skapa en växlingsfil på temporär disk. Du hittar mer information om detta i stegen nedan.
* När du skapar den virtuella hårddisken, Välj **Store virtuell disk som en enda fil**.

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Förbered en virtuell dator för RHEL 7 från VMware

1. Skapa eller redigera den `/etc/sysconfig/network` filen och Lägg till följande text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Skapa eller redigera den `/etc/sysconfig/network-scripts/ifcfg-eth0` filen och Lägg till följande text:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Se till att nätverkstjänsten startar när datorn startas genom att köra följande kommando:

    ```bash
    sudo chkconfig network on
    ```

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Om du vill göra den här ändringen, öppna `/etc/default/grub` i en textredigerare och ändra den `GRUB_CMDLINE_LINUX` parametern. Exempel:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Den här konfigurationen säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Den stängs även av de nya namnkonventionerna för RHEL 7 för nätverkskort. Vi rekommenderar dessutom att du tar bort följande parametrar:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Du kan lämna den `crashkernel` alternativet konfigureras om så önskas. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.

1. När du är klar redigering `/etc/default/grub`, kör följande kommando för att återskapa grub-konfigurationen:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Lägga till Hyper-V-modulerna i initramfs.

    Redigera `/etc/dracut.conf`, lägga till innehåll:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Återskapa initramfs:

    ```bash
    dracut -f -v
    ```

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas. Den här inställningen är vanligtvis standard. Ändra `/etc/ssh/sshd_config` att inkludera följande rad:

    ```sh
    ClientAliveInterval 180
    ```

1. Paketets WALinuxAgent `WALinuxAgent-<version>`, har överförts till lagringsplatsen för Red Hat-tillägg. Aktivera tillägg databasen genom att köra följande kommando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installera Azure Linux Agent genom att köra följande kommando:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisk som är kopplat till den virtuella datorn när den virtuella datorn har etablerats på Azure. Observera att den lokala resurs disken är en tillfällig disk och kan tömmas när virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående steg kan du ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Om du vill avregistrera prenumerationen, kör du följande kommando:

    ```bash
    sudo subscription-manager unregister
    ```

1. Om du använder ett system som har distribuerats med hjälp av en företagscertifikatutfärdare, den virtuella RHEL-datorn inte litar på rotcertifikatet för Azure Stack. Du måste placera som i arkivet Betrodda rotcertifikatutfärdare. Se [att lägga till betrodda rotcertifikat till servern](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Stäng av den virtuella datorn och konvertera filen VMDK till VHD-format.

    > [!NOTE]
    > Det finns ett känt fel i qemu img versioner > = 2.2.1 som resulterar i en felaktigt formaterad virtuell Hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda qemu img 2.2.0 eller lägre eller uppdatera 2.6 eller högre. Referens: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Först konvertera avbildningen till obearbetat format:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Se till att storleken på raw-bild stämmer överens med 1 MB. I annat fall avrunda uppåt storlek för att anpassas till 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertera den råa disken till en fast storlek på virtuell Hårddisk:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Eller, med qemu version **2.6 +** inkluderar den `force_size` alternativet:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Förbered en Red Hat-baserad virtuell dator från en ISO med hjälp av en kickstart-fil automatiskt

1. Skapa en kickstart-fil som innehåller följande innehåll och spara filen. Mer information om kickstart installation finns den [Kickstart installationsguide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Placera kickstart-fil där installation av systemet kan komma åt den.

1. Skapa en ny virtuell dator i Hyper-V Manager. På den **Anslut virtuell hårddisk** väljer **koppla en virtuell hårddisk senare**, och slutför guiden Ny virtuell dator.

1. Öppna inställningarna för virtuella datorer:

    a. Koppla en ny virtuell hårddisk till den virtuella datorn. Se till att välja **VHD-Format** och **fast storlek**.

    b. Koppla installationen ISO från DVD-enheten.

    c. Ange BIOS för start från CD: N.

1. Startar den virtuella datorn. När installationsguiden visas trycker du **fliken** konfigurera startalternativ för.

1. Ange `inst.ks=<the location of the kickstart file>` i slutet av startalternativ och tryck på **RETUR**.

1. Vänta tills installationen är klar. När det är klart, Stäng den virtuella datorn automatiskt. VHD för Linux är nu redo att överföras till Azure.

## <a name="known-issues"></a>Kända problem

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V-drivrutinen kan inte ingå i den första RAM-disken när du använder en icke-Hyper-V-hypervisor

I vissa fall kan kanske Linux installationsprogram inte innehåller drivrutiner för Hyper-V i den första RAM-disken (initrd eller initramfs) såvida inte Linux upptäcker att den körs i en Hyper-V-miljö.

När du använder ett annat virtualiseringssystem (det vill säga Virtualbox, Xen, osv.) för att förbereda din Linux-avbildning kan du behöva återskapa initrd för att säkerställa att minst de hv_vmbus och hv_storvsc kernel-modulerna är tillgängliga på den första disken i RAM-minne. Det här är ett känt problem minst på system som baseras på den överordnade Red Hat-distributionen.

Lägg till Hyper-V-modulerna i initramfs och återskapa den för att lösa problemet:

Redigera `/etc/dracut.conf`, och Lägg till följande innehåll:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

Återskapa initramfs:

    ```bash
    dracut -f -v
    ```

Mer information finns i [återskapa initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att använda dina Red Hat Enterprise Linux-virtuell hårddisk för att skapa nya virtuella datorer i Azure Stack. Om det här är första gången som du laddar upp VHD-filen till Azure Stack finns i [skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md).

Läs mer om hypervisor-program som är certifierade för att köra Red Hat Enterprise Linux, [Red Hat-webbplatsen](https://access.redhat.com/certified-hypervisors).

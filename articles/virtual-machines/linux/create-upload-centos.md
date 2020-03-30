---
title: Skapa och ladda upp en CentOS-baserad Linux VHD
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett CentOS-baserat Linux-operativsystem.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066787"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Förbereda en CentOS-baserad virtuell dator för Azure

Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett CentOS-baserat Linux-operativsystem.

* [Förbereda en virtuell CentOS 6.x virtuell dator för Azure](#centos-6x)
* [Förbereda en virtuell CentOS 7.0+-dator för Azure](#centos-70)


## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har installerat ett CentOS-operativsystem (eller liknande derivat) till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns [i Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**CentOS installationsanteckningar**

* Se även [Allmänna Linux-installationsanteckningar](create-upload-generic.md#general-linux-installation-notes) för fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hårddisk**.  Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten konvertera-vhd. Om du använder VirtualBox innebär det att välja **Fast storlek** i motsats till den standard som tilldelats dynamiskt när disken skapas dynamiskt.
* När du installerar Linux-systemet *rekommenderas* att du använder standardpartitioner i stället för LVM (ofta standard för många installationer). Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar.
* Kernel-stöd för montering av UDF-filsystem krävs. Vid första start på Azure skickas etableringskonfigurationen till Linux VM via UDF-formaterade media som är kopplat till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa dess konfiguration och etablera den virtuella datorn.
* Linux-kärnversioner under 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med hjälp av uppströms Red Hat 2.6.32-kärnan och åtgärdades i RHEL 6.6 (kernel-2.6.32-504). System som kör anpassade kärnor som är äldre än 2.6.37 eller RHEL-baserade kärnor äldre än 2.6.32-504 måste ange startparametern `numa=off` på kärnkommandoraden i grub.conf. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en växlingspartition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att raw-diskstorleken är en multipel av 1 MB före konvertering. Mer information finns i [Linux Installationsanteckningar.](create-upload-generic.md#general-linux-installation-notes)

## <a name="centos-6x"></a>CentOS 6.x

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.

3. I CentOS 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Skapa eller redigera `/etc/sysconfig/network` filen och lägg till följande text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0` filen och lägg till följande text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ändra udev-regler för att undvika att generera statiska regler för Ethernet-gränssnitten.Modify udev rules to avoid generating static rules for the Ethernet interface(s). Dessa regler kan orsaka problem vid kloning av en virtuell dator i Microsoft Azure eller Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

    ```bash
    sudo chkconfig network on
    ```

8. Om du vill använda OpenLogic-speglarna som finns i Azure-datacenter, `/etc/yum.repos.d/CentOS-Base.repo` ersätter du filen med följande databaser.  Detta kommer också att lägga till **[openlogic]** databasen som innehåller ytterligare paket som Azure Linux-agenten:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Resten av den här guiden förutsätter `[openlogic]` att du använder åtminstone repo, som kommer att användas för att installera Azure Linux-agenten nedan.

9. Lägg till följande rad i /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Kör följande kommando för att rensa de aktuella yum-metadata och uppdatera systemet med de senaste paketen:

    ```bash
    yum clean all
    ```

    Om du inte skapar en avbildning för en äldre version av CentOS rekommenderar vi att du uppdaterar alla paket till det senaste:

    ```bash
    sudo yum -y update
    ```

    En omstart kan krävas när du har kört det här kommandot.

11. (Valfritt) Installera drivrutinerna för Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Steget **krävs** för CentOS 6.3 och tidigare, och valfritt för senare versioner.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Du kan också följa instruktionerna för manuell installation på [LIS-nedladdningssidan](https://www.microsoft.com/download/details.aspx?id=51612) för att installera varvtalet på den virtuella datorn.

12. Installera Azure Linux-agenten och beroenden. Starta och aktivera waagent-tjänsten:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    WALinuxAgent-paketet tar bort NetworkManager- och NetworkManager-gnome-paketen om de inte redan har tagits bort enligt beskrivningen i steg 3.

13. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra `/boot/grub/menu.lst` detta, öppna i en textredigerare och se till att standardkärnan innehåller följande parametrar:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem.

    Utöver ovanstående rekommenderas att du *tar bort* följande parametrar:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten.  Alternativet `crashkernel` kan lämnas konfigurerat om så önskas, men observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre vm-storlekarna.

    > [!Important]
    > CentOS 6.5 och tidigare måste `numa=off`också ställa in kärnparametern . Se Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.

15. Skapa inte växlingsutrymme på OS-disken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se `/etc/waagent.conf` föregående steg) ändrar du följande parametrar på lämpligt sätt:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.



## <a name="centos-70"></a>CentOS 7,0+

**Förändringar i CentOS 7 (och liknande derivat)**

Att förbereda en virtuell CentOS 7-dator för Azure påminner mycket om CentOS 6, men det finns flera viktiga skillnader värda att notera:

* NetworkManager-paketet står inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att det inte tas bort.
* GRUB2 används nu som standard bootloader, så proceduren för redigering av kärnparametrar har ändrats (se nedan).
* XFS är nu standardfilsystemet. Filsystemet ext4 kan fortfarande användas om så önskas.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.

3. Skapa eller redigera `/etc/sysconfig/network` filen och lägg till följande text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0` filen och lägg till följande text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Ändra udev-regler för att undvika att generera statiska regler för Ethernet-gränssnitten.Modify udev rules to avoid generating static rules for the Ethernet interface(s). Dessa regler kan orsaka problem vid kloning av en virtuell dator i Microsoft Azure eller Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Om du vill använda OpenLogic-speglarna som finns i Azure-datacenter, `/etc/yum.repos.d/CentOS-Base.repo` ersätter du filen med följande databaser.  Detta kommer också att lägga till **[openlogic]** databasen som innehåller paket för Azure Linux-agenten:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Resten av den här guiden förutsätter `[openlogic]` att du använder åtminstone repo, som kommer att användas för att installera Azure Linux-agenten nedan.

7. Kör följande kommando för att rensa de aktuella yum-metadata och installera eventuella uppdateringar:

    ```bash
    sudo yum clean all
    ```

    Om du inte skapar en avbildning för en äldre version av CentOS rekommenderar vi att du uppdaterar alla paket till det senaste:

    ```bash
    sudo yum -y update
    ```

    En omstart kanske krävs efter att ha kört det här kommandot.

8. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra `/etc/default/grub` detta, öppna i `GRUB_CMDLINE_LINUX` en textredigerare och redigera parametern, till exempel:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Det stänger också av de nya Namngivningskonventionerna centOS 7 för nätverkskort. Utöver ovanstående rekommenderas att du *tar bort* följande parametrar:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Alternativet `crashkernel` kan lämnas konfigurerat om så önskas, men observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre vm-storlekarna.

9. När du är `/etc/default/grub` klar redigering per ovan, kör följande kommando för att återskapa grub konfiguration:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Om du skapar bilden från **VMware, VirtualBox eller KVM:** Se till att Hyper-V-drivrutinerna ingår i initramfs:

    Redigera `/etc/dracut.conf`, lägg till innehåll:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Bygg om initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Installera Azure Linux-agenten och beroenden:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Skapa inte växlingsutrymme på OS-disken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se `/etc/waagent.conf` föregående steg) ändrar du följande parametrar på lämpligt sätt:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att använda din virtuella CentOS Linux-hårddisk för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

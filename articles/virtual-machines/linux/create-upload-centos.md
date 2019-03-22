---
title: Skapa och ladda upp en CentOS-baserade Linux-VHD i Azure
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller en CentOS-baserade operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: 4e32d2357636cb488d3a58b78b025860da3f74c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091366"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Förbered en CentOS-baserad virtuell dator för Azure

* [Förbered en CentOS 6.x virtuell dator för Azure](#centos-6x)
* [Förbered en CentOS 7.0 + virtuell dator för Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat en CentOS (eller liknande härledda) Linux-operativsystem till en virtuell hårddisk. Det finns flera olika verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning, till exempel Hyper-V. Anvisningar finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**CentOS-installationsinformation**

* Se även [allmän information för Linux-Installation](create-upload-generic.md#general-linux-installation-notes) fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten convert-vhd. Om du använder VirtualBox innebär det att välja **fast storlek** istället för standard dynamiskt allokerade när du skapar disken.
* När du installerar Linux-systemet är det *rekommenderas* att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar.
* Kernel-stöd för att montera filsystem för UDF krävs. Vid första start på Azure skickas etableringskonfiguration för Linux-VM via UDF-formaterad medier som är kopplad till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux-kernel-versioner under 2.6.37 stöder inte NUMA på Hyper-V med större storlekar för Virtuella datorer. I detta fall främst påverkar äldre distributioner som använder den överordnade Red Hat 2.6.32 kernel och åtgärdades i RHEL 6.6 (kernel-2.6.32-504). System som kör anpassade kernlar som är äldre än 2.6.37 eller RHEL-baserade kernlar som är äldre än 2.6.32-504 måste ange parametern Start `numa=off` på kommandoraden i grub.conf kernel. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.

## <a name="centos-6x"></a>CentOS 6.x

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Connect** att öppna ett konsolfönster för den virtuella datorn.

3. CentOS 6 och kan NetworkManager påverka Azure Linux-agent. Avinstallera det här paketet genom att köra följande kommando:

        # sudo rpm -e --nodeps NetworkManager

4. Skapa eller redigera filen `/etc/sysconfig/network` och Lägg till följande text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Skapa eller redigera filen `/etc/sysconfig/network-scripts/ifcfg-eth0` och Lägg till följande text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Se till att nätverkstjänsten startar när datorn startas genom att köra följande kommando:

        # sudo chkconfig network on

8. Om du vill använda OpenLogic-speglingar som finns i Azure-datacenter och sedan ersätta den `/etc/yum.repos.d/CentOS-Base.repo` fil med följande databaser.  Detta lägger också till den **[openlogic]** lagringsplats som innehåller ytterligare paket som till exempel Azure Linux-agenten:

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

    > [!Note]
    > Resten av den här guiden kommer förutsätter att du använder minst `[openlogic]` lagringsplats, som används för att installera Azure Linux-agenten nedan.

9. Lägg till följande rad i /etc/yum.conf:

        http_caching=packages

10. Kör följande kommando för att ta bort den aktuella yum-metadata och uppdatera systemet med de senaste paketen:

        # yum clean all

    Såvida du inte skapar en avbildning för en äldre version av CentOS, bör du uppdatera alla paket till senast:

        # sudo yum -y update

    En omstart kan krävas när du har kört det här kommandot.

11. (Valfritt) Installera drivrutinerna för Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Steget är **krävs** för CentOS 6.3 och tidigare och en valfri för senare versioner.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Du kan också följa anvisningarna för manuell installation på den [LIS hämtningssidan](https://go.microsoft.com/fwlink/?linkid=403033) installera RPM till den virtuella datorn.

12. Installera Azure Linux Agent och beroenden:

        # sudo yum install python-pyasn1 WALinuxAgent

    Paketets WALinuxAgent tar bort NetworkManager och NetworkManager gör väldigt lätt paket om de inte har redan tagits bort enligt beskrivningen i steg 3.

13. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Gör detta genom att öppna `/boot/grub/menu.lst` i en textredigerare och se till att standardkernel innehåller följande parametrar:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem.

    Förutom ovanstående rekommenderar vi att du *ta bort* följande parametrar:

        rhgb quiet crashkernel=auto

    Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.  Den `crashkernel` alternativet kanske vänster konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på mindre storlekar för Virtuella datorer.

    > [!Important]
    > CentOS 6.5 och tidigare måste också ange parametern kernel `numa=off`. See Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.

15. Skapa inte växlingsutrymme på OS-disken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

- - -

## <a name="centos-70"></a>CentOS 7.0+

**Ändringar i CentOS 7 (och liknande derivat)**

Förbereda en virtuell CentOS 7-dator för Azure är mycket lik CentOS 6, men det finns flera viktiga skillnader att tänka på:

* NetworkManager paketet är inte längre i konflikt med Azure Linux-agent. Det här paketet installeras som standard och vi rekommenderar att den inte tas bort.
* GRUB2 används nu som standard-startprogrammet så metoderna för att redigera kernel parametrar har ändrats (se nedan).
* XFS är nu standardfilsystemet. Filsystemet ext4 kan fortfarande användas om du vill.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Connect** att öppna ett konsolfönster för den virtuella datorn.

3. Skapa eller redigera filen `/etc/sysconfig/network` och Lägg till följande text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Skapa eller redigera filen `/etc/sysconfig/network-scripts/ifcfg-eth0` och Lägg till följande text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Om du vill använda OpenLogic-speglingar som finns i Azure-datacenter och sedan ersätta den `/etc/yum.repos.d/CentOS-Base.repo` fil med följande databaser.  Detta lägger också till den **[openlogic]** lagringsplats som innehåller paket för Azure Linux-agenten:

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

    > [!Note]
    > Resten av den här guiden kommer förutsätter att du använder minst `[openlogic]` lagringsplats, som används för att installera Azure Linux-agenten nedan.

7. Kör följande kommando för att ta bort den aktuella yum-metadata och installera relevanta uppdateringar:

        # sudo yum clean all

    Såvida du inte skapar en avbildning för en äldre version av CentOS, bör du uppdatera alla paket till senast:

        # sudo yum -y update

    En omstart kanske krävs när du har kört det här kommandot.

8. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Gör detta genom att öppna `/etc/default/grub` i en textredigerare och redigera den `GRUB_CMDLINE_LINUX` parameter, till exempel:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Den stängs även av de nya namnkonventionerna för CentOS 7 för nätverkskort. Förutom ovanstående rekommenderar vi att du *ta bort* följande parametrar:

        rhgb quiet crashkernel=auto

    Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Den `crashkernel` alternativet kanske vänster konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på mindre storlekar för Virtuella datorer.

9. När du är klar redigering `/etc/default/grub` per ovan, kör du följande kommando för att återskapa grub-konfigurationen:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Om att skapa avbildningen från **VMware, VirtualBox eller KVM:** Kontrollera att Hyper-V-drivrutiner som ingår i initramfs:

    Redigera `/etc/dracut.conf`, lägga till innehåll:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Återskapa initramfs:

        # sudo dracut -f -v

11. Installera Azure Linux Agent och beroenden:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Skapa inte växlingsutrymme på OS-disken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att använda din virtuella hårddisk CentOS Linux för att skapa nya virtuella datorer i Azure. Om detta är första gången som du laddar upp VHD-filen till Azure, se [skapa en Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

---
title: "Skapa och ladda upp en baserat på CentOS Linux VHD i Azure"
description: "Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller en baserat på CentOS Linux-operativsystem."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 010f4b05b35fa1f31c14f34a5fae9298fcd831e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Förbered en CentOS-baserad virtuell dator för Azure
* [Förbered en CentOS 6.x virtuell dator för Azure](#centos-6x)
* [Förbered en CentOS 7.0 + virtuell dator för Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har installerat en CentOS (eller liknande derivat) Linux-operativsystem till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

**CentOS installationsinformation**

* Se även [allmänna Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer tips om hur du förbereder Linux för Azure.
* VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet convert-vhd. Om du använder VirtualBox innebär detta att välja **fast storlek** till skillnad från standardvärdet dynamiskt allokerade när skapas.
* När du installerar Linux-system är *rekommenderas* att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan identisk virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar.
* Kernel-stöd för att montera UDF-filsystem krävs. Vid första start på Azure skickas etablering konfigurationen för Linux-VM via UDF-formaterad media som är anslutna till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux kernel-versioner än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar huvudsakligen äldre distributioner med hjälp av den överordnade Red Hat 2.6.32 kernel och åtgärdades i RHEL 6.6 (kernel-2.6.32-504). System som kör anpassade kernlar som är äldre än 2.6.37 eller RHEL-baserade kernlar som är äldre än 2.6.32-504 måste ange parametern Start `numa=off` på kommandoraden i grub.conf kernel. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en byte-partition på OS-disk. Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken.  Mer information om detta finns i stegen nedan.
* Alla de virtuella hårddiskarna måste ha storlekar som multiplar av 1 MB.

## <a name="centos-6x"></a>CentOS 6.x

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** att öppna ett konsolfönster för den virtuella datorn.

3. CentOS 6, kan NetworkManager störa Azure Linux-agenten. Avinstallera paketet genom att köra följande kommando:
   
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

6. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. De här reglerna kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Kontrollera nätverkstjänsten startar när datorn startas genom att köra följande kommando:
   
        # sudo chkconfig network on

8. Om du vill använda OpenLogic speglingen som finns i Azure-datacenter och Ersätt den `/etc/yum.repos.d/CentOS-Base.repo` fil med följande databaser.  Detta lägger också till den **[openlogic]** databasen som innehåller ytterligare paket, till exempel Azure Linux-agenten:

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

    >[!Note]
    Resten av den här guiden kommer anta att du använder minst `[openlogic]` lagringsplatsen som används för att installera Azure Linux-agenten nedan.


9. Lägg till följande rad i /etc/yum.conf:
    
        http_caching=packages

10. Kör följande kommando för att rensa aktuella yum metadata och uppdatera systemet med de senaste paket:
    
        # yum clean all

    Om du skapar en avbildning för en äldre version av CentOS, bör uppdatera alla paket till senast:

        # sudo yum -y update

    En omstart kan krävas när du har kört kommandot.

11. (Valfritt) Installera drivrutiner för Linux Integration Services (LIS).
   
    >[!IMPORTANT]
    Steget är **krävs** för CentOS 6.3 och tidigare och en valfri för senare versioner.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Du kan också följa instruktioner för manuell installation på den [LIS hämtningssidan](https://go.microsoft.com/fwlink/?linkid=403033) att installera RPM på den virtuella datorn.
 
12. Installera Azure Linux-agenten och beroenden:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    Paketets WALinuxAgent tar bort NetworkManager och NetworkManager gör väldigt lätt paket om de inte har redan tagits bort enligt beskrivningen i steg 3.


13. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Det gör du genom att öppna `/boot/grub/menu.lst` i en textredigerare och se till att standard-kärnan innehåller följande parametrar:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem.
    
    Förutom de som nämns ovan, rekommenderas att *ta bort* följande parametrar:
    
        rhgb quiet crashkernel=auto
    
    Grafisk och tyst Start inte är användbara i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.  Den `crashkernel` alternativet kan vara left konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på de virtuella datorn är mindre.

    >[!Important]
    CentOS 6.5 och tidigare måste också ange parametern kernel `numa=off`. Se Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.

15. Skapa inte växlingsutrymme på OS-disk.
    
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.


- - -
## <a name="centos-70"></a>CentOS 7.0 +
**Ändringar i CentOS 7 (och liknande produkter)**

Förbereda en virtuell dator för CentOS 7 för Azure liknar CentOS 6, men det finns flera viktiga skillnader noteras:

* NetworkManager paketet inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att den inte tas bort.
* GRUB2 används nu som standard-startprogrammet så proceduren för att redigera kernel parametrar har ändrats (se nedan).
* XFS är nu filsystemet. Filsystemet ext4 kan fortfarande användas om du vill.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** att öppna ett konsolfönster för den virtuella datorn.

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

5. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. De här reglerna kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Om du vill använda OpenLogic speglingen som finns i Azure-datacenter och Ersätt den `/etc/yum.repos.d/CentOS-Base.repo` fil med följande databaser.  Detta lägger också till den **[openlogic]** databasen som innehåller paket för Azure Linux-agenten:
   
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

    >[!Note]
    Resten av den här guiden kommer anta att du använder minst `[openlogic]` lagringsplatsen som används för att installera Azure Linux-agenten nedan.

7. Kör följande kommando för att rensa aktuella yum metadata och installera uppdateringar:
   
        # sudo yum clean all

    Om du skapar en avbildning för en äldre version av CentOS, bör uppdatera alla paket till senast:

        # sudo yum -y update

    En omstart krävs kanske köra det här kommandot.

8. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Det gör du genom att öppna `/etc/default/grub` i en textredigerare och redigera den `GRUB_CMDLINE_LINUX` parameter, till exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Även stängs av nya CentOS 7 namnkonventionerna för nätverkskort. Förutom de som nämns ovan, rekommenderas att *ta bort* följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst Start inte är användbara i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten. Den `crashkernel` alternativet kan vara left konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på de virtuella datorn är mindre.

9. När du är klar redigera `/etc/default/grub` per ovan, kör du följande kommando för att återskapa grub konfigurationen:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Om du skapar bilden från **VMWare, VirtualBox eller KVM:** Kontrollera Hyper-V-drivrutiner som ingår i initramfs:
   
   Redigera `/etc/dracut.conf`, lägga till innehåll:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Återskapa initramfs:
   
        # sudo dracut –f -v

11. Installera Azure Linux-agenten och beroenden:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Skapa inte växlingsutrymme på OS-disk.
   
   Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i `/etc/waagent.conf` på rätt sätt:
   
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att använda din virtuella hårddisk CentOS Linux för att skapa nya virtuella datorer i Azure. Om att du överföra VHD-filen till Azure finns i steg 2 och 3 i [skapa och ladda upp en virtuell hårddisk som innehåller Linux-operativsystem](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


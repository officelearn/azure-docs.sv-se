---
title: Skapa och ladda upp en Red Hat Enterprise Linux VHD för användning i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Red Hat Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: cd0a71c60930e3eb659255a23cdb03360730f2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060736"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Förbereda en Red Hat-baserad virtuell dator för Azure
I den här artikeln får du lära dig hur du förbereder en virtuell Red Hat Enterprise Linux -dator (RHEL) för användning i Azure. De versioner av RHEL som beskrivs i den här artikeln är 6,7+ och 7,1+. De hypervisorer som beskrivs i den här artikeln är Hyper-V, kärnbaserad virtuell dator (KVM) och VMware. Mer information om behörighetskrav för att delta i Red Hats Cloud Access-program finns på [Red Hats Cloud Access-webbplats](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) och [Köra RHEL på Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Olika sätt att automatisera byggandet av RHEL-avbildningar finns i [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Förbereda en virtuell dator med Red Hat från Hyper-V Manager

### <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har fått en ISO-fil från Red Hat-webbplatsen och installerat RHEL-avbildningen på en virtuell hårddisk (VHD). Mer information om hur du använder Hyper-V Manager för att installera en operativsystemavbildning finns i [Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**RHEL-installationsanteckningar**

* Azure stöder inte VHDX-formatet. Azure stöder endast fast virtuell hårddisk. Du kan använda Hyper-V-hanteraren för att konvertera disken till VHD-format, eller så kan du använda cmdleten konvertera-vhd. Om du använder VirtualBox väljer du **Fast storlek** i motsats till standardalternativet dynamiskt allokerat när du skapar disken.
* Azure stöder Gen1 (BIOS boot) & Gen2 (UEFI boot) virtuella datorer.
* Den maximala storleken som är tillåten för den virtuella hårddisken är 1 023 GB.
* Logisk volymhanterare (LVM) stöds och kan användas på OS-disken eller datadiskarna i virtuella Azure-datorer. I allmänhet rekommenderas dock att använda standardpartitioner på OS-disken i stället för LVM. Den här metoden undviker LVM-namnkonflikter med klonade virtuella datorer, särskilt om du någonsin behöver koppla en operativsystemdisk till en annan identisk virtuell dator för felsökning. Se även [LVM-](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [RAID-dokumentation.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Kärnstöd för montering av UDF-filsystem (Universal Disk Format) krävs. Vid första start på Azure skickar UDF-formaterade media som är kopplat till gästen etableringskonfigurationen till den virtuella Linux-datorn. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa dess konfiguration och etablera den virtuella datorn.
* Konfigurera inte en växlingspartition på operativsystemdisken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken.  Mer information om detta finns i följande steg.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att raw-diskstorleken är en multipel av 1 MB före konvertering. Mer information finns i stegen nedan. Se även [Linux Installation Notes](create-upload-generic.md#general-linux-installation-notes) för mer information.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Förbereda en virtuell RHEL 6-dator från Hyper-V Manager

1. Välj den virtuella datorn i Hyper-V Manager.

1. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.

1. I RHEL 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Skapa eller `/etc/sysconfig/network` redigera filen och lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att generera statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # sudo chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra `/boot/grub/menu.lst` den här ändringen, öppna i en textredigerare och se till att standardkärnan innehåller följande parametrar:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem.
    
    Dessutom rekommenderar vi att du tar bort följande parametrar:
    
        rhgb quiet crashkernel=auto
    
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten.  Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer. Den här konfigurationen kan vara problematisk på mindre storlekar på virtuella datorer.


1. Kontrollera att SSH-servern (Secure Shell) är installerad och konfigurerad för att starta vid start, vilket vanligtvis är standard. Ändra /etc/ssh/sshd_config för att inkludera följande rad:

        ClientAliveInterval 180

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Om du installerar PAKETEN WALinuxAgent tas NetworkManager- och NetworkManager-gnome-paketen bort om de inte redan har tagits bort i steg 3.

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående steg ändrar du följande parametrar i /etc/waagent.conf på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Avregistrera prenumerationen (om det behövs) genom att köra följande kommando:

        # sudo subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klicka på > **Åtgärdsavstängning** i Hyper-V-hanteraren. **Action** Din virtuella Linux-hårddisk är nu klar att överföras till Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Förbereda en virtuell RHEL 7-dator från Hyper-V Manager

1. Välj den virtuella datorn i Hyper-V Manager.

1. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.

1. Skapa eller `/etc/sysconfig/network` redigera filen och lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. Om du vill `/etc/default/grub` göra den här ändringen `GRUB_CMDLINE_LINUX` öppnar du i en textredigerare och redigerar parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Den här konfigurationen inaktiverar också de nya namngivningskonventionerna för RHEL 7 för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.

1. När du har `/etc/default/grub`redigerat klart kör du följande kommando för att återskapa grub-konfigurationen:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start, vilket vanligtvis är standard. Ändra `/etc/ssh/sshd_config` så att följande rad ingår:

        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående `/etc/waagent.conf` steg ändrar du följande parametrar på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Om du vill avregistrera prenumerationen kör du följande kommando:

        # sudo subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klicka på > **Åtgärdsavstängning** i Hyper-V-hanteraren. **Action** Din virtuella Linux-hårddisk är nu klar att överföras till Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Förbereda en Red Hat-baserad virtuell dator från KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Förbereda en virtuell RHEL 6-dator från KVM

1. Ladda ner KVM-bilden på RHEL 6 från Red Hats webbplats.

1. Ange ett rotlösenord.

    Generera ett krypterat lösenord och kopiera utdata från kommandot:

        # openssl passwd -1 changeme

    Ange ett rotlösenord med gästfisk:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Ändra det andra fältet för rotanvändaren från "!!" till det krypterade lösenordet.

1. Skapa en virtuell dator i KVM från qcow2-avbildningen. Ställ in disktypen på **qcow2**och ställ in den virtuella nätverksgränssnittsenhetsmodellen på **virtio**. Starta sedan den virtuella datorn och logga in som root.

1. Skapa eller `/etc/sysconfig/network` redigera filen och lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att generera statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra `/boot/grub/menu.lst` den här konfigurationen, öppna i en textredigerare och se till att standardkärnan innehåller följande parametrar:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem.
    
    Dessutom rekommenderar vi att du tar bort följande parametrar:
    
        rhgb quiet crashkernel=auto
    
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.


1. Lägg till Hyper-V-moduler i initramfs:  

    Redigera `/etc/dracut.conf`och lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Bygg om initramfs:

        # dracut -f -v

1. Avinstallera cloud-init:

        # yum remove cloud-init

1. Kontrollera att SSH-servern är installerad och konfigurerad för start vid start:

        # chkconfig sshd on

    Ändra /etc/ssh/sshd_config för att inkludera följande rader:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående steg ändrar du följande parametrar i **/etc/waagent.conf** på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Avregistrera prenumerationen (om det behövs) genom att köra följande kommando:

        # subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Stäng av den virtuella datorn i KVM.

1. Konvertera qcow2-bilden till VHD-format.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versioner >=2.2.1 som resulterar i en felaktigt formaterad virtuell hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda antingen qemu-img 2.2.0 eller lägre, eller uppdatera till 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Förbereda en virtuell RHEL 7-dator från KVM

1. Ladda ner KVM-bilden på RHEL 7 från Red Hats webbplats. Den här proceduren använder RHEL 7 som exempel.

1. Ange ett rotlösenord.

    Generera ett krypterat lösenord och kopiera utdata från kommandot:

        # openssl passwd -1 changeme

    Ange ett rotlösenord med gästfisk:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Ändra det andra fältet för rotanvändare från "!!" till det krypterade lösenordet.

1. Skapa en virtuell dator i KVM från qcow2-avbildningen. Ställ in disktypen på **qcow2**och ställ in den virtuella nätverksgränssnittsenhetsmodellen på **virtio**. Starta sedan den virtuella datorn och logga in som root.

1. Skapa eller `/etc/sysconfig/network` redigera filen och lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. Om du vill `/etc/default/grub` göra den här konfigurationen `GRUB_CMDLINE_LINUX` öppnar du i en textredigerare och redigerar parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Det här kommandot säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Kommandot inaktiverar också de nya namngivningskonventionerna för RHEL 7 för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.

1. När du har `/etc/default/grub`redigerat klart kör du följande kommando för att återskapa grub-konfigurationen:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Lägg till Hyper-V-moduler i initramfs.

    Redigera `/etc/dracut.conf` och lägg till innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Bygg om initramfs:

        # dracut -f -v

1. Avinstallera cloud-init:

        # yum remove cloud-init

1. Kontrollera att SSH-servern är installerad och konfigurerad för start vid start:

        # systemctl enable sshd

    Ändra /etc/ssh/sshd_config för att inkludera följande rader:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # yum install WALinuxAgent

    Aktivera waagent-tjänsten:

        # systemctl enable waagent.service

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående `/etc/waagent.conf` steg ändrar du följande parametrar på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Avregistrera prenumerationen (om det behövs) genom att köra följande kommando:

        # subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Stäng av den virtuella datorn i KVM.

1. Konvertera qcow2-bilden till VHD-format.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versioner >=2.2.1 som resulterar i en felaktigt formaterad virtuell hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda antingen qemu-img 2.2.0 eller lägre, eller uppdatera till 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Förbered en Red Hat-baserad virtuell dator från VMware
### <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har installerat en virtuell RHEL-dator i VMware. Mer information om hur du installerar ett operativsystem i VMware finns i [VMware Guest Operating System Installation Guide](https://partnerweb.vmware.com/GOSIG/home.html).

* När du installerar Operativsystemet Linux rekommenderar vi att du använder standardpartitioner i stället för LVM, vilket ofta är standard för många installationer. Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorn, särskilt om ett operativsystem disk någonsin måste kopplas till en annan virtuell dator för felsökning. LVM eller RAID kan användas på datadiskar om så önskas.
* Konfigurera inte en växlingspartition på operativsystemdisken. Du kan konfigurera Linux-agenten för att skapa en växlingsfil på den temporära resursdisken. Du hittar mer information om detta i de steg som följer.
* När du skapar den virtuella hårddisken väljer du **Lagra virtuell disk som en enda fil**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Förbereda en virtuell RHEL 6-dator från VMware
1. I RHEL 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Skapa en fil med namnet **nätverk** i katalogen /etc/sysconfig/ som innehåller följande text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att generera statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # sudo chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. Det gör du `/etc/default/grub` genom att öppna i `GRUB_CMDLINE_LINUX` en textredigerare och redigera parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.

1. Lägg till Hyper-V-moduler i initramfs:

    Redigera `/etc/dracut.conf`och lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Bygg om initramfs:

        # dracut -f -v

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start, vilket vanligtvis är standard. Ändra `/etc/ssh/sshd_config` så att följande rad ingår:

    ClientAliveInterval 180

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående `/etc/waagent.conf` steg ändrar du följande parametrar på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Avregistrera prenumerationen (om det behövs) genom att köra följande kommando:

        # sudo subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Stäng av den virtuella datorn och konvertera VMDK-filen till en VHD-fil.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versioner >=2.2.1 som resulterar i en felaktigt formaterad virtuell hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda antingen qemu-img 2.2.0 eller lägre, eller uppdatera till 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Förbereda en virtuell RHEL 7-dator från VMware
1. Skapa eller `/etc/sysconfig/network` redigera filen och lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller `/etc/sysconfig/network-scripts/ifcfg-eth0` redigera filen och lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installation av paket från RHEL-databasen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. Om du vill `/etc/default/grub` göra den här ändringen `GRUB_CMDLINE_LINUX` öppnar du i en textredigerare och redigerar parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Den här konfigurationen säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Den stänger också av de nya namngivningskonventionerna för RHEL 7 för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten. Du kan `crashkernel` låta alternativet vara konfigurerat om så önskas. Observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på mindre storlekar för virtuella datorer.

1. När du har `/etc/default/grub`redigerat klart kör du följande kommando för att återskapa grub-konfigurationen:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Lägg till Hyper-V-moduler i initramfs.

    Redigera `/etc/dracut.conf`, lägg till innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Bygg om initramfs:

        # dracut -f -v

1. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start. Den här inställningen är vanligtvis standard. Ändra `/etc/ssh/sshd_config` så att följande rad ingår:

        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till Red Hat extramaterialarkivet. Aktivera extradatabasen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Skapa inte växlingsutrymme på operativsystemdisken.

    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter att den virtuella datorn har etablerats på Azure. Observera att den lokala resursdisken är en tillfällig disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux Agent i föregående `/etc/waagent.conf` steg ändrar du följande parametrar på lämpligt sätt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Om du vill avregistrera prenumerationen kör du följande kommando:

        # sudo subscription-manager unregister

1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Stäng av den virtuella datorn och konvertera VMDK-filen till VHD-format.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versioner >=2.2.1 som resulterar i en felaktigt formaterad virtuell hårddisk. Problemet har åtgärdats i QEMU 2.6. Det rekommenderas att använda antingen qemu-img 2.2.0 eller lägre, eller uppdatera till 2.6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Förbereda en Red Hat-baserad virtuell dator från en ISO med hjälp av en kickstart-fil automatiskt
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Förbereda en virtuell RHEL 7-dator från en kickstart-fil

1.  Skapa en kickstart-fil som innehåller följande innehåll och spara filen. Mer information om kickstart-installation finns i [Kickstarts installationsguide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
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

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Placera kickstart-filen där installationssystemet kan komma åt den.

1. Skapa en ny virtuell dator i Hyper-V Manager. På sidan **Anslut virtuell hårddisk** väljer du Bifoga en virtuell hårddisk **senare**och slutför guiden Ny virtuell dator.

1. Öppna inställningarna för den virtuella datorn:

    a.  Koppla en ny virtuell hårddisk till den virtuella datorn. Se till att välja **VHD-format** och **fast storlek**.

    b.  Anslut installationen ISO till DVD-enheten.

    c.  Ställ in BIOS att starta från CD.

1. Starta den virtuella datorn. När installationsguiden visas trycker du på **Tabb** för att konfigurera startalternativen.

1. Skriv `inst.ks=<the location of the kickstart file>` in i slutet av startalternativen och tryck på **Retur**.

1. Vänta tills installationen är klar. När den är klar stängs den virtuella datorn av automatiskt. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="known-issues"></a>Kända problem
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V-drivrutinen kunde inte inkluderas i den första RAM-disken när du använder en hypervisor som inte är Hyper-V

I vissa fall kanske Linux-installationsprogrammet inte innehåller drivrutinerna för Hyper-V i den ursprungliga RAM-disken (initrd eller initramfs) om inte Linux upptäcker att den körs i en Hyper-V-miljö.

När du använder ett annat virtualiseringssystem (det vill säga VirtualBox, Xen, etc.) för att förbereda din Linux-avbildning kan du behöva återskapa initrd för att säkerställa att åtminstone hv_vmbus och hv_storvsc kärnmoduler är tillgängliga på den ursprungliga RAM-disken. Detta är ett känt problem åtminstone på system som är baserade på uppströms Red Hat distribution.

LÃ¶s problemet genom att lÃ¤sa Hyper-V-moduler i initramfs och återskapa det:

Redigera `/etc/dracut.conf`och lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Bygg om initramfs:

        # dracut -f -v

Mer information finns i informationen om [ombyggnad av initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Nästa steg
* Du är nu redo att använda din virtuella Red Hat Enterprise Linux-hårddisk för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).
* Mer information om de hypervisorer som är certifierade för att köra Red Hat Enterprise Linux finns [på Red Hat-webbplatsen](https://access.redhat.com/certified-hypervisors).
* Mer information om hur du använder produktionsklara RHEL BYOS-avbildningar finns på dokumentationssidan för [BYOS](../workloads/redhat/byos.md).

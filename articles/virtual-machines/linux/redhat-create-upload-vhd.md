---
title: Skapa och ladda upp en Red Hat Enterprise Linux VHD för användning i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hård disk (VHD) som innehåller ett Red Hat Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: mimckitt
ms.openlocfilehash: 77334e3e807776e9072bb4ad9674bf7ba5a8f915
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732526"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Förbered en Red Hat-baserad virtuell dator för Azure
I den här artikeln får du lära dig hur du förbereder en virtuell Red Hat Enterprise Linux-dator (RHEL) för användning i Azure. De versioner av RHEL som beskrivs i den här artikeln är 6,7 + och 7.1 +. De flesta paneler för förberedelse som beskrivs i den här artikeln är Hyper-V, kernel-baserad virtuell dator (KVM) och VMware. Mer information om krav för berättigande för deltagande i Red Hats moln åtkomst program finns i [Red Hats webbplats för moln åtkomst](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) och [köra RHEL på Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Olika sätt att automatisera skapandet av RHEL-avbildningar finns i [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Förbereda en Red Hat-baserad virtuell dator från Hyper-V Manager

### <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har fått en ISO-fil från Red Hat-webbplatsen och installerat RHEL-avbildningen på en virtuell hård disk (VHD). Mer information om hur du använder Hyper-V Manager för att installera en operativ system avbildning finns i [Installera Hyper-v-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**Installations information för RHEL**

* Azure har inte stöd för VHDX-formatet. Azure stöder endast fast virtuell hård disk. Du kan använda Hyper-V Manager för att konvertera disken till VHD-format, eller så kan du använda cmdleten Convert-VHD. Om du använder VirtualBox väljer du **fast storlek** i stället för standard alternativet dynamiskt allokerat när du skapar disken.
* Azure har endast stöd för virtuella datorer i generation 1. Du kan konvertera en virtuell dator av första generationen från VHDX till VHD-filformat och från dynamiskt expandera till en disk med fast storlek. Du kan inte ändra den virtuella datorns generation. Mer information finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Den maximala storlek som tillåts för den virtuella hård disken är 1 023 GB.
* Logical Volume Manager (LVM) stöds och kan användas på operativ system disken eller data diskarna på virtuella Azure-datorer. I allmänhet rekommenderas det dock att använda standardpartitioner på OS-disken i stället för LVM. Den här övningen undviker LVM namn konflikter med klonade virtuella datorer, särskilt om du skulle behöva koppla en operativ system disk till en annan identisk virtuell dator för fel sökning. Se även [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -dokumentation.
* Kernel-stöd för montering av UDF-filsystem (Universal disk format) krävs. Vid den första starten av Azure skickar det UDF-formaterade mediet som är kopplat till gästen etablerings konfigurationen till den virtuella Linux-datorn. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Konfigurera inte en swap-partition på operativ system disken. Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken.  Mer information om detta finns i följande steg.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information hittar du i stegen nedan. Se även [Linux-installations anteckningar](create-upload-generic.md#general-linux-installation-notes) för mer information.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Förbereda en virtuell RHEL 6-dator från Hyper-V Manager

1. Välj den virtuella datorn i Hyper-V Manager.

1. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.

1. I RHEL 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Skapa eller redigera `/etc/sysconfig/network`-filen och Lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att skapa statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # sudo chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Om du vill göra den här ändringen öppnar du `/boot/grub/menu.lst` i en text redigerare och kontrollerar att standard kerneln innehåller följande parametrar:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem.
    
    Dessutom rekommenderar vi att du tar bort följande parametrar:
    
        rhgb quiet crashkernel=auto
    
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.  Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer. Den här konfigurationen kan vara problematisk vid mindre storlekar på virtuella datorer.


1. Se till att SSH-servern (Secure Shell) är installerad och konfigurerad att starta vid start, vilket vanligt vis är standardvärdet. Ändra/etc/ssh/-sshd_config att inkludera följande rad:

        ClientAliveInterval 180

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Installation av WALinuxAgent-paketet tar bort NetworkManager-och NetworkManager-GNOME-paketen om de inte redan har tagits bort i steg 3.

1. Skapa inte växlings utrymme på operativ system disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och att den kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i/etc/waagent.conf på lämpligt sätt:

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

1. Klicka på **åtgärd** > **Stäng av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Förbereda en virtuell RHEL 7-dator från Hyper-V Manager

1. Välj den virtuella datorn i Hyper-V Manager.

1. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.

1. Skapa eller redigera `/etc/sysconfig/network`-filen och Lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = Ja NM_CONTROLLED = Ja

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Om du vill göra den här ändringen öppnar du `/etc/default/grub` i en text redigerare och redigerar `GRUB_CMDLINE_LINUX`s parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Den här konfigurationen stänger också av de nya RHEL 7-namngivnings konventionerna för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt vid mindre storlekar på virtuella datorer.

1. När du är klar med redigeringen `/etc/default/grub`kör du följande kommando för att återskapa konfigurationen av grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Se till att SSH-servern är installerad och konfigurerad för start vid start, vilket vanligt vis är standardvärdet. Ändra `/etc/ssh/sshd_config` att inkludera följande rad:

        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Skapa inte växlings utrymme på operativ system disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i `/etc/waagent.conf` på lämpligt sätt:

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

1. Klicka på **åtgärd** > **Stäng av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Förbered en Red Hat-baserad virtuell dator från KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Förbered en virtuell RHEL 6-dator från KVM

1. Hämta KVM-avbildningen av RHEL 6 från Red Hat-webbplatsen.

1. Ange ett rot lösen ord.

    Generera ett krypterat lösen ord och kopiera utdata från kommandot:

        # openssl passwd -1 changeme

    Ange ett rot lösen ord med guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Ändra det andra fältet för rot användaren från "!!" till det krypterade lösen ordet.

1. Skapa en virtuell dator i KVM från QCOW2-avbildningen. Ange disk typen till **QCOW2**och ange enhets modellen för det virtuella nätverks gränssnittet till **Virtio**. Starta sedan den virtuella datorn och logga in som rot.

1. Skapa eller redigera `/etc/sysconfig/network`-filen och Lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att skapa statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Om du vill utföra den här konfigurationen öppnar du `/boot/grub/menu.lst` i en text redigerare och kontrollerar att standard kärnan innehåller följande parametrar:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem.
    
    Dessutom rekommenderar vi att du tar bort följande parametrar:
    
        rhgb quiet crashkernel=auto
    
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt vid mindre storlekar på virtuella datorer.


1. Lägg till Hyper-V-moduler i initramfs:  

    Redigera `/etc/dracut.conf`och Lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Återskapa initramfs:

        # dracut -f -v

1. Avinstallera Cloud-Init:

        # yum remove cloud-init

1. Se till att SSH-servern är installerad och konfigurerad för start vid start:

        # chkconfig sshd on

    Ändra/etc/ssh/-sshd_config att inkludera följande rader:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i **/etc/waagent.conf** på lämpligt sätt:

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

1. Konvertera QCOW2-avbildningen till VHD-formatet.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versionerna > = 2.2.1 som resulterar i en felaktigt formaterad virtuell hård disk. Problemet har åtgärd ATS i QEMU 2,6. Vi rekommenderar att du använder antingen qemu-img-2.2.0 eller lägre, eller uppdatera till 2,6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
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

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Förbered en virtuell RHEL 7-dator från KVM

1. Hämta KVM-avbildningen av RHEL 7 från Red Hat-webbplatsen. I den här proceduren används RHEL 7 som exempel.

1. Ange ett rot lösen ord.

    Generera ett krypterat lösen ord och kopiera utdata från kommandot:

        # openssl passwd -1 changeme

    Ange ett rot lösen ord med guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Ändra det andra fältet för rot användaren från "!!" till det krypterade lösen ordet.

1. Skapa en virtuell dator i KVM från QCOW2-avbildningen. Ange disk typen till **QCOW2**och ange enhets modellen för det virtuella nätverks gränssnittet till **Virtio**. Starta sedan den virtuella datorn och logga in som rot.

1. Skapa eller redigera `/etc/sysconfig/network`-filen och Lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = Ja NM_CONTROLLED = Ja

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. För att utföra den här konfigurationen öppnar du `/etc/default/grub` i en text redigerare och redigerar `GRUB_CMDLINE_LINUX`s parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Det här kommandot säkerställer också att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure-support med fel söknings problem. Kommandot stänger också av de nya RHEL 7-namngivnings konventionerna för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt vid mindre storlekar på virtuella datorer.

1. När du är klar med redigeringen `/etc/default/grub`kör du följande kommando för att återskapa konfigurationen av grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Lägg till Hyper-V-moduler i initramfs.

    Redigera `/etc/dracut.conf` och Lägg till innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Återskapa initramfs:

        # dracut -f -v

1. Avinstallera Cloud-Init:

        # yum remove cloud-init

1. Se till att SSH-servern är installerad och konfigurerad för start vid start:

        # systemctl enable sshd

    Ändra/etc/ssh/-sshd_config att inkludera följande rader:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # yum install WALinuxAgent

    Aktivera waagent-tjänsten:

        # systemctl enable waagent.service

1. Skapa inte växlings utrymme på operativ system disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i `/etc/waagent.conf` på lämpligt sätt:

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

1. Konvertera QCOW2-avbildningen till VHD-formatet.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versionerna > = 2.2.1 som resulterar i en felaktigt formaterad virtuell hård disk. Problemet har åtgärd ATS i QEMU 2,6. Vi rekommenderar att du använder antingen qemu-img-2.2.0 eller lägre, eller uppdatera till 2,6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Förbereda en Red Hat-baserad virtuell dator från VMware
### <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har installerat en virtuell RHEL-dator i VMware. Mer information om hur du installerar ett operativ system i VMware finns i [installations guide för VMware gäst operativ system](https://partnerweb.vmware.com/GOSIG/home.html).

* När du installerar Linux-operativsystemet rekommenderar vi att du använder standardpartitioner snarare än LVM, vilket ofta är standardvärdet för många installationer. På så sätt undviker du LVM namn konflikter med klonad virtuell dator, särskilt om en operativ system disk någonsin måste kopplas till en annan virtuell dator för fel sökning. LVM eller RAID kan användas på data diskar om det är lämpligt.
* Konfigurera inte en swap-partition på operativ system disken. Du kan konfigurera Linux-agenten för att skapa en växlings fil på den tillfälliga resurs disken. Du hittar mer information om detta i de steg som följer.
* När du skapar den virtuella hård disken väljer du **lagra virtuell disk som en enskild fil**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Förbered en virtuell RHEL 6-dator från VMware
1. I RHEL 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Skapa en fil med namnet **nätverk** i katalogen/etc/sysconfig/som innehåller följande text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Flytta (eller ta bort) udev-reglerna för att undvika att skapa statiska regler för Ethernet-gränssnittet. Dessa regler orsakar problem när du klonar en virtuell dator i Azure eller Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # sudo chkconfig network on

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna `/etc/default/grub` i en text redigerare och redigera `GRUB_CMDLINE_LINUX`-parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt vid mindre storlekar på virtuella datorer.

1. Lägg till Hyper-V-moduler i initramfs:

    Redigera `/etc/dracut.conf`och Lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Återskapa initramfs:

        # dracut -f -v

1. Se till att SSH-servern är installerad och konfigurerad för start vid start, vilket vanligt vis är standardvärdet. Ändra `/etc/ssh/sshd_config` att inkludera följande rad:

    ClientAliveInterval 180

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Skapa inte växlings utrymme på operativ system disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i `/etc/waagent.conf` på lämpligt sätt:

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
> Det finns en känd bugg i qemu-img-versionerna > = 2.2.1 som resulterar i en felaktigt formaterad virtuell hård disk. Problemet har åtgärd ATS i QEMU 2,6. Vi rekommenderar att du använder antingen qemu-img-2.2.0 eller lägre, eller uppdatera till 2,6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
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
1. Skapa eller redigera `/etc/sysconfig/network`-filen och Lägg till följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Skapa eller redigera `/etc/sysconfig/network-scripts/ifcfg-eth0`-filen och Lägg till följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = Ja NM_CONTROLLED = Ja

1. Kontrol lera att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

        # sudo systemctl enable network

1. Registrera din Red Hat-prenumeration för att aktivera installationen av paket från RHEL-lagringsplatsen genom att köra följande kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Om du vill göra den här ändringen öppnar du `/etc/default/grub` i en text redigerare och redigerar `GRUB_CMDLINE_LINUX`s parametern. Ett exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Den här konfigurationen säkerställer också att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure-support med fel söknings problem. Den stänger också av de nya RHEL 7-namngivnings konventionerna för nätverkskort. Dessutom rekommenderar vi att du tar bort följande parametrar:
   
        rhgb quiet crashkernel=auto
   
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. Du kan lämna `crashkernel` alternativet konfigurerat om du vill. Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt vid mindre storlekar på virtuella datorer.

1. När du är klar med redigeringen `/etc/default/grub`kör du följande kommando för att återskapa konfigurationen av grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Lägg till Hyper-V-moduler i initramfs.

    Redigera `/etc/dracut.conf`, Lägg till innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Återskapa initramfs:

        # dracut -f -v

1. Se till att SSH-servern är installerad och konfigurerad för start vid start. Den här inställningen är vanligt vis standardvärdet. Ändra `/etc/ssh/sshd_config` att inkludera följande rad:

        ClientAliveInterval 180

1. WALinuxAgent-paketet `WALinuxAgent-<version>`har flyttats till den Red Hat extra-lagringsplatsen. Aktivera extra-lagringsplatsen genom att köra följande kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installera Azure Linux-agenten genom att köra följande kommando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Skapa inte växlings utrymme på operativ system disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har allokerats på Azure. Observera att den lokala resurs disken är en temporär disk och kan tömmas om den virtuella datorn avetableras. När du har installerat Azure Linux-agenten i föregående steg ändrar du följande parametrar i `/etc/waagent.conf` på lämpligt sätt:

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

1. Stäng av den virtuella datorn och konvertera VMDK-filen till VHD-formatet.

> [!NOTE]
> Det finns en känd bugg i qemu-img-versionerna > = 2.2.1 som resulterar i en felaktigt formaterad virtuell hård disk. Problemet har åtgärd ATS i QEMU 2,6. Vi rekommenderar att du använder antingen qemu-img-2.2.0 eller lägre, eller uppdatera till 2,6 eller högre. Referens: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Förbereda en Red Hat-baserad virtuell dator från en ISO genom att använda en Kickstart-fil automatiskt
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Förbereda en virtuell RHEL 7-dator från en Kickstart-fil

1.  Skapa en Kickstart-fil som innehåller följande innehåll och spara filen. Mer information om installation av kickstart finns i [installations guiden för kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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
    PERSISTENT_DHCLIENT = Ja NM_CONTROLLED = Ja EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Placera kickstart-filen där installations systemet har åtkomst till den.

1. Skapa en ny virtuell dator i Hyper-V Manager. På sidan **Anslut virtuell hård disk** väljer du **Anslut en virtuell hård disk senare**och Slutför guiden Ny virtuell dator.

1. Öppna inställningarna för den virtuella datorn:

    a.  Koppla en ny virtuell hård disk till den virtuella datorn. Se till att välja **VHD-format** och **fast storlek**.

    b.  Bifoga installationen av ISO till DVD-enheten.

    c.  Ange att BIOS ska starta från CD.

1. Starta den virtuella datorn. När installations guiden visas trycker du på **TABB** för att konfigurera start alternativen.

1. Ange `inst.ks=<the location of the kickstart file>` i slutet av start alternativen och tryck på **RETUR**.

1. Vänta tills installationen är klar. När den är färdig stängs den virtuella datorn av automatiskt. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="known-issues"></a>Kända problem
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Det gick inte att ta med Hyper-V-drivrutinen på den första RAM-disken när en icke-Hyper-V-hypervisor används

I vissa fall kanske Linux-installations program inte innehåller driv rutinerna för Hyper-V i den första RAM-disken (initrd eller initramfs) om inte Linux känner av att den körs i en Hyper-V-miljö.

När du använder ett annat Virtualization-system (det vill säga VirtualBox, Xen osv.) för att förbereda Linux-avbildningen kan du behöva återskapa initrd för att säkerställa att minst hv_vmbus och hv_storvsc kernel-moduler är tillgängliga på den första RAM-disken. Detta är ett känt problem som är minst på system som baseras på den överordnade Red Hat-distributionen.

Lös problemet genom att lägga till Hyper-V-moduler i initramfs och återskapa den:

Redigera `/etc/dracut.conf`och Lägg till följande innehåll:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Återskapa initramfs:

        # dracut -f -v

Mer information finns i informationen om att [Återskapa initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Red Hat Enterprise Linux virtuella hård disk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

Mer information om de hypervisorer som är certifierade att köra Red Hat Enterprise Linux finns i [Red Hat-webbplatsen](https://access.redhat.com/certified-hypervisors).

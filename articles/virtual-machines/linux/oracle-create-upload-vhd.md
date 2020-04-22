---
title: Skapa och ladda upp en Virtuell System -fil på Oracle Linux
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Oracle Linux-operativsystem.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: fd6d17709cc3e5e9f6bb89ed7480fcd9ee80fd97
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759388"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Förbereda en virtuell Oracle Linux-dator för Azure

Den här artikeln förutsätter att du redan har installerat ett Oracle Linux-operativsystem på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns [i Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux installationsanteckningar
* Se även [Allmänna Linux-installationsanteckningar](create-upload-generic.md#general-linux-installation-notes) för fler tips om hur du förbereder Linux för Azure.
* Hyper-V och Azure stöder Oracle Linux med antingen UEK (Unbreakable Enterprise Kernel) eller Red Hat Compatible Kernel.
* Oracles UEK2 stöds inte på Hyper-V och Azure eftersom det inte innehåller de drivrutiner som krävs.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hårddisk**.  Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten konvertera-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standardpartitioner i stället för LVM (ofta standard för många installationer). Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar om så önskas.
* Linux-kärnversioner tidigare än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med hjälp av uppströms Red Hat 2.6.32-kärnan och åtgärdades i Oracle Linux 6.6 och senare
* Konfigurera inte en växlingspartition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att raw-diskstorleken är en multipel av 1 MB före konvertering. Mer information finns i [Linux Installationsanteckningar.](create-upload-generic.md#general-linux-installation-notes)
* Kontrollera att `Addons` databasen är aktiverad. Redigera filen `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) `/etc/yum.repos.d/public-yum-ol7.repo`eller (Oracle Linux `enabled=0` 7) och ändra raden till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 och senare
Du måste slutföra specifika konfigurationssteg i operativsystemet för att den virtuella datorn ska kunna köras i Azure.

1. Välj den virtuella datorn i mittrutan i Hyper-V-hanteraren.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Avinstallera NetworkManager genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Anm.:** Om paketet inte redan är installerat misslyckas det här kommandot med ett felmeddelande. Detta är normalt.
4. Skapa en **network** fil med `/etc/sysconfig/` namnet nätverk i katalogen som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Skapa en fil med namnet **ifcfg-eth0** i katalogen `/etc/sysconfig/network-scripts/` som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Ändra udev-regler för att undvika att generera statiska regler för Ethernet-gränssnitten.Modify udev rules to avoid generating static rules for the Ethernet interface(s). Dessa regler kan orsaka problem vid kloning av en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:
   
        # chkconfig network on
8. Installera python-pyasn1 genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
9. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra detta öppet "/boot/grub/menu.lst" i en textredigerare och se till att kärnan innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem.
   
   Utöver ovanstående rekommenderas att du *tar bort* följande parametrar:
   
        rhgb quiet crashkernel=auto
   
   Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten.
   
   Alternativet `crashkernel` kan lämnas konfigurerat om så önskas, men observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre vm-storlekarna.
10. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.
11. Installera Azure Linux-agenten genom att köra följande kommando. Den senaste versionen är 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observera att om du installerar PAKETEN WALinuxAgent tas NetworkManager- och NetworkManager-gnome-paketen bort om de inte redan har tagits bort enligt beskrivningen i steg 2.
12. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på lämpligt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 och senare
**Förändringar i Oracle Linux 7**

Förbereda en Oracle Linux 7 virtuell maskin för Azure är mycket lik Oracle Linux 6, men det finns flera viktiga skillnader värt att notera:

* Azure stöder Oracle Linux med antingen UEK (Unbreakable Enterprise Kernel) eller Red Hat Compatible Kernel. Oracle Linux med UEK rekommenderas.
* NetworkManager-paketet står inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att det inte tas bort.
* GRUB2 används nu som standard bootloader, så proceduren för redigering av kärnparametrar har ändrats (se nedan).
* XFS är nu standardfilsystemet. Filsystemet ext4 kan fortfarande användas om så önskas.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.
3. Skapa en **network** fil med `/etc/sysconfig/` namnet nätverk i katalogen som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Skapa en fil med namnet **ifcfg-eth0** i katalogen `/etc/sysconfig/network-scripts/` som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Ändra udev-regler för att undvika att generera statiska regler för Ethernet-gränssnitten.Modify udev rules to avoid generating static rules for the Ethernet interface(s). Dessa regler kan orsaka problem vid kloning av en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Kontrollera att nätverkstjänsten startar vid starttiden genom att köra följande kommando:
   
        # sudo chkconfig network on
7. Installera python-pyasn1-paketet genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
8. Kör följande kommando för att rensa de aktuella yum-metadata och installera eventuella uppdateringar:
   
        # sudo yum clean all
        # sudo yum -y update
9. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra detta öppna "/etc/default/grub" `GRUB_CMDLINE_LINUX` i en textredigerare och redigera parametern, till exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Det inaktiverar också namngivningskonventionerna för nätverkskort i Oracle Linux 7 med Unbreakable Enterprise Kernel. Utöver ovanstående rekommenderas att du *tar bort* följande parametrar:
   
       rhgb quiet crashkernel=auto
   
   Grafisk och tyst start är inte användbara i en molnmiljö där vi vill att alla loggar ska skickas till serieporten.
   
   Alternativet `crashkernel` kan lämnas konfigurerat om så önskas, men observera att den här parametern minskar mängden tillgängligt minne i den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre vm-storlekarna.
10. När du är klar redigering "/ etc / default / grub" per ovan, kör följande kommando för att återskapa grub konfiguration:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.
12. Installera Azure Linux-agenten genom att köra följande kommando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på lämpligt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Oracle Linux .vhd för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


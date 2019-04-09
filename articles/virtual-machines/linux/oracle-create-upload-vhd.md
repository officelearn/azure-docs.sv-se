---
title: Skapa och ladda upp en VHD för Oracle Linux | Microsoft Docs
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller ett Oracle Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: ecd30d30434d91893102ce6ec0df21daa84b677c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276871"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Förbered en virtuell Oracle Linux-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat ett Oracle Linux-operativsystem till en virtuell hårddisk. Det finns flera olika verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning, till exempel Hyper-V. Anvisningar finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Installationsinformation för Oracle Linux
* Se även [allmän information för Linux-Installation](create-upload-generic.md#general-linux-installation-notes) fler tips om hur du förbereder Linux för Azure.
* Oracles Red Hat kompatibla kernel och deras UEK3 (Unbreakable Enterprise Kernel) stöds både på Hyper-V och Azure. För bästa resultat var noga med att uppdatera till den senaste kerneln när du förbereder dina VHD: N för Oracle Linux.
* Oracles UEK2 stöds inte på Hyper-V och Azure eftersom det inte innehåller de nödvändiga drivrutinerna.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten convert-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar om du så föredrar.
* NUMA finns inte stöd för större VM-storlekar på grund av ett fel i Linux-kernel-versioner under 2.6.37. Det här problemet påverkar huvudsakligen distributioner som använder den överordnade Red Hat 2.6.32 kernel. Manuell installation av Azure Linux-agenten (waagent) inaktiverar automatiskt NUMA i GRUB-konfigurationen för Linux-kernel. Mer information om detta finns i stegen nedan.
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.
* Se till att den `Addons` databasen är aktiverat. Redigera filen `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7), och ändrar du raden `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4+
Du måste slutföra specifika konfigurationssteg i operativsystemet för den virtuella datorn att köra i Azure.

1. I den mittersta rutan av Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Connect** att öppna fönstret för den virtuella datorn.
3. Avinstallera NetworkManager genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Obs!** Om paketet inte redan är installerad, misslyckas det här kommandot med ett felmeddelande. Detta är normalt.
4. Skapa en fil med namnet **nätverk** i den `/etc/sysconfig/` katalog som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Skapa en fil med namnet **ifcfg-eth0** i den `/etc/sysconfig/network-scripts/` katalog som innehåller följande text:
   
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
   
        # chkconfig network on
8. Installera python-pyasn1 genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
9. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Att göra den här öppen ”/ boot/grub/menu.lst” i en textredigerare och se till att standardkernel innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Detta inaktiverar NUMA på grund av ett fel i Oracle för Red Hat kompatibla kernel.
   
   Förutom ovanstående rekommenderar vi att du *ta bort* följande parametrar:
   
        rhgb quiet crashkernel=auto
   
   Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.
   
   Den `crashkernel` alternativet kanske vänster konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på mindre storlekar för Virtuella datorer.
10. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.
11. Installera Azure Linux Agent genom att köra följande kommando. Den senaste versionen är 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observera att installera paketet WALinuxAgent tar bort NetworkManager och NetworkManager gör väldigt lätt paket om de inte har redan tagits bort enligt beskrivningen i steg 2.
12. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
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

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0+
**Ändringar i Oracle Linux 7**

Förbereda en virtuell Oracle Linux 7-dator för Azure är mycket lik Oracle Linux 6, men det finns flera viktiga skillnader att tänka på:

* Både Red Hat kompatibla kernel och Oracles UEK3 stöds i Azure.  UEK3 kernel rekommenderas.
* NetworkManager paketet är inte längre i konflikt med Azure Linux-agent. Det här paketet installeras som standard och vi rekommenderar att den inte tas bort.
* GRUB2 används nu som standard-startprogrammet så metoderna för att redigera kernel parametrar har ändrats (se nedan).
* XFS är nu standardfilsystemet. Filsystemet ext4 kan fortfarande användas om du vill.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Connect** att öppna ett konsolfönster för den virtuella datorn.
3. Skapa en fil med namnet **nätverk** i den `/etc/sysconfig/` katalog som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Skapa en fil med namnet **ifcfg-eth0** i den `/etc/sysconfig/network-scripts/` katalog som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Se till att nätverkstjänsten startar när datorn startas genom att köra följande kommando:
   
        # sudo chkconfig network on
7. Installera python-pyasn1-paketet genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
8. Kör följande kommando för att ta bort den aktuella yum-metadata och installera relevanta uppdateringar:
   
        # sudo yum clean all
        # sudo yum -y update
9. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Om du vill öppna ”/ etc/standard/grub” i en textredigerare och redigera den `GRUB_CMDLINE_LINUX` parameter, till exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Den stängs även av de nya namnkonventionerna för OEL 7 för nätverkskort. Förutom ovanstående rekommenderar vi att du *ta bort* följande parametrar:
   
       rhgb quiet crashkernel=auto
   
   Grafiska och tyst start är inte användbart i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.
   
   Den `crashkernel` alternativet kanske vänster konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på mindre storlekar för Virtuella datorer.
10. När du är klar redigering ”/ etc/standard/grub” per ovan, kör du följande kommando för att återskapa grub-konfigurationen:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.
12. Installera Azure Linux Agent genom att köra följande kommando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda Oracle Linux-VHD för att skapa nya virtuella datorer i Azure. Om detta är första gången som du laddar upp VHD-filen till Azure, se [skapa en Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


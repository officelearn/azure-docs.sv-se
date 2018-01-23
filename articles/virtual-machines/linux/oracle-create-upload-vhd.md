---
title: "Skapa och ladda upp en VHD för Oracle Linux | Microsoft Docs"
description: "Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller ett Oracle Linux-operativsystem."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: szark
ms.openlocfilehash: a592dfbc6f19afe255cee1a8dfb48e3c96d7baf8
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Förbered en virtuell Oracle Linux-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat ett Oracle Linux-operativsystem till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Installationsinformation för Oracle Linux
* Se även [allmänna Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer tips om hur du förbereder Linux för Azure.
* Oracles Red Hat kompatibel kernel och deras UEK3 (Unbreakable Enterprise Kernel) stöds både i Hyper-V och Azure. För bästa resultat bör kontrollera att uppdatera till den senaste kerneln när du förberedde din Oracle Linux VHD.
* Oracles UEK2 stöds inte på Hyper-V och Azure som inte innehåller de nödvändiga drivrutinerna.
* VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet convert-vhd.
* När du installerar Linux-system rekommenderas att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar som önskade.
* NUMA stöds inte för större VM-storlekar på grund av ett fel i Linux kernel-versioner än 2.6.37. Det här problemet påverkar huvudsakligen distributioner med hjälp av den överordnade Red Hat 2.6.32 kernel. Manuell installation av Azure Linux-agenten (waagent) inaktiverar automatiskt NUMA i GRUB konfigurationen för Linux-kärnan. Mer information om detta finns i stegen nedan.
* Konfigurera inte en byte-partition på OS-disk. Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken.  Mer information om detta finns i stegen nedan.
* Alla de virtuella hårddiskarna måste ha storlekar som multiplar av 1 MB.
* Se till att den `Addons` databasen är aktiverad. Redigera filen `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), och ändrar du raden `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Du måste slutföra specifika konfigurationssteg i operativsystemet för den virtuella datorn körs i Azure.

1. I rutan i mitten av Hyper-V Manager, Välj den virtuella datorn.
2. Klicka på **Anslut** att öppna fönstret för den virtuella datorn.
3. Avinstallera NetworkManager genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Obs:** om paketet inte har installerats, kommandot misslyckas med felmeddelandet. Detta är förväntat.
4. Skapa en fil med namnet **nätverk** i den `/etc/sysconfig/` katalog som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Skapa en fil med namnet **ifcfg eth0** i den `/etc/sysconfig/network-scripts/` katalog som innehåller följande text:
   
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
   
        # chkconfig network on
8. Installera python-pyasn1 genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
9. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Att göra den här öppna ”/ boot/grub/menu.lst” i en textredigerare och se till att standard-kärnan innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Detta inaktiverar NUMA på grund av ett fel i Oracles Red Hat kompatibel kernel.
   
   Förutom de som nämns ovan, rekommenderas att *ta bort* följande parametrar:
   
        rhgb quiet crashkernel=auto
   
   Grafisk och tyst Start inte är användbara i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.
   
   Den `crashkernel` alternativet kan vara left konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på de virtuella datorn är mindre.
10. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.
11. Installera Azure Linux-agenten genom att köra följande kommando. Den senaste versionen är 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observera att installera paketet WALinuxAgent tar bort NetworkManager och NetworkManager gör väldigt lätt paket om de inte har redan tagits bort enligt beskrivningen i steg 2.
12. Skapa inte växlingsutrymme på OS-disk.
    
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
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

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Ändringar i Oracle Linux 7**

Förbereda en virtuell dator för Oracle Linux 7 för Azure liknar Oracle Linux 6, men det finns flera viktiga skillnader noteras:

* Både kompatibla kernel Red Hat och Oracles UEK3 stöds i Azure.  UEK3 kernel rekommenderas.
* NetworkManager paketet inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att den inte tas bort.
* GRUB2 används nu som standard-startprogrammet så proceduren för att redigera kernel parametrar har ändrats (se nedan).
* XFS är nu filsystemet. Filsystemet ext4 kan fortfarande användas om du vill.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** att öppna ett konsolfönster för den virtuella datorn.
3. Skapa en fil med namnet **nätverk** i den `/etc/sysconfig/` katalog som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Skapa en fil med namnet **ifcfg eth0** i den `/etc/sysconfig/network-scripts/` katalog som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. De här reglerna kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Kontrollera nätverkstjänsten startar när datorn startas genom att köra följande kommando:
   
        # sudo chkconfig network on
7. Installera python-pyasn1 paketet genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
8. Kör följande kommando för att rensa aktuella yum metadata och installera uppdateringar:
   
        # sudo yum clean all
        # sudo yum -y update
9. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Om du vill öppna ”/ etc/standard/grub” i en textredigerare och redigera den `GRUB_CMDLINE_LINUX` parameter, till exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Även stängs av nya OEL 7 namnkonventionerna för nätverkskort. Förutom de som nämns ovan, rekommenderas att *ta bort* följande parametrar:
   
       rhgb quiet crashkernel=auto
   
   Grafisk och tyst Start inte är användbara i en molnmiljö där vi vill att alla loggar som ska skickas till den seriella porten.
   
   Den `crashkernel` alternativet kan vara left konfigureras om så önskas, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, som kan vara problematiskt på de virtuella datorn är mindre.
10. När du är klar för redigering ”/ etc/standard/grub” per ovan, kör du följande kommando för att återskapa grub konfigurationen:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.
12. Installera Azure Linux-agenten genom att köra följande kommando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Skapa inte växlingsutrymme på OS-disk.
    
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att använda Oracle Linux-VHD för att skapa nya virtuella datorer i Azure. Om att du överföra VHD-filen till Azure Se [skapa ett Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


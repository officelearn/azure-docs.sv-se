---
title: Skapa och ladda upp en virtuell Hårddisk SUSE Linux i Azure
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller en SUSE Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 3bdd3f4d67fcaae37736022c3c59af19eb04bec7
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036657"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Förbered en virtuell SLES- eller openSUSE-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat en SUSE- eller openSUSE Linux-operativsystem till en virtuell hårddisk. Det finns flera olika verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning, till exempel Hyper-V. Anvisningar finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE installationsinformation
* Se även [allmän information för Linux-Installation](create-upload-generic.md#general-linux-installation-notes) fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten convert-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar om du så föredrar.
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.

## <a name="use-suse-studio"></a>Använd SUSE Studio
[SUSE Studio](http://www.susestudio.com) kan enkelt skapa och hantera dina SLES och openSUSE avbildningar för Azure och Hyper-V. Det här är den rekommenderade metoden för att anpassa dina egna SLES och openSUSE-avbildningar.

Som ett alternativ till att skapa en egen virtuell Hårddisk, SUSE publicerar även BYOS (ta med din egen prenumeration) avbildningar för SLES på [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Förbereda SUSE Linux Enterprise Server 11 SP4
1. I den mittersta rutan av Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Connect** att öppna fönstret för den virtuella datorn.
3. Registrera din SUSE Linux Enterprise-system så att den kan hämta uppdateringar och installera paket.
4. Uppdatera systemet med de senaste uppdateringarna:
   
        # sudo zypper update
5. Installera Azure Linux Agent från SLES-lagringsplatsen:
   
        # sudo zypper install WALinuxAgent
6. Kontrollera om waagent är inställd på ”on” i chkconfig och om inte, aktiverar du det för autostart:
   
        # sudo chkconfig waagent on
7. Kontrollera om waagent-tjänsten körs och starta om annars: 
   
        # sudo service waagent start
8. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Att göra den här öppen ”/ boot/grub/menu.lst” i en textredigerare och se till att standardkernel innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Det säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem.
9. Bekräfta att /boot/grub/menu.lst och/etc/fstab båda hänvisar till disken med dess UUID (med uuid) i stället för disk-ID (med-id). 
   
    Hämta disk UUID
   
        # ls /dev/disk/by-uuid/
   
    Om /dev/disk/by-id / används, uppdatera både /boot/grub/menu.lst och/etc/fstab med rätt av uuid-värdet
   
    Före ändringen
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Efter ändring
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Det är rekommenderat att redigera filen ”/ etc/sysconfig/nätverk/dhcp” och ändra den `DHCLIENT_SET_HOSTNAME` parametern för följande:
    
     DHCLIENT_SET_HOSTNAME="no"
12. I ”/ etc/sudoers”, kommentera ut eller ta bort följande rader om de finns:
    
     Som standard targetpw # be om lösenordet för målanvändaren d.v.s. rot alla ALL=(ALL) alla # varning! Använd endast detta tillsammans med ”standardvärden targetpw'!
13. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.
14. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Obs: Ställ in på vad du behöver det ska vara.
15. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

- - -
## <a name="prepare-opensuse-131"></a>Förbereda openSUSE 13,1 +
1. I den mittersta rutan av Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Connect** att öppna fönstret för den virtuella datorn.
3. Kör kommandot på skal, '`zypper lr`'. Om det här kommandot returnerar utdata som liknar följande och sedan lagringsplatserna har konfigurerats som förväntat--några justeringar krävs (Observera att versionsnumren kan variera):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Om kommandot returnerar ”inga databaser som definierats...” använder du följande kommandon för att lägga till dessa databaser:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Du kan kontrollera lagringsplatserna har lagts till genom att köra kommandot '`zypper lr`' igen. Om en uppdatering som databaser inte är aktiverad kan du aktivera det med följande kommando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Uppdatera kernel till den senaste tillgängliga versionen:
   
        # sudo zypper up kernel-default
   
    Eller för att uppdatera systemet med de senaste korrigeringarna:
   
        # sudo zypper update
5. Installera Azure Linux-agenten.
   
        # sudo zypper install WALinuxAgent
6. Ändra i kernel boot line i din grub konfiguration och omfattar ytterligare kernel parametrar för Azure. Gör detta genom att öppna ”/ boot/grub/menu.lst” i en textredigerare och se till att standardkernel innehåller följande parametrar:
   
     konsolen = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Det säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Dessutom ta bort följande parametrar från i kernel boot line om de finns:
   
     libata.atapi_enabled=0 reservera = 0x1f0, 0x8
7. Det är rekommenderat att redigera filen ”/ etc/sysconfig/nätverk/dhcp” och ändra den `DHCLIENT_SET_HOSTNAME` parametern för följande:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Viktigt:** i ”/ etc/sudoers”, kommentera ut eller ta bort följande rader om de finns:
   
     Som standard targetpw # be om lösenordet för målanvändaren d.v.s. rot alla ALL=(ALL) alla # varning! Använd endast detta tillsammans med ”standardvärden targetpw'!
9. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.
10. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme använder den lokala resursdisk som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Obs: Ställ in på vad du behöver det ska vara.
11. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Kontrollera att Azure Linux Agent körs vid start:
    
        # sudo systemctl enable waagent.service
13. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda den virtuella hårddisken för SUSE Linux för att skapa nya virtuella datorer i Azure. Om detta är första gången som du laddar upp VHD-filen till Azure, se [skapa en Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

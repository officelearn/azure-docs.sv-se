---
title: Skapa och ladda upp en SUSE Linux VHD i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett SUSE Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: fe057cb4e831fd95669ad5d5f93332a3952f16ef
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460944"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Förbereda en virtuell SLES- eller openSUSE-dator för Azure


Den här artikeln förutsätter att du redan har installerat ett SUSE- eller openSUSE Linux-operativsystem på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns [i Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE installationsanteckningar
* Se även [Allmänna Linux-installationsanteckningar](create-upload-generic.md#general-linux-installation-notes) för fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hårddisk**.  Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten konvertera-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standardpartitioner i stället för LVM (ofta standard för många installationer). Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar om så önskas.
* Konfigurera inte en växlingspartition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att raw-diskstorleken är en multipel av 1 MB före konvertering. Mer information finns i [Linux Installationsanteckningar.](create-upload-generic.md#general-linux-installation-notes)

## <a name="use-suse-studio"></a>Använd SUSE Studio
[SUSE Studio](http://www.susestudio.com) kan enkelt skapa och hantera dina SLES- och openSUSE-avbildningar för Azure och Hyper-V. Detta är den rekommenderade metoden för att anpassa dina egna SLES och openSUSE-bilder.

Som ett alternativ till att bygga din egen VHD publicerar SUSE också BYOS (Bring Your Own Subscription) bilder för SLES på [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Förbered SUSE Linux Enterprise Server 11 SP4
1. Välj den virtuella datorn i mittrutan i Hyper-V-hanteraren.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Registrera ditt SUSE Linux Enterprise-system så att det kan hämta uppdateringar och installera paket.
4. Uppdatera systemet med de senaste korrigeringsfilerna:
   
        # sudo zypper update
5. Installera Azure Linux-agenten från SLES-databasen:
   
        # sudo zypper install python-azure-agent
6. Kontrollera om waagent är inställt på "på" i chkconfig, och om inte, aktivera den för autostart:
   
        # sudo chkconfig waagent on
7. Kontrollera om waagent tjänsten körs, och om inte, starta den: 
   
        # sudo service waagent start
8. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra detta öppet "/boot/grub/menu.lst" i en textredigerare och se till att standardkärnan innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem.
9. Bekräfta att /boot/grub/menu.lst och /etc/fstab båda refererar till disken med hjälp av dess UUID (by-uuid) i stället för disk-ID (by-id). 
   
    Hämta UUID för disk
   
        # ls /dev/disk/by-uuid/
   
    Om /dev/disk/by-id/ används uppdaterar du både /boot/grub/menu.lst och /etc/fstab med rätt by-uuid-värde
   
    Före ändring
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Efter förändring
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Ändra udev-regler för att undvika att generera statiska regler för Ethernet-gränssnitten.Modify udev rules to avoid generating static rules for the Ethernet interface(s). Dessa regler kan orsaka problem vid kloning av en virtuell dator i Microsoft Azure eller Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Vi rekommenderar att du redigerar filen "/etc/sysconfig/network/dhcp" och ändrar parametern `DHCLIENT_SET_HOSTNAME` till följande:
    
     DHCLIENT_SET_HOSTNAME="nej"
12. I "/etc/sudoers", kommentera ut eller ta bort följande rader om de finns:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.
14. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på lämpligt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## OBS: Ställ in det här på vad du vill att det ska vara.
15. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

---
## <a name="prepare-opensuse-131"></a>Förbered openSUSE 13.1+
1. Välj den virtuella datorn i mittrutan i Hyper-V-hanteraren.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Kör kommandot ' 'på`zypper lr`skalet. Om det här kommandot returnerar utdata som liknar följande, konfigureras databaserna som förväntade – inga justeringar är nödvändiga (observera att versionsnumren kan variera):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Om kommandot returnerar "Inga databaser definierade..." Använd sedan följande kommandon för att lägga till dessa repor:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Du kan sedan kontrollera att databaserna har lagts`zypper lr`till genom att köra kommandot ' ' igen. Om något av de relevanta uppdateringsdatabaserna inte är aktiverat aktiverar du det med följande kommando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Uppdatera kärnan till den senaste tillgängliga versionen:
   
        # sudo zypper up kernel-default
   
    Eller för att uppdatera systemet med alla de senaste patcharna:
   
        # sudo zypper update
5. Installera Azure Linux-agenten.
   
        # sudo zypper install WALinuxAgent
6. Ändra kärnstartlinjen i grub-konfigurationen för att inkludera ytterligare kärnparametrar för Azure. För att göra detta, öppna "/boot/grub/menu.lst" i en textredigerare och se till att standardkärnan innehåller följande parametrar:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-stöd med felsökningsproblem. Ta dessutom bort följande parametrar från kärnstartlinjen om de finns:
   
     libata.atapi_enabled=0 reserv=0x1f0,0x8
7. Vi rekommenderar att du redigerar filen "/etc/sysconfig/network/dhcp" och ändrar parametern `DHCLIENT_SET_HOSTNAME` till följande:
   
     DHCLIENT_SET_HOSTNAME="nej"
8. **Viktigt:** I "/etc/sudoers", kommentera ut eller ta bort följande rader om de finns:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.
10. Skapa inte växlingsutrymme på OS-disken.
    
    Azure Linux Agent kan automatiskt konfigurera växlingsutrymme med hjälp av den lokala resursdisken som är kopplad till den virtuella datorn efter etablering på Azure. Observera att den lokala resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux Agent (se föregående steg), ändra följande parametrar i /etc/waagent.conf på lämpligt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## OBS: Ställ in det här på vad du vill att det ska vara.
11. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Se till att Azure Linux Agent körs vid start:
    
        # sudo systemctl enable waagent.service
13. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda den virtuella hårddisken SUSE Linux för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

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
ms.openlocfilehash: 2372550548f40ad07b4f76c19bc3bc1cb8380830
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Förbered en virtuell SLES- eller openSUSE-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat en SUSE eller openSUSE Linux-operativsystem till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE installationsinformation
* Se även [allmänna Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer tips om hur du förbereder Linux för Azure.
* VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet convert-vhd.
* När du installerar Linux-system rekommenderas att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar som önskade.
* Konfigurera inte en byte-partition på OS-disk. Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB innan konverteringen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.

## <a name="use-suse-studio"></a>Använd SUSE Studio
[SUSE Studio](http://www.susestudio.com) kan enkelt skapa och hantera dina SLES och openSUSE avbildningar för Azure och Hyper-V. Det här är den rekommenderade metoden för att anpassa dina egna SLES och openSUSE bilder.

Som ett alternativ till att skapa egna VHD SUSE publiceras även BYOS (ta med din egen prenumeration) avbildningar för SLES på [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Förbereda SUSE Linux Enterprise Server 11 SP4
1. I rutan i mitten av Hyper-V Manager, Välj den virtuella datorn.
2. Klicka på **Anslut** att öppna fönstret för den virtuella datorn.
3. Registrera SUSE Linux Enterprise systemet så att den kan hämta uppdateringar och installera paket.
4. Uppdatera systemet med de senaste korrigeringarna:
   
        # sudo zypper update
5. Installera Azure Linux-agenten från SLES databasen:
   
        # sudo zypper install WALinuxAgent
6. Kontrollera om waagent är inställd på ”on” i chkconfig och om inte, aktivera den för autostart:
   
        # sudo chkconfig waagent on
7. Kontrollera om waagent-tjänsten körs och starta om inte: 
   
        # sudo service waagent start
8. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Att göra den här öppna ”/ boot/grub/menu.lst” i en textredigerare och se till att standard-kärnan innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem.
9. Bekräfta att /boot/grub/menu.lst och /etc/fstab båda refererar till disken med dess UUID (av uuid) i stället för disk-ID (med-id). 
   
    Hämta disk-UUID
   
        # ls /dev/disk/by-uuid/
   
    Om /dev/disk/by-id är används, uppdatera både /boot/grub/menu.lst och/etc/fstab med rätt av uuid-värdet
   
    Före ändringen
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Efter ändring
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Ändra udev regler för att undvika att generera statiska regler för Ethernet-gränssnitt. De här reglerna kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Det rekommenderas att redigera filen ”/ etc/sysconfig/nätverk/dhcp” och ändra den `DHCLIENT_SET_HOSTNAME` parameter till följande:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Kommentera ut eller ta bort följande rader om de finns i ”/ etc/sudoers”:
    
     Som standard targetpw # be om lösenordet för målanvändaren d.v.s. rot alla ALL=(ALL) alla # varning! Använd endast denna tillsammans med 'Standardvärden targetpw'!
13. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.
14. Skapa inte växlingsutrymme på OS-disk.
    
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Obs: Ställ in på vad du behöver den ska.
15. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - avetablering
    # <a name="export-histsize0"></a>Exportera HISTSIZE = 0
    # <a name="logout"></a>logga ut
16. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.

- - -
## <a name="prepare-opensuse-131"></a>Förbereda openSUSE 13,1 +
1. I rutan i mitten av Hyper-V Manager, Välj den virtuella datorn.
2. Klicka på **Anslut** att öppna fönstret för den virtuella datorn.
3. Shell, kör kommandot '`zypper lr`'. Om det här kommandot returnerar utdata liknar följande och sedan databaserna är korrekt konfigurerat--krävs inte några justeringar (Observera att versionsnumren kan variera):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Om kommandot returnerar ”inga databaser som definierats...” använder du följande kommandon för att lägga till dessa repor:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Du kan kontrollera databaser har lagts till genom att köra kommandot '`zypper lr`' igen. Om en uppdatering som databaser inte är aktiverad kan du aktivera det med följande kommando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Uppdatera kernel till den senaste tillgängliga versionen:
   
        # sudo zypper up kernel-default
   
    Eller uppdatera datorn med de senaste korrigeringarna:
   
        # sudo zypper update
5. Installera Azure Linux-agenten.
   
   # <a name="sudo-zypper-install-walinuxagent"></a>sudo zypper installera WALinuxAgent
6. Ändra raden kernel Start i grub konfigurationen så att ytterligare kernel parametrar för Azure. Det gör du genom att öppna ”/ boot/grub/menu.lst” i en textredigerare och se till att standard-kärnan innehåller följande parametrar:
   
     konsolen = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure support med felsökning av problem. Dessutom, ta bort följande parametrar från kernel-Start rad om de finns:
   
     reservera libata.atapi_enabled=0 = 0x1f0, 0x8
7. Det rekommenderas att redigera filen ”/ etc/sysconfig/nätverk/dhcp” och ändra den `DHCLIENT_SET_HOSTNAME` parameter till följande:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Viktigt:** i ”/ etc/sudoers”, kommentera ut eller ta bort följande rader om de finns:
   
     Som standard targetpw # be om lösenordet för målanvändaren d.v.s. rot alla ALL=(ALL) alla # varning! Använd endast denna tillsammans med 'Standardvärden targetpw'!
9. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.
10. Skapa inte växlingsutrymme på OS-disk.
    
    Azure Linux-agenten kan automatiskt konfigurera växlingsutrymme med den lokala resurs disken som är kopplad till den virtuella datorn när du har etablerat på Azure. Observera att den lokala resurs disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg), ändra följande parametrar i /etc/waagent.conf på rätt sätt:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Obs: Ställ in på vad du behöver den ska.
11. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - avetablering
    # <a name="export-histsize0"></a>Exportera HISTSIZE = 0
    # <a name="logout"></a>logga ut
12. Kontrollera Azure Linux-agenten körs vid start:
    
        # sudo systemctl enable waagent.service
13. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att använda din virtuella hårddisk SUSE Linux för att skapa nya virtuella datorer i Azure. Om att du överföra VHD-filen till Azure Se [skapa ett Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

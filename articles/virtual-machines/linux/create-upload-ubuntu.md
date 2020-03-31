---
title: Skapa och ladda upp en Ubuntu Linux VHD i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Ubuntu Linux-operativsystem.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066738"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Förbereda en virtuell Ubuntu-dator för Azure


Ubuntu publicerar nu officiella Azure VHD:er för nedladdning på [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/). Om du behöver skapa din egen specialiserade Ubuntu-avbildning för Azure, i stället för att använda den manuella proceduren nedan rekommenderas att börja med dessa kända fungerande virtuella hårddiskar och anpassa efter behov. De senaste bildversionerna finns alltid på följande platser:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har installerat ett Ubuntu Linux-operativsystem på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns [i Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu installationsanteckningar**

* Se även [Allmänna Linux-installationsanteckningar](create-upload-generic.md#general-linux-installation-notes) för fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hårddisk**.  Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten konvertera-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standardpartitioner i stället för LVM (ofta standard för många installationer). Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar om så önskas.
* Konfigurera inte en växlingspartition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att raw-diskstorleken är en multipel av 1 MB före konvertering. Mer information finns i [Linux Installationsanteckningar.](create-upload-generic.md#general-linux-installation-notes)

## <a name="manual-steps"></a>Manuella steg
> [!NOTE]
> Innan du försöker skapa din egen anpassade Ubuntu-avbildning för Azure bör [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) du överväga att använda de färdiga och testade avbildningarna från i stället.
> 
> 

1. Välj den virtuella datorn i mittrutan i Hyper-V-hanteraren.

2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.

3. Ersätt de aktuella databaserna i avbildningen för att använda Ubuntus Azure-databas. Stegen varierar något beroende på Ubuntu-versionen.
   
    Innan `/etc/apt/sources.list`du redigerar rekommenderas att göra en säkerhetskopia:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure-avbildningarna följer nu HWE-kärnan *(Hardware Enablement).* Uppdatera operativsystemet till den senaste kärnan genom att köra följande kommandon:

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Se även:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Ändra kärnstartlinjen för Grub för att inkludera ytterligare kärnparametrar för Azure. Om du `/etc/default/grub` vill göra detta öppet i `GRUB_CMDLINE_LINUX_DEFAULT` en textredigerare hittar du variabeln som heter (eller lägger till den om det behövs) och redigerar den så att den innehåller följande parametrar:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Spara och stäng filen och `sudo update-grub`kör sedan . Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten, vilket kan hjälpa Azure teknisk support med felsökningsproblem.

6. Kontrollera att SSH-servern är installerad och konfigurerad för att starta vid start.  Detta är vanligtvis standard.

7. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Paketet `walinuxagent` kan ta `NetworkManager` `NetworkManager-gnome` bort paketen och, om de är installerade.


1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klicka på **Åtgärd -> stäng av** i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="references"></a>Referenser
[Ubuntu maskinvaruaktivering (HWE) kärna](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din virtuella Ubuntu Linux-hårddisk för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


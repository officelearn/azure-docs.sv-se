---
title: Skapa och ladda upp en Ubuntu Linux VHD i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Ubuntu Linux operativ system.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: mimckitt
ms.openlocfilehash: e8226322ad1aa9a1079834cc26b4ff8a1b40a204
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750164"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Förbered en virtuell Ubuntu-dator för Azure


Ubuntu publicerar nu officiella Azure-VHD: er för hämtning på [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/). Om du behöver skapa en egen specialiserad Ubuntu-avbildning för Azure, i stället för att använda den manuella proceduren nedan, rekommenderar vi att du börjar med de kända virtuella hård diskarna och anpassar efter behov. De senaste avbildnings versionerna kan alltid hittas på följande platser:

* Ubuntu 12.04/exakt: [Ubuntu-12,04-Server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusted: [Ubuntu-14,04-Server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/xenial: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. VHD. zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [Cosmic-Server-cloudimg-amd64. VHD. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har installerat ett Ubuntu Linux operativ system på en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

**Installations information för Ubuntu**

* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten Convert-VHD.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på data diskar om det är lämpligt.
* Konfigurera inte en swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Manuella steg
> [!NOTE]
> Innan du försöker skapa en egen anpassad Ubuntu-avbildning för Azure bör du överväga att använda de förbyggda och testade avbildningarna från [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) i stället.
> 
> 

1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.

2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.

3. Ersätt de aktuella databaserna i avbildningen så att de använder Ubuntu Azure-lagringsplatsen. Stegen varierar något beroende på Ubuntu-versionen.
   
    Innan du redigerar `/etc/apt/sources.list`bör du göra en säkerhets kopia:
   
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

4. Ubuntu Azure-avbildningarna följer nu HWE-kärnan ( *Hardware enableion* ). Uppdatera operativ systemet till den senaste kernel genom att köra följande kommandon:

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

    **Se även:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Ändra start raden för kernel för grub för att inkludera ytterligare kernel-parametrar för Azure. Om du vill göra det här öppnar du `/etc/default/grub` i en text redigerare, letar upp variabeln som heter `GRUB_CMDLINE_LINUX_DEFAULT` (eller lägger till den vid behov) och redigerar den för att inkludera följande parametrar:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Spara och Stäng den här filen och kör sedan `sudo update-grub`. Detta säkerställer att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure Technical Support med fel söknings problem.

6. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.

7. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  `walinuxagent` paketet kan ta bort `NetworkManager` och `NetworkManager-gnome` paket, om de är installerade.


1. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="references"></a>Referenser
[HWE-kernel (Ubuntu Hardware enableion)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Ubuntu Linux virtuella hård disk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


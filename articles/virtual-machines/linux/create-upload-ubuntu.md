---
title: Skapa och ladda upp en Ubuntu Linux VHD i Azure
description: "Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller ett Ubuntu Linux-operativsystem."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 4496b34ff88ca1e08cc74788ae09d787d4399eaf
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Förbered en virtuell Ubuntu-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Officiell Ubuntu molnet bilder
Ubuntu nu publicerar officiella Azure virtuella hårddiskar för hämtning på [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Om du behöver skapa särskilda Ubuntu-avbildningen för Azure snarare rekommenderas än att använda manuell proceduren nedan att börja med dessa kända fungerar virtuella hårddiskar och anpassa efter behov. De senaste versionerna av avbildningen kan alltid hittas på följande platser:

* Ubuntu 12.04/exakt: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/sin: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har installerat ett Ubuntu Linux-operativsystem till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Instruktioner finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu installationsinformation**

* Se även [allmänna Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer tips om hur du förbereder Linux för Azure.
* VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet convert-vhd.
* När du installerar Linux-system rekommenderas att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar som önskade.
* Konfigurera inte en byte-partition på OS-disk. Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken.  Mer information om detta finns i stegen nedan.
* Alla de virtuella hårddiskarna måste ha storlekar som multiplar av 1 MB.

## <a name="manual-steps"></a>Manuella steg
> [!NOTE]
> Innan du försöker skapa en egen anpassad avbildning Ubuntu för Azure, Överväg att använda fördefinierade och testade avbildningar från [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) i stället.
> 
> 

1. I rutan i mitten av Hyper-V Manager, Välj den virtuella datorn.

2. Klicka på **Anslut** att öppna fönstret för den virtuella datorn.

3. Ersätt aktuella databaserna i avbildningen som ska användas Ubuntu's Azure repor. Stegen variera beroende på vilken Ubuntu-version.
   
    Innan du redigerar `/etc/apt/sources.list`, rekommenderas att säkerhetskopiera:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure-avbildningar följer nu det *maskinvara aktivering* kernel (HWE). Uppdatera operativsystemet för senaste kerneln genom att köra följande kommandon:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Se även:**
    - [https://Wiki.Ubuntu.com/kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://Wiki.Ubuntu.com/kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Ändra raden kernel-Start för Grub att inkludera ytterligare kernel parametrar för Azure. Att göra den här öppna `/etc/default/grub` i en textredigerare, söka efter den variabel som kallas `GRUB_CMDLINE_LINUX_DEFAULT` (eller lägga till det om det behövs) och redigera den för att inkludera följande parametrar:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Spara och stäng filen och kör sedan `sudo update-grub`. Detta säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan hjälpa Azure teknisk support med felsökning av problem.

6. Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas.  Detta är vanligtvis standardvärdet.

7. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    Den `walinuxagent` paketet kan ta bort den `NetworkManager` och `NetworkManager-gnome` paket, om de är installerade.

8. Kör följande kommandon för att ta bort etableringen av den virtuella datorn och förbereda den för att etablera i Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Klicka på **åtgärd -> stängs ned** i Hyper-V Manager. Din Linux VHD är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att använda din virtuella hårddisk Ubuntu Linux för att skapa nya virtuella datorer i Azure. Om att du överföra VHD-filen till Azure finns i steg 2 och 3 i [skapa och ladda upp en virtuell hårddisk som innehåller Linux-operativsystem](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="references"></a>Referenser
Ubuntu maskinvara aktivering (HWE) kernel:

* [http://Blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://Blog.utlemming.org/2015/02/1204-Azure-cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)


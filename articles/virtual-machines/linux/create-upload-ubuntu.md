---
title: Skapa och ladda upp en virtuell Hårddisk Ubuntu Linux i Azure
description: Lär dig att skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller ett Ubuntu Linux-operativsystem.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 5b2f902389b96136e0ea0c4c58f5e8be8144a248
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056144"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Förbered en virtuell Ubuntu-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Officiella Ubuntu cloud-avbildningar
Ubuntu nu publicerar officiella virtuella Azure-hårddiskar ned från [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Om du vill skapa en egen specialiserade Ubuntu-avbildning för Azure, snarare rekommenderas än manuell proceduren nedan att börja med dessa kända arbeta virtuella hårddiskar och anpassa efter behov. Den senaste versionen av avbildningen finns alltid på följande platser:

* Ubuntu 12.04/exakt: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/sin: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat ett Ubuntu Linux-operativsystem till en virtuell hårddisk. Det finns flera olika verktyg för att skapa VHD-filer, till exempel en virtualiseringslösning, till exempel Hyper-V. Anvisningar finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu installationsinformation**

* Se även [allmän information för Linux-Installation](create-upload-generic.md#general-linux-installation-notes) fler tips om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten convert-vhd.
* När du installerar Linux-systemet rekommenderas att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar om du så föredrar.
* Konfigurera inte swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk.  Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.

## <a name="manual-steps"></a>Manuella steg
> [!NOTE]
> Innan du försöker skapa en egen anpassad Ubuntu-avbildning för Azure genom att använda fördefinierade och testade bilder från [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) i stället.
> 
> 

1. I den mittersta rutan av Hyper-V Manager väljer du den virtuella datorn.

2. Klicka på **Connect** att öppna fönstret för den virtuella datorn.

3. Ersätt de aktuella lagringsplatserna i avbildningen som ska användas Ubuntu's Azure lagringsplatser. Stegen variera beroende på Ubuntu-version.
   
    Innan du redigerar `/etc/apt/sources.list`, rekommenderar vi att du gör en säkerhetskopia:
   
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

4. Ubuntu Azure-avbildningar följer nu den *maskinvara aktivering* kernel (HWE). Uppdatera operativsystemet för senaste kerneln genom att köra följande kommandon:

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
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Ändra i kernel boot line för Grub att inkludera ytterligare kernel parametrar för Azure. Att göra den här öppen `/etc/default/grub` i en textredigerare och hitta variabeln heter `GRUB_CMDLINE_LINUX_DEFAULT` (eller lägga till den om det behövs) och redigera det med följande parametrar:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Spara och stäng filen och kör sedan `sudo update-grub`. Det säkerställer att alla konsolmeddelanden skickas till den första seriella porten som kan bidra till teknisk support för Azure med felsökning av problem.

6. Kontrollera att SSH-servern är installerad och konfigurerad för att starta när datorn startas.  Detta är vanligtvis standardinställningen.

7. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    Den `walinuxagent` paketet kan ta bort den `NetworkManager` och `NetworkManager-gnome` paket, om de är installerade.

8. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering i Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Klicka på **åtgärd -> Stäng ned** i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

## <a name="references"></a>Referenser
[Ubuntu maskinvara aktivering (HWE) kernel](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Ubuntu Linux-virtuell hårddisk för att skapa nya virtuella datorer i Azure. Om detta är första gången som du laddar upp VHD-filen till Azure, se [skapa en Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).





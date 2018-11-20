---
title: Förbered en Debian VHD för Linux i Azure | Microsoft Docs
description: Lär dig hur du skapar Debian VHD-avbildningar för distribution i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: ce2b9811baffea85cfa9a542fb5f93652daf39c8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976458"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Förbered en Debian VHD för Azure
## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet förutsätter att du redan har installerat en Debian Linux-operativsystem från en ISO-fil som hämtats från den [Debian webbplats](https://www.debian.org/distrib/) till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer Hyper-V är bara ett exempel. Instruktioner med Hyper-V finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Installationsinformation
* Se även [allmän information för Linux-Installation](create-upload-generic.md#general-linux-installation-notes) fler tips om hur du förbereder Linux för Azure.
* Nyare VHDX-formatet stöds inte i Azure. Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller **convert-vhd** cmdlet.
* När du installerar Linux-systemet, rekommenderas att du använder standard partitioner i stället för LVM (ofta standard i många fall räcker). Detta undviker LVM namnet står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas för datadiskar om du så föredrar.
* Konfigurera inte swap-partition på OS-disken. Azure Linux-agenten kan konfigureras för att skapa en växlingsfil på temporär disk. Mer information finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk, måste du kontrollera att rådata diskens storlek är en multipel av 1MB före omvandlingen. Mer information finns i [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Använd Azure – hantera för att skapa Debian VHD: er
Det finns ett verktyg för att generera Debian VHD: er för Azure, till exempel den [azure-hantera](https://github.com/credativ/azure-manage) skript från [Credativ](http://www.credativ.com/). Det här är den rekommenderade metoden jämfört med att skapa en avbildning från grunden. Till exempel skapa ett Debian 8 VHD kör följande kommandon för att ladda ned den `azure-manage` -verktyget (och beroenden) och kör den `azure_build_image` skript:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Förbered en Debian VHD manuellt
1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Connect** att öppna ett konsolfönster för den virtuella datorn.
3. Om du har installerat Operativsystemet med en ISO som sedan kommentera bort någon av staplarna rör ”`deb cdrom`” i `/etc/apt/source.list`.

4. Redigera den `/etc/default/grub` -filen och ändra den **GRUB_CMDLINE_LINUX** parametern på följande sätt för att inkludera ytterligare kernel parametrar för Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Återskapa grub och kör:

        # sudo update-grub

6. Lägg till Debian's Azure databaser /etc/apt/sources.list för Debian 8 eller 9:

    **Debian 8.x ”Jessie”**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x ”Stretch”**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. För Debian 9 och senare rekommenderas att använda nya Debian molnet kerneln för användning med virtuella datorer i Azure. För att installera den här nya kernel måste du först skapa en fil med namnet /etc/apt/preferences.d/linux.pref med följande innehåll:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Kör ”sudo apt-get install linux-avbildning-cloud-amd64” att installera den nya Debian Cloud-kerneln.

9. Avetablera den virtuella datorn och förbereda den för etablering på Azure och kör:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Klicka på **åtgärd** -> Stäng ned i Hyper-V Manager. VHD för Linux är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Debian virtuell hårddisk för att skapa nya virtuella datorer i Azure. Om detta är första gången som du laddar upp VHD-filen till Azure, se [skapa en Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


---
title: "Förbereda en Debian Linux VHD i Azure | Microsoft Docs"
description: "Lär dig hur du skapar Debian 7 och 8 VHD-filer för distribution i Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 9b32b298f141e9ee54b4c42d3ee9c15174daf8b7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Förbered en Debian VHD för Azure
## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet förutsätter att du redan har installerat en Debian Linux-operativsystem från en ISO-fil som hämtas från den [Debian webbplats](https://www.debian.org/distrib/) till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer. Hyper-V är bara ett exempel. Instruktioner med Hyper-V finns i [installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Installationsinformation
* Se även [allmänna Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer tips om hur du förbereder Linux för Azure.
* Nyare VHDX-format stöds inte i Azure. Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller **convert-vhd** cmdlet.
* När du installerar Linux-system rekommenderas att du använder standard partitioner i stället för LVM (ofta är standard för många installationer). Det här undviker LVM står i konflikt med klonade virtuella datorer, särskilt om en OS-disk någonsin måste vara kopplad till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) får användas i datadiskar som önskade.
* Konfigurera inte en byte-partition på OS-disk. Azure Linux-agenten kan konfigureras för att skapa en växlingsfil på tillfällig disken. Mer information om detta finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justeras till 1MB. Vid konvertering från en rå disk till virtuell Hårddisk måste du kontrollera att rådata diskens storlek är en multipel av 1MB innan konverteringen. Se [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) för mer information.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Använd hantera i Azure för att skapa Debian virtuella hårddiskar
Det finns ett verktyg för att generera Debian virtuella hårddiskar på Azure, som den [azure-hantera](https://github.com/credativ/azure-manage) skript från [credativ](http://www.credativ.com/). Det här är den rekommenderade metoden jämfört med att skapa en avbildning från grunden. Till exempel för att skapa en Debian 8 VHD kör följande kommandon för att hämta azure hantera (och beroenden) och kör skriptet azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Förbered manuellt en Debian virtuell Hårddisk
1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** att öppna ett konsolfönster för den virtuella datorn.
3. Kommentera raden för **deb cdrom** i `/etc/apt/source.list` om du ställer in den virtuella datorn mot en ISO-fil.
4. Redigera den `/etc/default/grub` filen och ändra den **GRUB_CMDLINE_LINUX** parametern enligt följande för att inkludera ytterligare kernel parametrar för Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Återskapa grub och kör:
   
        # sudo update-grub
6. Lägg till Debian's Azure databaser /etc/apt/sources.list för Debian 7 eller 8:
   
    **Debian 7.x ”Wheezy”**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x ”Jessie”**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Installera Azure Linux-agenten:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Det är Debian 7 krävs för att köra kärnan i 3,16 från wheezy backports-databasen. Först skapar du en fil som heter /etc/apt/preferences.d/linux.pref med följande innehåll:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Kör ”sudo lgh get installera linux-bild-amd64” så här installerar du nya kernel.
3. Ta bort etableringen av den virtuella datorn och förbereda den för att etablera på Azure och kör:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Klicka på **åtgärd** i Hyper-V Manager -> stängs ned. Din Linux VHD är nu redo att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att använda din Debian virtuell hårddisk för att skapa nya virtuella datorer i Azure. Om att du överföra VHD-filen till Azure Se [skapa ett Linux VM från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


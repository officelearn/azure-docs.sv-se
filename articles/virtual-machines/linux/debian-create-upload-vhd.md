---
title: Förbered en Debian Linux VHD
description: Lär dig hur du skapar Debians VHD-avbildningar för VM-distributioner i Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d54f7a11d929c31fee29a788eb3a2ae2cc8f2703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066715"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Förbereda en Debian-VIRTUELLD för Azure
## <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du redan har installerat ett Debian Linux-operativsystem från en ISO-fil som laddats ner från [Debians webbplats](https://www.debian.org/distrib/) till en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer. Hyper-V är bara ett exempel. Instruktioner om hur du använder Hyper-V finns [i Installera Hyper-V-rollen och Konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Installationsanteckningar
* Se även [Allmänna Linux-installationsanteckningar](create-upload-generic.md#general-linux-installation-notes) för fler tips om hur du förbereder Linux för Azure.
* Det nyare VHDX-formatet stöds inte i Azure. Du kan konvertera disken till VHD-format med Hyper-V-hanteraren eller cmdleten **konvertera-vhd.**
* När du installerar Linux-systemet rekommenderas att du använder standardpartitioner i stället för LVM (ofta standard för många installationer). Detta kommer att undvika LVM namnkonflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för felsökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på datadiskar om så önskas.
* Konfigurera inte en växlingspartition på OS-disken. Azure Linux-agenten kan konfigureras för att skapa en växlingsfil på den temporära resursdisken. Mer information finns i stegen nedan.
* Alla virtuella hårddiskar på Azure måste ha en virtuell storlek justerad till 1 MB. När du konverterar från en rådisk till virtuell hårddisk måste du se till att rådiskstorleken är en multipel av 1 MB före konvertering. Mer information finns i [Linux Installation Notes](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Använd Azure-Manage för att skapa Debians virtuella hårddiskar
Det finns verktyg för att generera Debian-virtuella hårddiskar för Azure, till exempel [azure-manage-skript](https://github.com/credativ/azure-manage) från [Credativ](https://www.credativ.com/). Detta är den rekommenderade metoden jämfört med att skapa en bild från grunden. Om du till exempel vill skapa en Virtuell Debian 8-hårddisk kör du följande kommandon för att hämta `azure-manage` verktyget (och beroenden) och köra skriptet: `azure_build_image`

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Förbereda en Debian-VHD manuellt
1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** om du vill öppna ett konsolfönster för den virtuella datorn.
3. Om du har installerat operativsystemet med en ISO,`deb cdrom`kommentera `/etc/apt/source.list`ut någon rad som rör " " i .

4. Redigera `/etc/default/grub` filen och ändra **parametern GRUB_CMDLINE_LINUX** enligt följande för att inkludera ytterligare kärnparametrar för Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Bygg om grub och kör:

        # sudo update-grub

6. Lägg till Debians Azure-databaser i /etc/apt/sources.list för antingen Debian 8 eller 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

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

8. För Debian 9+ rekommenderas att använda den nya Debian Cloud-kärnan för användning med virtuella datorer i Azure. För att installera denna nya kärna, först skapa en fil som heter /etc/apt/preferences.d/linux.pref med följande innehåll:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Kör sedan "sudo apt-get install linux-image-cloud-amd64" för att installera den nya Debian Cloud-kärnan.

9. Avetablera den virtuella datorn och förbered den för etablering på Azure och kör:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Klicka på **Åtgärd** -> stäng av i Hyper-V-hanteraren. Din virtuella Linux-hårddisk är nu klar att överföras till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din virtuella Debian-hårddisk för att skapa nya virtuella datorer i Azure. Om det är första gången du överför VHD-filen till Azure läser du [Skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).


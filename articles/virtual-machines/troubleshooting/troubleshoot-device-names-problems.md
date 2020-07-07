---
title: Felsöka virtuella Linux-enheters enhets namns ändringar i Azure | Microsoft Docs
description: Förklarar varför virtuella Linux-enheters enhets namn ändras och hur du löser problemet.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "71058208"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Felsöka ändringar av enhets namn för virtuella Linux-datorer

I den här artikeln förklaras varför enhets namn ändras när du startar om en virtuell Linux-dator eller återansluter data diskarna. Artikeln innehåller också lösningar på det här problemet.

## <a name="symptoms"></a>Symtom
Följande problem kan uppstå när du kör virtuella Linux-datorer i Microsoft Azure:

- Den virtuella datorn kan inte starta efter en omstart.
- När data diskar kopplas från och återkopplas, ändras disk enhets namnen.
- Ett program eller skript som refererar till en disk med hjälp av enhets namnet Miss lyckas eftersom enhets namnet har ändrats.

## <a name="cause"></a>Orsak

Enhets Sök vägar i Linux garanterar inte att de är konsekventa i omstarter. Enhets namn består av huvud siffror (bokstäver) och mindre siffror. När driv rutinen för Linux-lagringsenheten identifierar en ny enhet tilldelar driv rutinen huvud-och del nummer från det tillgängliga intervallet till enheten. När en enhet tas bort frigörs enhets numren för åter användning.

Problemet beror på att enhets genomsökning i Linux har schemalagts av SCSI-undersystemet för att ske asynkront. Det innebär att namnet på en enhets Sök väg kan variera mellan omstarter.

## <a name="solution"></a>Lösning

Lös problemet genom att använda beständiga namn. Det finns fyra sätt att använda beständiga namn: efter fil Systems etikett, efter UUID, efter ID eller sökväg. Vi rekommenderar att du använder fil Systems etiketten eller UUID: n för virtuella Azure Linux-datorer.

De flesta distributioner ger `fstab` parametrarna **nomisslyckande** eller **nobootwait** . Dessa parametrar gör det möjligt för ett system att starta när disken inte kan monteras vid start. Mer information om dessa parametrar finns i distributions dokumentationen. Information om hur du konfigurerar en virtuell Linux-dator för att använda en UUID när du lägger till en datadisk finns i [Anslut till den virtuella Linux-datorn för att montera den nya disken](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

När Azure Linux-agenten installeras på en virtuell dator använder agenten udev-regler för att skapa en uppsättning symboliska länkar under/dev/disk/Azure-sökvägen. Program och skript använder udev-regler för att identifiera diskar som är anslutna till den virtuella datorn, samt disk typen och diskens LUN.

Om du redan har redigerat din fstab på ett sådant sätt att den virtuella datorn inte startar och du inte kan använda SSH till den virtuella datorn kan du använda den [virtuella datorns serie konsol](./serial-console-linux.md) för att ange [enanvändarläge](./serial-console-grub-single-user-mode.md) och ändra din fstab.

### <a name="identify-disk-luns"></a>Identifiera disk-LUN

Program använder LUN för att hitta alla anslutna diskar och skapa symboliska länkar. Azure Linux-agenten innehåller udev-regler som konfigurerar symboliska länkar från ett LUN till enheterna:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

LUN-information från Linux-gäst kontot hämtas med hjälp av `lsscsi` eller ett liknande verktyg:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

LUN-informationen för gästen används med metadata för Azure-prenumerationen för att hitta den virtuella hård disken i Azure Storage som innehåller partitionens data. Du kan till exempel använda `az` CLI:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Identifiera UUID för filesystem med hjälp av blkid

Program och skript läser utdata från `blkid` , eller liknande informations källor, för att skapa symboliska länkar i/dev-sökvägen. Utdata visar UUID: n för alla diskar som är anslutna till den virtuella datorn och deras tillhör ande enhets fil:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Udev-regler för Azure Linux-Agent konstruerar en uppsättning symboliska länkar under/dev/disk/Azure-sökvägen:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Programmen använder länkarna för att identifiera start disk enheten och resurs (tillfällig) disk. I Azure bör programmen titta på/dev/disk/Azure/root-part1-eller/dev/disk/Azure-Resource-part1-sökvägar för att identifiera dessa partitioner.

Eventuella ytterligare partitioner från `blkid` listan finns på en datadisk. Program behåller UUID för dessa partitioner och använder en sökväg för att identifiera enhets namnet vid körning:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Hämta de senaste Azure Storage reglerna

Du kan hämta de senaste Azure Storage reglerna genom att köra följande kommandon:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Se även

Mer information finns i följande artiklar:

- [Ubuntu: använda UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: beständig namngivning](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: vilka UUID: er som kan användas för dig](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduktion till enhets hantering i ett modernt Linux-system](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


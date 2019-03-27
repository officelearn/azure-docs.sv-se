---
title: Felsöka ändringar av Linux VM enhetsnamn i Azure | Microsoft Docs
description: Förklarar varför ändra namn i Linux VM-enhet och hur du löser problemet.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d636d5f31e78828a518882091af29b25f7219304
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443990"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Felsöka ändringar av enhetsnamn Linux VM

Den här artikeln förklarar varför enhetsnamn ändras när du startar om en Linux-VM eller ansluta datadiskar. Artikeln innehåller också lösningar för det här problemet.

## <a name="symptoms"></a>Symtom
Följande problem kan uppstå när du kör virtuella Linux-datorer i Microsoft Azure:

- Den virtuella datorn inte kan startas efter en omstart.
- När datadiskar är oberoende och återansluta, har disk-enhetsnamnen ändrats.
- Ett program eller skript som refererar till en disk med hjälp av namnet på enheten misslyckas eftersom namnet på enheten har ändrats.

## <a name="cause"></a>Orsak

Enhetssökvägarna i Linux är inte garanterat enhetlig mellan omstarter. Enhetsnamn består av större tal (versaler) och lägre tal. När Linux storage drivrutinen upptäcker en ny enhet, tilldelar drivrutinen högre och den lägre tal från det tillgängliga intervallet till enheten. När en enhet tas bort, frigörs enheten siffror för återanvändning.

Problemet uppstår eftersom enheten i Linux schemaläggs av SCSI-undersystem ske asynkront. Ett enhetsnamn för sökvägen kan därför kan variera mellan olika omstarter.

## <a name="solution"></a>Lösning

Lös problemet genom att använda beständiga naming. Det finns fyra sätt att använda beständiga naming: av filsystem etikett, UUID, efter ID eller efter sökväg. Vi rekommenderar att du använder filsystem etiketten eller UUID för virtuella Linux-datorer.

De flesta distributioner ger den `fstab` **nofail** eller **nobootwait** parametrar. Dessa parametrar kan ett system att starta när disken inte kan montera vid start. Kontrollera din distribution-dokumentationen för mer information om dessa parametrar. Information om hur du konfigurerar en Linux-VM för att använda en UUID när du lägger till en datadisk finns i [Anslut till Linux VM att montera den nya disken](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

När Azure Linux-agent installeras på en virtuell dator, använder agenten Udev regler för att konstruera en uppsättning symboliska länkar under /dev/disk/azure-sökvägen. Program och skript kan du använda Udev regler för att identifiera diskar som är anslutna till den virtuella datorn, tillsammans med disktypen och disk LUN.

Om du redan har redigerat din fstab så att den virtuella datorn inte startar och du kan inte SSH till den virtuella datorn, kan du använda den [VM Seriekonsolen](./serial-console-linux.md) ange [enanvändarläge](./serial-console-grub-single-user-mode.md) och ändra din fstab.

### <a name="identify-disk-luns"></a>Identifiera disken LUN

Program använda LUN för att hitta alla anslutna diskar och att konstruera symboliska länkar. Azure Linux-agent innehåller Udev-regler som har konfigurerat symboliska länkar från en LUN till enheterna:

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

LUN-information från Linux gästkontot hämtas med hjälp av `lsscsi` eller ett liknande verktyg:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Gästen LUN informationen används med Azure-prenumeration metadata för att hitta den virtuella Hårddisken i Azure Storage som innehåller data för partitionen. Du kan till exempel använda den `az` CLI:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Identifiera UUID: N för filsystem med hjälp av blkid

Program och skript läsa utdata från `blkid`, eller liknande informationskällorna att konstruera symboliska länkar i /dev sökvägen. Utdata visar UUID för alla diskar som är kopplade till den virtuella datorn och deras associerade-fil:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux agent Udev regler konstruera en uppsättning symboliska länkarna under /dev/disk/azure-sökväg:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Program använda länkarna för att identifiera disken startenheten och resursdisk (tillfällig). I Azure, program bör se ut i /dev/disk/azure/root-part1 eller /dev/disk/azure-resource-part1 sökvägarna att identifiera de här partitionerna.

Alla ytterligare partitioner från den `blkid` listan finns på en datadisk. Program Underhåll UUID för de här partitionerna och använda en sökväg för att identifiera namnet på enheten vid körning:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Hämta de senaste Azure Storage-reglerna

Om du vill hämta de senaste Azure Storage-reglerna, kör du följande kommandon:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Se också

Mer information finns i följande artiklar:

- [Ubuntu: Med hjälp av UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Beständiga namngivning](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Vad du kan göra med UUID: N](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduktion till hantering av enheter i ett modernt Linux-system](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


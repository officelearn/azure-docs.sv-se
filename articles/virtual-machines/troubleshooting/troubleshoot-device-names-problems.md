---
title: Felsöka namnändringar för Linux VM-enheten i Azure | Microsoft-dokument
description: Förklarar varför Linux VM-enhetsnamn ändras och hur du löser problemet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058208"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Felsöka namnändringar för Linux VM-enheten

I den här artikeln beskrivs varför enhetsnamn ändras när du har startat om en virtuell Linux-dator eller återansluter datadiskarna. Artikeln innehåller också lösningar på detta problem.

## <a name="symptoms"></a>Symtom
Följande problem kan uppstå när du kör virtuella Linux-datorer i Microsoft Azure:

- Den virtuella datorn kan inte starta efter en omstart.
- När datadiskar tas bort och återansluts ändras diskenhetsnamnen.
- Ett program eller skript som refererar till en disk med enhetsnamnet misslyckas eftersom enhetsnamnet har ändrats.

## <a name="cause"></a>Orsak

Enhetssökvägar i Linux är inte garanterade att vara konsekventa över omstarter. Enhetsnamn består av större siffror (bokstäver) och mindre nummer. När Linux-lagringsenheten drivrutinen upptäcker en ny enhet, tilldelar drivrutinen större och mindre nummer från det tillgängliga intervallet till enheten. När en enhet tas bort frigörs enhetsnumren för återanvändning.

Problemet uppstår eftersom enhetsskanning i Linux schemaläggs av SCSI-undersystemet så att det sker asynkront. Därför kan ett enhetssökvägsnamn variera mellan omstarter.

## <a name="solution"></a>Lösning

LÃ¶s problemet genom att anse beständigt namn. Det finns fyra sätt att använda beständigt namn: efter filsystemetikett, UUID, ID eller efter sökväg. Vi rekommenderar att du använder filsystemetiketten eller UUID för virtuella Azure Linux-datorer.

De flesta distributioner ger `fstab` **nofail** eller **nobootwait** parametrar. Dessa parametrar gör det möjligt för ett system att starta när disken inte kan monteras vid start. Mer information om dessa parametrar finns i distributionsdokumentationen. Information om hur du konfigurerar en virtuell Linux-dator för att använda ett UUID när du lägger till en datadisk finns i [Anslut till Den virtuella datorn för Linux för att montera den nya disken](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

När Azure Linux-agenten installeras på en virtuell dator använder agenten Udev-regler för att skapa en uppsättning symboliska länkar under sökvägen /dev/disk/azure. Program och skript använder Udev-regler för att identifiera diskar som är kopplade till den virtuella datorn, tillsammans med disktyp och disk-LUN.

Om du redan har redigerat din fstab på ett sådant sätt att din virtuella dator inte startar och du inte kan SSH till din virtuella dator, kan du använda [VM Serial Console](./serial-console-linux.md) för att gå in i [enanvändarläge](./serial-console-grub-single-user-mode.md) och ändra din fstab.

### <a name="identify-disk-luns"></a>Identifiera LUN för disk

Program använder LUN för att hitta alla anslutna diskar och för att skapa symboliska länkar. Azure Linux-agenten innehåller Udev-regler som ställer in symboliska länkar från ett LUN till enheterna:

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

LUN-information från Linux-gästkontot `lsscsi` hämtas med hjälp av eller ett liknande verktyg:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Gäst-LUN-informationen används med Azure-prenumerationsmetadata för att hitta den virtuella hårddisken i Azure Storage som innehåller partitionsdata. Du kan till exempel `az` använda CLI:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Upptäck UUID-filer i filsystemet med hjälp av blkid

Program och skript läser `blkid`resultatet av , eller liknande informationskällor, för att skapa symboliska länkar i sökvägen /dev. Utdata visar UUID:erna för alla diskar som är kopplade till den virtuella datorn och tillhörande enhetsfil:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux-agenten Udev-reglerna skapar en uppsättning symboliska länkar under sökvägen /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Program använder länkarna för att identifiera startdiskenheten och resursdisken (efemära) disken. I Azure bör program leta i sökvägarna /dev/disk/azure/root-part1 eller /dev/disk/azure-resource-part1 för att identifiera dessa partitioner.

Alla ytterligare partitioner `blkid` från listan finns på en datadisk. Program underhåller UUID för dessa partitioner och använder en sökväg för att identifiera enhetsnamnet vid körning:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Hämta de senaste Azure Storage-reglerna

Om du vill hämta de senaste Azure Storage-reglerna kör du följande kommandon:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Se även

Mer information finns i följande artiklar:

- [Ubuntu: Använda UUID](https://help.ubuntu.com/community/UsingUUID)
- [Röd hatt: Ihållande namngivning](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Vad UUIDs kan göra för dig](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduktion till enhetshantering i ett modernt Linux-system](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


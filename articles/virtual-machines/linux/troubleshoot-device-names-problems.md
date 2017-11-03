---
title: "Felsöka Linux VM enheten namnändringar i Azure | Microsoft Docs"
description: "Förklarar varför ändra namn på Linux VM enheten och hur du löser problemet."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.openlocfilehash: 249d2cb42e2d8534af1e27da7f5d909b71eccbc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Felsöka Linux VM enheten namnändringar

Den här artikeln förklarar varför enhetsnamn ändra när du startar om en Linux VM eller återansluta datadiskar. Artikeln innehåller även lösningar på problemet.

## <a name="symptoms"></a>Symtom
Följande problem kan uppstå när du kör virtuella Linux-datorer i Microsoft Azure:

- Den virtuella datorn inte startar efter en omstart.
- När datadiskar oberoende och anbringas på nytt, har enheten disknamnen ändrats.
- Ett program eller skript som refererar till en disk med hjälp av namnet på en enhet misslyckas eftersom namnet på en enhet har ändrats.

## <a name="cause"></a>Orsak

Sökvägar till enheter i Linux är inte säkert att vara konsekvent mellan olika omstarter. Enhetsnamn består av större tal (versaler) och mindre tal. När drivrutinen Linux lagring identifierar en ny enhet, tilldelar drivrutinen högre och lägre siffror från intervall till enheten. När en enhet tas bort frigörs enheten siffror för återanvändning.

Problemet uppstår eftersom enheten skanning i Linux har schemalagts av SCSI-undersystemet ske asynkront. Därför kan kan en enhet sökväg variera mellan olika omstarter. 

## <a name="solution"></a>Lösning

Använd beständiga naming för att lösa problemet. Det finns fyra olika sätt att använda beständiga naming: av filsystem etikett, UUID, -ID: t eller sökväg. Vi rekommenderar att du använder filesystem etiketten eller UUID för virtuella Azure Linux-datorer. 

Ange de flesta distributioner av `fstab` **nofail** eller **nobootwait** parametrar. Dessa parametrar kan ett system för att starta om disken inte att montera vid start. Läs dokumentationen till din distribution för mer information om dessa parametrar. Information om hur du konfigurerar en Linux VM för att använda en UUID när du lägger till en datadisk finns [Anslut till Linux-VM för att montera den nya disken](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

När Azure Linux-agenten är installerad på en virtuell dator använder agenten Udev regler för att konstruera en uppsättning symboliska länkar under sökvägen /dev/disk/azure. Program och skript använder du Udev regler för att identifiera diskar som är kopplade till den virtuella datorn, tillsammans med typ av disk- och diskresurser LUN.

### <a name="identify-disk-luns"></a>Identifiera disk LUN

Program använda LUN att söka efter alla anslutna diskar och för att konstruera symboliska länkar. Azure Linux-agenten innehåller Udev regler som ställts in symboliska länkar från en LUN till enheter:

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

Gästen LUN-information används med metadata för Azure-prenumeration för att hitta den virtuella Hårddisken i Azure Storage som innehåller data för partitionen. Du kan till exempel använda den `az` CLI:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Identifiera filesystem UUID: er med hjälp av blkid

Program och skript läsa utdata från `blkid`, eller liknande informationskällor att konstruera symboliska länkar i /dev sökvägen. Utdata visar UUID: er för alla diskar som är kopplade till den virtuella datorn och deras associerade enheten fil:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux-agenten Udev regler konstruera en uppsättning symboliska länkar under /dev/disk/azure-sökväg:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Program använda länkarna för att identifiera disken startenheten och resurs (tillfälliga) disken. Program ska leta i /dev/disk/azure/root-part1 eller /dev/disk/azure-resource-part1 sökvägar att identifiera dessa partitioner i Azure.

Alla ytterligare partitioner från den `blkid` listan finns på en datadisk. Program Underhåll UUID för dessa partitioner och använda en sökväg för att identifiera namnet på en enhet vid körning:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Hämta de senaste Azure Storage-reglerna

Kör följande kommandon för att få de senaste Azure Storage-reglerna:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Se även

Mer information finns i följande artiklar:

- [Ubuntu: Med UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Beständiga namngivning av](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Vad UUID: er kan du göra](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduktion till hantering av enheter i en modern Linux-system](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


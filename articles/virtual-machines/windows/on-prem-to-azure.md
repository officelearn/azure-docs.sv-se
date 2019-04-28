---
title: Migrera från AWS och andra plattformar till Managed Disks i Azure | Microsoft Docs
description: Skapa virtuella datorer i Azure med hjälp av virtuella hårddiskar som överförts från andra moln som AWS eller andra virtualiseringsplattformar och utnyttja fördelarna med Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ad7bc10cb7b93bd4db9260f950ae4ca12aba44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126920"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrera från Amazon Web Services (AWS) och andra plattformar till Managed Disks i Azure

Du kan överföra VHD-filer från AWS eller lokala lösningar till Azure för att skapa virtuella datorer som drar nytta av Managed Disks. Azure Managed Disks eliminerar behovet av att hantera lagringskonton för virtuella Azure IaaS-datorer. Du behöver bara ange typ (Premium eller Standard) och storlek på disk och Azure skapar och hanterar disken åt dig. 

Du kan överföra generaliserade och specialiserade virtuella hårddiskar. 
- **Generaliserad virtuell Hårddisk** -har haft all personlig information tas bort med hjälp av Sysprep. 
- **Specialiserad VHD** -underhåller användarkonton, program och andra systemtillstånd från den ursprungliga virtuella datorn. 

> [!IMPORTANT]
> Innan du laddar upp alla VHD till Azure, bör du följa [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Du har befintliga AWS EC2-instanser som du vill migrera till Azure virtuella datorer med hanterade diskar                              | [Flytta en virtuell dator från Amazon Web Services (AWS) till Azure](aws-to-azure.md)                           |
| Du har en virtuell dator från en annan virtualiseringsplattform som du vill använda som en bild för att skapa flera virtuella Azure-datorer. | [Ladda upp en generaliserad virtuell Hårddisk och använda den för att skapa en ny virtuell dator i Azure](upload-generalized-managed.md) |
| Du har en unik anpassad virtuell dator som du vill återskapa i Azure.                                                      | [Ladda upp en specialiserad virtuell Hårddisk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Översikt över hanterade diskar

Azure Managed Disks förenklar hanteringen av virtuella datorer genom att ta bort behovet av att hantera lagringskonton. Hanterade diskar också förmånen från bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det innebär att diskar på olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Den placerar automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) vilket begränsar effekten av enskild lagringsskalningsenhetsfel bero på att maskinvara och programvarufel.
Utifrån dina behov kan välja du mellan fyra typer av lagringsalternativ. Läs om tillgängliga disktyper i vår artikel [Välj en disktyp av](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migrering till Managed Disks

Det här avsnittet hjälper dig att fatta det bästa beslutet på virtuell dator och disk-typer.

Om du planerar att migrera från ohanterade diskar till hanterade diskar, bör du vara medveten att användare med den [virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rollen kommer inte att kunna ändra storleken på virtuella datorn (som de skulle före konvertering). Det beror på att virtuella datorer med hanterade diskar kräver att användaren har behörigheten Microsoft.Compute/disks/write på OS-diskar.

### <a name="location"></a>Location

Välj en plats där Azure Managed Disks är tillgängliga. Om du migrerar till Premium Managed Disks kan också kontrollera att Premium storage är tillgängligt i regionen där du planerar att migrera till. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium Managed Disks kan behöva du uppdatera storleken på den virtuella datorn till Premium Storage kan storlek tillgängliga i den region där VM finns. Granska de storlekar som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaperna för virtuella datorer med Premium Storage och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Se till att det finns tillräckligt mycket bandbredd på den virtuella datorn att driva trafiken disk.

### <a name="disk-sizes"></a>Diskstorlekar

**Premium Managed Disks**

Det finns sju typer av premium-hanterade diskar som kan användas med den virtuella datorn och alla har specifika IOPs och dataflöde gränser. Ta hänsyn till dessa gränser när välja Premium-disktyp för din virtuella dator baserat på dina behov för ditt program när det gäller kapacitet, prestanda, skalbarhet och högsta läses in.

| Typen för Premium-diskar  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4 095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100  |2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund |150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

**Hanterade standarddiskar**

Det finns sju typer av hanterade standarddiskar som kan användas med den virtuella datorn. Var och en av dem har kapacitet för olika men har samma IOPS och dataflöde gränser. Välj typ av Standard Managed disks baserat på programmets kapacitetsbehov.

| Disk av standardtyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1 024 GB (1 TB)   | 2048 GB (2TB)    | 4 095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 

### <a name="disk-caching-policy"></a>Disk-principen för cachelagring 

**Premium Managed Disks**

Som standard disken Cachelagringsprincip är *skrivskyddad* för alla Premium datadiskar, och *skrivskyddad* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. Inaktivera diskcachelagring så att du kan få bättre prestanda för hög eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

### <a name="pricing"></a>Prissättning

Granska den [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Priser för Premium Managed Disks är samma som Premium Unmanaged Disks. Men priserna för Standard Managed Disks skiljer sig från vanliga ohanterade diskar.


## <a name="next-steps"></a>Nästa steg

- Innan du laddar upp alla VHD till Azure, bör du följa [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

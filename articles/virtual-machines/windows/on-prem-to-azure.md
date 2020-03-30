---
title: Migrera från AWS och andra plattformar till hanterade diskar i Azure
description: Skapa virtuella datorer i Azure med hjälp av virtuella hårddiskar som överförs från andra moln som AWS eller andra virtualiseringsplattformar och dra nytta av Azure Managed Disks.
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
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbce2969ccb508c2bf3ee33730d0b112caa45c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243164"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrera från Amazon Web Services (AWS) och andra plattformar till hanterade diskar i Azure

Du kan ladda upp VHD-filer från AWS eller lokala virtualiseringslösningar till Azure för att skapa virtuella datorer som drar nytta av hanterade diskar. Azure Managed Disks tar bort behovet av att hantera lagringskonton för virtuella Azure IaaS-datorer. Du behöver bara ange typ (Premium eller Standard) och storleken på disken du behöver, och Azure skapar och hanterar disken åt dig. 

Du kan ladda upp antingen generaliserade och specialiserade virtuella hårddiskar. 
- **Generaliserad virtuell hårddisk** - har fått all din personliga kontoinformation borttagen med Sysprep. 
- **Specialiserad virtuell hårddisk** – underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

> [!IMPORTANT]
> Innan du laddar upp en virtuell hårddisk till Azure bör du följa [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Du har befintliga AWS EC2-instanser som du vill migrera till virtuella Azure-datorer med hanterade diskar                              | [Flytta en virtuell dator från Amazon Web Services (AWS) till Azure](aws-to-azure.md)                           |
| Du har en virtuell dator från en annan virtualiseringsplattform som du vill använda som en avbildning för att skapa flera virtuella Azure-datorer. | [Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa en ny virtuell dator i Azure](upload-generalized-managed.md) |
| Du har en unikt anpassad virtuell dator som du vill återskapa i Azure.                                                      | [Ladda upp en specialiserad virtuell hårddisk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Översikt över hanterade diskar

Azure Managed Disks förenklar VM-hantering genom att ta bort behovet av att hantera lagringskonton. Hanterade diskar drar också nytta av bättre tillförlitlighet för virtuella datorer i en tillgänglighetsuppsättning. Det säkerställer att diskarna för olika virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika en enda felpunkt. Den placerar automatiskt diskar med olika virtuella datorer i en tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) som begränsar effekten av fel på en enda lagringsskalaenhet som orsakas på grund av maskinvaru- och programvarufel.
Baserat på dina behov kan du välja mellan fyra typer av lagringsalternativ. Mer information om tillgängliga disktyper finns i vår artikel [Välj en disktyp](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migreringen till hanterade diskar

Det här avsnittet hjälper dig att fatta det bästa beslutet om virtuell dator och disktyper.

Om du planerar att migrera från ohanterade diskar till hanterade diskar bör du vara medveten om att användare med rollen [Deltagare i virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) inte kan ändra den virtuella datorns storlek (eftersom de kan före konverteringen). Detta beror på att virtuella datorer med hanterade diskar kräver att användaren har behörigheten Microsoft.Compute/disk/write på os-diskarna.

### <a name="location"></a>Location

Välj en plats där Azure Managed Disks är tillgängliga. Om du migrerar till Premium-hanterade diskar ska du också se till att Premium-lagring är tillgängligt i den region där du planerar att migrera till. Se [Azure Services efter region](https://azure.microsoft.com/regions/#services) för uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium-hanterade diskar måste du uppdatera storleken på den virtuella datorn till premiumlagringskompatibla storlek som är tillgänglig i den region där den virtuella datorn finns. Granska den virtuella datorns storlekar som är Premium Storage-kompatibla. Storleksspecifikationerna för Azure VM visas i [Storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaperna för virtuella datorer som fungerar med Premium Storage och välj den lämpligaste vm-storleken som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att driva disktrafiken.

### <a name="disk-sizes"></a>Diskstorlekar

**Premium hanterade diskar**

Det finns sju typer av premiumhanterade diskar som kan användas med din virtuella dator och var och en har specifika IOPs och dataflödesgränser. Ta hänsyn till dessa gränser när du väljer Premium-disktypen för din virtuella dator baserat på behoven i ditt program när det gäller kapacitet, prestanda, skalbarhet och toppbelastningar.

| Typ av premiumdiskar  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1 024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100  |2 300              | 5000              | 7 500              | 7 500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund |150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

**Standardhanterade diskar**

Det finns sju typer av standardhanterade diskar som kan användas med den virtuella datorn. Var och en av dem har olika kapacitet men har samma IOPS och dataflödesgränser. Välj typ av standardhanterade diskar baserat på programmets kapacitetsbehov.

| Disk av standardtyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50 (S50)              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1 024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 

### <a name="disk-caching-policy"></a>Princip för cachelagring av disk 

**Premium hanterade diskar**

Som standard är diskcachelagringsprincipen *skrivskyddad* för alla Premium-datadiskar och *Läs-skriv* för premium-operativsystemdisken som är ansluten till den virtuella datorn. Den här konfigurationsinställningen rekommenderas för att uppnå optimal prestanda för programmets IOs. För skrivtunna eller skriv-bara datadiskar (till exempel SQL Server-loggfiler) inaktiverar du diskcachelagring så att du kan uppnå bättre programprestanda.

### <a name="pricing"></a>Prissättning

Granska [prissättningen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/). Prissättningen av Premium-hanterade diskar är samma som Premium Ohanterade diskar. Men prissättningen för standardhanterade diskar skiljer sig från Standard Ohanterade diskar.


## <a name="next-steps"></a>Efterföljande moment

- Innan du laddar upp en virtuell hårddisk till Azure bör du följa [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

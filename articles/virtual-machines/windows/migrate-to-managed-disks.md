---
title: Migrera virtuella Azure-datorer till Managed Disks | Microsoft Docs
description: Migrera virtuella Azure-datorer skapas med ohanterade diskar i storage-konton för att använda Managed Disks.
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
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418411"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till Managed Disks i Azure

Azure Managed Disks förenklar din lagringshantering genom att ta bort behovet av att hantera storage-konton separat.  Du kan också migrera dina befintliga virtuella Azure-datorer till Managed Disks för att dra nytta av bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det innebär att diskar på olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritisk fel. Den placerar automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) vilket begränsar effekten av enskild lagringsskalningsenhetsfel bero på att maskinvara och programvarufel.
Utifrån dina behov kan välja du mellan fyra typer av lagringsalternativ. Läs om tillgängliga disktyper i vår artikel [Välj en disktyp av](disks-types.md)

## <a name="migration-scenarios"></a>Migreringsscenarier

Du kan migrera till Managed Disks i följande scenarier:

|Scenario  |Artikel  |
|---------|---------|
|Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till hanterade diskar     |[Konvertera virtuella datorer om du vill använda hanterade diskar](convert-unmanaged-to-managed-disks.md)         |
|Konvertera en enskild virtuell dator från klassisk till Resource Manager på hanterade diskar     |[Skapa en virtuell dator från en klassisk virtuell Hårddisk](create-vm-specialized-portal.md)         |
|Konvertera alla virtuella datorer i ett virtuellt nätverk från klassisk till Resource Manager på hanterade diskar     |[Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och sedan [konvertera en virtuell dator från ohanterade diskar till managed disks](convert-unmanaged-to-managed-disks.md)         |
|Uppgradera virtuella datorer med ohanterade standarddiskar för virtuella datorer med hanterade premiumdiskar     | Först [konvertera en Windows-dator från ohanterade diskar till managed disks](convert-unmanaged-to-managed-disks.md). Sedan [uppdatera lagringstypen för en hanterad disk](convert-disk-storage.md).         |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Managed Disks](managed-disks-overview.md)
- Granska den [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

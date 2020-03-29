---
title: Migrera virtuella Azure-datorer till hanterade diskar
description: Migrera virtuella Azure-datorer som skapats med ohanterade diskar i lagringskonton för att använda hanterade diskar.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921359"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till hanterade diskar i Azure

Azure Managed Disks förenklar lagringshanteringen genom att ta bort behovet av att hantera lagringskonton separat.  Du kan också migrera dina befintliga virtuella Azure-datorer till hanterade diskar för att dra nytta av bättre tillförlitlighet för virtuella datorer i en tillgänglighetsuppsättning. Det säkerställer att diskarna för olika virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika enstaka felpunkt. Den placerar automatiskt diskar med olika virtuella datorer i en tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) som begränsar effekten av fel på en enda lagringsskalaenhet som orsakas på grund av maskinvaru- och programvarufel.
Baserat på dina behov kan du välja mellan fyra typer av lagringsalternativ. Mer information om tillgängliga disktyper finns i vår artikel [Välj en disktyp](disks-types.md)

## <a name="migration-scenarios"></a> Migreringsscenarier

Du kan migrera till hanterade diskar i följande scenarier:

|Scenario  |Artikel  |
|---------|---------|
|Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till hanterade diskar     |[Konvertera virtuella datorer för att använda hanterade diskar](convert-unmanaged-to-managed-disks.md)         |
|Konvertera en virtuell dator från klassisk till Resurshanteraren på hanterade diskar     |[Skapa en virtuell dator från en klassisk virtuell hårddisk](create-vm-specialized-portal.md)         |
|Konvertera alla virtuella datorer i ett virtuellt nätverk från klassiskt till Resurshanteraren på hanterade diskar     |[Migrera IaaS-resurser från klassiskt till Resurshanteraren](migration-classic-resource-manager-ps.md) och konvertera sedan [en virtuell dator från ohanterade diskar till hanterade diskar](convert-unmanaged-to-managed-disks.md)         |
|Uppgradera virtuella datorer med vanliga ohanterade diskar till virtuella datorer med hanterade premiumdiskar     | Konvertera [först en virtuell Windows-dator från ohanterade diskar till hanterade diskar](convert-unmanaged-to-managed-disks.md). Uppdatera sedan [lagringstypen för en hanterad disk](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [hanterade diskar](managed-disks-overview.md)
- Granska [prissättningen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

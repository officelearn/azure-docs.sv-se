---
title: Migrera virtuella Azure-datorer till Managed Disks
description: Migrera virtuella Azure-datorer som skapats med ohanterade diskar i lagrings konton för att använda Managed Disks.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d64ebb053ef01e375edd52ad0bf2c1f424f1b837
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660833"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till Managed Disks i Azure

Azure Managed Disks fören klar lagrings hanteringen genom att ta bort behovet av separat hantering av lagrings konton.  Du kan också migrera dina befintliga virtuella Azure-datorer till Managed Disks för att dra nytta av bättre tillförlitlighet för virtuella datorer i en tillgänglighets uppsättning. Det garanterar att diskarna för olika virtuella datorer i en tillgänglighets uppsättning är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Den placerar automatiskt diskar med olika virtuella datorer i en tillgänglighets uppsättning i olika lagrings skalnings enheter (stämplar) som begränsar konsekvenserna av enskilda lagrings enhets enhets problem som orsakas av maskin-och program varu problem.
Utifrån dina behov kan du välja mellan fyra olika typer av lagrings alternativ. Information om tillgängliga disk typer finns i vår artikel [Välj en disktyp](disks-types.md)

## <a name="migration-scenarios"></a>Migreringsscenarier

Du kan migrera till Managed Disks i följande scenarier:

|Scenario  |Artikel  |
|---------|---------|
|Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till hanterade diskar     |[Konvertera virtuella datorer för att använda hanterade diskar](convert-unmanaged-to-managed-disks.md)         |
|Konvertera en enskild virtuell dator från klassisk till Resource Manager på Managed disks     |[Skapa en virtuell dator från en klassisk virtuell hård disk](create-vm-specialized-portal.md)         |
|Konvertera alla virtuella datorer i ett vNet från klassiskt läge till Resource Manager på Managed disks     |[Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och [konvertera sedan en virtuell dator från ohanterade diskar till Managed disks](convert-unmanaged-to-managed-disks.md)         |
|Uppgradera virtuella datorer med ohanterade standard diskar till virtuella datorer med hanterade Premium-diskar     | Konvertera först [en virtuell Windows-dator från ohanterade diskar till hanterade diskar](convert-unmanaged-to-managed-disks.md). [Uppdatera sedan lagrings typen för en hanterad disk](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Managed disks](managed-disks-overview.md)
- Läs [om prissättningen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

---
title: Migrera virtuella Azure-datorer till Managed Disks | Microsoft Docs
description: Migrera virtuella Azure-datorer skapas med ohanterade diskar i storage-konton för att använda Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803591"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till Managed Disks i Azure

Azure Managed Disks förenklar din lagringshantering genom att ta bort behovet av att hantera storage-konton separat.  Du kan också migrera dina befintliga virtuella Azure-datorer till Managed Disks för att dra nytta av bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det innebär att diskar på olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritisk fel. Den placerar automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) vilket begränsar effekten av enskild lagringsskalningsenhetsfel bero på att maskinvara och programvarufel.
Utifrån dina behov kan välja du mellan fyra typer av lagringsalternativ. Läs om tillgängliga disktyper i vår artikel [Välj en disktyp av](disks-types.md)

## <a name="migrate-scenarios"></a>Migrera scenarier

Du kan migrera till Managed Disks i följande scenarier:

| **Migrera...**                                            | **Länk till dokumentation**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till managed disks   | [Konvertera virtuella datorer om du vill använda hanterade diskar](convert-unmanaged-to-managed-disks.md) |
| En enskild virtuell dator från klassisk till Resource Manager på hanterade diskar     | [Skapa en virtuell dator från en klassisk virtuell Hårddisk](create-vm-specialized-portal.md)  | 
| Alla virtuella datorer i ett virtuellt nätverk från klassisk till Resource Manager på hanterade diskar     | [Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och sedan [konvertera en virtuell dator från ohanterade diskar till managed disks](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Managed Disks](managed-disks-overview.md)
- Granska den [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

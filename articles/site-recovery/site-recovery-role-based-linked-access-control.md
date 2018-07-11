---
title: Använda rollbaserad åtkomstkontroll för att hantera Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du tillämpa och använda rollbaserad åtkomstkontroll (RBAC) för att hantera dina Azure Site Recovery-distributioner
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/06/2018
author: mayanknayar
ms.topic: conceptual
ms.author: manayar
ms.openlocfilehash: dfd880b6ff3a7e199ea259acc5e5ec59f89c897d
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919734"
---
# <a name="use-role-based-access-control-to-manage-site-recovery-access"></a>Använda rollbaserad åtkomstkontroll för att hantera Site Recovery-åtkomst

Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du särskilja ansvarsområden i ditt team och bevilja endast specifika åtkomstbehörigheter till användare som behövs för att utföra specifika arbetsuppgifter.

Azure Site Recovery tillhandahåller 3 inbyggda roller som styr Site Recovery-hanteringsåtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Site Recovery-bidragsgivare](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer som omständigheterna.
* [Site Recovery-operatör](../role-based-access-control/built-in-roles.md#site-recovery-operator) – den här rollen har behörighet att köra och hantera redundans och återställning efter fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxling för virtuella datorer eller program efter instruktioner från programägare och IT-administratörer i en situation med faktiska eller simulerade haveriberedskap som en DR Visa detaljer. Publicera lösning av katastrofen DR-operatorn kan skydda igen och återställa virtuella datorer.
* [Site Recovery-läsare](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden om det behövs.

Om du vill definiera egna roller för ännu mer kontroll, se hur du [skapa anpassade roller](../role-based-access-control/custom-roles.md) i Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Behörigheter som krävs för att aktivera replikering för nya virtuella datorer
När en ny virtuell dator replikeras till Azure med hjälp av Azure Site Recovery, verifieras åtkomstnivåer för den associerade användaren så att användaren har behörighet att använda Azure-resurserna till Site Recovery.

Om du vill aktivera replikering för en ny virtuell dator, måste användarna ha:
* Behörighet att skapa en virtuell dator i den valda resursgruppen
* Behörighet att skapa en virtuell dator i det valda virtuella nätverket
* Behörighet att skriva till det valda lagringskontot

En användare behöver följande behörigheter till fullständig replikering av en ny virtuell dator.

> [!IMPORTANT]
>Se till att relevant behörighet läggs per distributionsmodell (Resource Manager / klassiska) används för resursdistribution av.

| **Resurstyp** | **Distributionsmodell** | **Behörighet** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Klassisk | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Nätverk | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassisk | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klassisk | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resursgrupp | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Överväg att använda ”virtuell Datordeltagare” och ”klassisk virtuell Datordeltagare” [inbyggda roller](../role-based-access-control/built-in-roles.md) för Resource Manager och klassisk distribution modeller respektive.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md): Kom igång med RBAC i Azure-portalen.
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Role-Based Access Control – felsökning](../role-based-access-control/troubleshooting.md): få förslag för att åtgärda vanliga problem.

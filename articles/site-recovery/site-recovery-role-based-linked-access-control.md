---
title: "Använda rollbaserad åtkomstkontroll för att hantera Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du tillämpa och använda rollbaserad åtkomstkontroll (RBAC) för att hantera distributionen av Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: manayar
ms.openlocfilehash: 9dd74014bf05234a83c7678b67b42b96cd8b8d64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Använda rollbaserad åtkomstkontroll för att hantera distributioner av Azure Site Recovery

Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du särskilja ansvarsområden i din grupp och ge endast behörighet till användare som behövs för att utföra specifika jobb.

Azure Site Recovery tillhandahåller 3 inbyggda roller för att styra Site Recovery-hanteringsåtgärder. Läs mer på [inbyggda Azure RBAC-roller](../active-directory/role-based-access-built-in-roles.md)

* [Site Recovery-deltagare](../active-directory/role-based-access-built-in-roles.md#site-recovery-contributor) -rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i Recovery Services-valvet. Skapa en användare med den här rollen, men kan inte eller ta bort ett Recovery Services-valv eller tilldela behörigheter till andra användare. Den här rollen passar bäst för disaster recovery-administratörer som kan aktivera och hantera katastrofåterställning för program eller hela organisationer som eventuellt.
* [Site Recovery-operatorn](../active-directory/role-based-access-built-in-roles.md#site-recovery-operator) -rollen har behörighet att köra och åtgärder för redundans och återställning efter fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela behörigheter till andra användare. Den här rollen är bäst för en disaster recovery-operatör som kan redundans virtuella datorer eller program när du uppmanas av applikationsägare och IT-administratörer i en situation faktiska eller simulerade katastrofåterställning, till exempel en DR detaljgranska. Bokför upplösning över DR-operatorn kan skydda igen och återställning av virtuella datorer.
* [Site Recovery Reader](../active-directory/role-based-access-built-in-roles.md#site-recovery-reader) -rollen har behörighet att visa alla Site Recovery-hanteringsåtgärder. Den här rollen passar bäst för en övervakning IT-chef som kan övervaka det aktuella tillståndet för skydd och höja supportärenden om det behövs.

Om du behöver för att definiera egna roller för ännu mer kontroll, se hur du [skapa anpassade roller](../active-directory/role-based-access-control-custom-roles.md) i Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Behörigheter som krävs för att aktivera replikering för den nya virtuella datorer
När en ny virtuell dator replikeras till Azure med Azure Site Recovery verifieras åtkomstnivåer för den associerade användaren så att användaren har behörighet att använda Azure-resurser till Site Recovery.

Om du vill aktivera replikering för en ny virtuell dator, måste användaren ha:
* Behörighet att skapa en virtuell dator i den valda resursgruppen
* Behörighet att skapa en virtuell dator i det valda virtuella nätverket
* Behörighet att skriva till det valda lagringskontot

En användare behöver följande behörigheter till fullständig replikering av en ny virtuell dator.

> [!IMPORTANT]
>Kontrollera att relevanta behörigheter läggs per distributionsmodell (Resource Manager / klassiska) används för distribution av resursen.

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
| Lagring | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klassisk | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resursgrupp | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Överväg att använda Virtual Machine-deltagare och 'Klassiska Virtual Machine-deltagare' [inbyggda roller](../active-directory/role-based-access-built-in-roles.md) för Resource Manager och klassisk distribution modeller respektive.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md): komma igång med RBAC på Azure-portalen.
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [Rollbaserad åtkomstkontroll felsökning](../active-directory/role-based-access-control-troubleshooting.md): få förslag om hur du löser vanliga problem.

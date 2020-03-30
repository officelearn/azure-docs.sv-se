---
title: Hantera Azure-rollbaserad åtkomstkontroll i Azure Site Recovery
description: I den här artikeln beskrivs hur du använder rollbaserad åtkomstkontroll (RBAC) för att hantera Azure Site Recovery-åtkomst.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257581"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Hantera åtkomst till platsåterställning med rollbaserad åtkomstkontroll (RBAC)

Azure role-based access Control (RBAC) möjliggör finkornig åtkomsthantering för Azure. Med hjälp av RBAC kan du segregera ansvarsområden inom ditt team och endast ge specifika åtkomstbehörigheter till användare efter behov för att utföra specifika jobb.

Azure Site Recovery innehåller 3 inbyggda roller för att styra hantering av webbplatsåterställning. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Site Recovery-bidragsgivare](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen passar bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer, alltefter er.
* [Site Recovery-operatör](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomsträttigheter till andra användare. Den här rollen passar bäst för en haveriberedskapsoperatör som kan redundans virtuella datorer eller program när de instrueras av programägare och IT-administratörer i en faktisk eller simulerad katastrofsituation, till exempel en DR-borr. Efter lösning av katastrofen kan DR-operatorn skydda om och återställa de virtuella datorerna.
* [Site Recovery-läsare](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen passar bäst för en IT-övervakningsansvarig som kan övervaka det aktuella skyddet och höja supportbiljetterna om det behövs.

Om du vill definiera dina egna roller för ännu mer kontroll kan du se hur du [skapar anpassade roller](../role-based-access-control/custom-roles.md) i Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Behörigheter som krävs för att aktivera replikering för nya virtuella datorer
När en ny virtuell dator replikeras till Azure med Azure Site Recovery valideras den associerade användarens åtkomstnivåer för att säkerställa att användaren har de behörigheter som krävs för att använda Azure-resurser som tillhandahålls till Site Recovery.

Om du vill aktivera replikering för en ny virtuell dator måste en användare ha:
* Behörighet att skapa en virtuell dator i den valda resursgruppen
* Behörighet att skapa en virtuell dator i det valda virtuella nätverket
* Behörighet att skriva till det valda lagringskontot

En användare behöver följande behörigheter för att slutföra replikeringen av en ny virtuell dator.

> [!IMPORTANT]
>Kontrollera att relevanta behörigheter läggs till per distributionsmodellen (Resource Manager/ Classic) som används för resursdistribution.

> [!NOTE]
> Om du aktiverar replikering för en virtuell Azure-dator och vill tillåta att site recovery hanterar uppdateringar, kanske du också vill skapa ett nytt Automation-konto när du aktiverar replikering också skapa ett nytt Automation-konto, i så fall behöver du behörighet att skapa ett automationskonto i samma abonnemang som valvet också.

| **Resurstyp** | **Distributionsmodell** | **Behörighet** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft. |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Klassisk | Microsoft.ClassicCompute/domainNames/read Microsoft.ClassicCompute/domainNames/read Microsoft.ClassicCompute/domainNames/read Microsoft. |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read Microsoft.ClassicCompute/virtualMachines/read Microsoft.ClassicCompute/virtualMachines/read Microsoft. |
|  |  | Microsoft.ClassicCompute/virtualMachines/write Microsoft.ClassicCompute/virtualMachines/write Microsoft.ClassicCompute/virtualMachines/write Microsoft. |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete Microsoft.ClassicCompute/virtualMachines/delete Microsoft.ClassicCompute/virtualMachines/delete Microsoft. |
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
|  |  | Microsoft.Storage/storageKonto/listnycklar/åtgärd |
|  | Klassisk | Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft. |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resursgrupp | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resurser/prenumerationer/resourceGroups/read |

Överväg att använda inbyggda roller för Resource Manager respektive Classic Virtual Machine [Contributor.](../role-based-access-control/built-in-roles.md)

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md): Kom igång med RBAC i Azure-portalen.
* Läs om hur du hanterar åtkomst med:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Felsökning av rollbaserad åtkomstkontroll](../role-based-access-control/troubleshooting.md): Få förslag på hur du åtgärdar vanliga problem.

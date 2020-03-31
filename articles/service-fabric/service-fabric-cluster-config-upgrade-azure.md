---
title: Uppgradera konfigurationen av ett Azure Service Fabric-kluster
description: Lär dig hur du uppgraderar konfigurationen som kör ett Service Fabric-kluster i Azure med hjälp av en Resource Manager-mall.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463281"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uppgradera konfigurationen av ett kluster i Azure 

I den här artikeln beskrivs hur du anpassar de olika inställningarna för infrastruktur för ditt Service Fabric-kluster. För kluster som finns i Azure kan du anpassa inställningarna via [Azure-portalen](https://portal.azure.com) eller med hjälp av en Azure Resource Manager-mall.

> [!NOTE]
> Alla inställningar är inte tillgängliga i portalen och det är en [bra idé att anpassa den med hjälp av en Azure Resource Manager-mall.](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) Portalen är endast till för scenariot Service Fabric Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassa klusterinställningar med hjälp av Resource Manager-mallar
Azure-kluster kan konfigureras via JSON Resource Manager-mallen. Mer information om de olika inställningarna finns i [Konfigurationsinställningar för kluster](service-fabric-cluster-fabric-settings.md). Som ett exempel visar stegen nedan hur du lägger till en ny inställning *MaxDiskQuotaInMB* i *avsnittet Diagnostik* med Azure Resource Explorer.

1. Gå till https://resources.azure.com
2. Navigera till din prenumeration genom att utöka **prenumerationer** -> **\<Din prenumeration>**  ->  **resourceGroups** -> **\<Din resursgrupp **  ->  **>-leverantörer** -> **Microsoft.ServiceFabric-kluster** -> **clusters** -> **\<Ditt klusternamn>**
3. Välj **Läs/skriv** i det övre högra hörnet.
4. Välj **Redigera** och `fabricSettings` uppdatera JSON-elementet och lägg till ett nytt element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Du kan också anpassa klusterinställningarna på något av följande sätt med Azure Resource Manager:

- Använd [Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) för att exportera och uppdatera resursmanger-mallen.
- Använd [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) för att exportera och uppdatera resource manager-mallen.
- Använd [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) för att exportera och uppdatera Resource Manager-mallen.
- Använd azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) och [Remove-AzServiceFabricSetting-kommandon](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) för att ändra inställningen direkt.
- Använd kommandona för Azure CLI [az sf-klusterinställning](https://docs.microsoft.com/cli/azure/sf/cluster/setting) för att ändra inställningen direkt.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [klusterinställningarna för Service Fabric](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skalar klustret in och ut](service-fabric-cluster-scale-up-down.md).

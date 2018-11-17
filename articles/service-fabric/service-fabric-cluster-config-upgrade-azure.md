---
title: Uppgradera konfigurationen av ett Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du uppgraderar den konfiguration som körs av Service Fabric-kluster i Azure med en Resource Manager-mall.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 9323b393edb808f3d2d069f868deb0b67cd0c871
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855191"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uppgradera konfigurationen av ett kluster i Azure 

Den här artikeln beskriver hur du anpassar olika fabric inställningarna för Service Fabric-klustret. För kluster i Azure kan du anpassa inställningar via den [Azure-portalen](https://portal.azure.com) eller genom att använda en Azure Resource Manager-mall.

> [!NOTE]
> Alla inställningar är inte tillgänglig i portalen. Om en inställning som anges nedan inte är tillgänglig via portalen anpassar du den med en Azure Resource Manager-mall.> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassa inställningar för klustret med hjälp av Resource Manager-mallar
Azure-kluster kan konfigureras via JSON Resource Manager-mallen. Läs mer om de olika inställningarna i [konfigurationsinställningar för kluster](service-fabric-cluster-fabric-settings.md). Till exempel stegen nedan visar hur du lägger till en ny inställning *MaxDiskQuotaInMB* till den *diagnostik* avsnittet med hjälp av Azure Resource Explorer.

1. Gå till https://resources.azure.com
2. Gå till din prenumeration genom att expandera **prenumerationer** -> **\<din prenumeration >** -> **resourceGroups**  ->   **\<Din resursgrupp >** -> **providers** -> **Microsoft.ServiceFabric**  ->  **kluster** -> **\<Your klustrets namn >**
3. I övre högra hörnet väljer **Läs/Skriv.**
4. Välj **redigera** och uppdatera den `fabricSettings` JSON-element och Lägg till ett nytt element:

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

Du kan också anpassa inställningar för klustret i något av följande sätt med Azure Resource Manager:

- Använd den [Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) att exportera och uppdatera Resource Manager-mall.
- Använd [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) att exportera och uppdatera Resource Manager-mallen.
- Använd den [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) att exportera och uppdatera Resource Manager-mallen.
- Använda Azure RM PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) och [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) kommandon för att ändra inställningen direkt.
- Använda Azure CLI [az sf cluster inställningen](https://docs.microsoft.com/cli/azure/sf/cluster/setting) kommandon för att ändra inställningen direkt.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [inställningar för Service Fabric-klustret](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md).

---
title: Skala ut ett Service Fabric hanterat kluster (för hands version)
description: I den här självstudien får du lära dig hur du skalar upp en nodtyp för ett Service Fabric hanterat kluster.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 01b299744d462496296884211eff08b7a9c64687
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316146"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Självstudie: skala ut ett Service Fabric hanterat kluster (förhands granskning)

I den här själv studie serien diskuterar vi:

> [!div class="checklist"]
> * [Distribuera ett Service Fabric hanterat kluster.](tutorial-managed-cluster-deploy.md)
> * Så här skalar du ut ett Service Fabric hanterat kluster
> * [Lägga till och ta bort nodtyper i ett Service Fabric hanterat kluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Så här distribuerar du ett program till ett Service Fabric hanterat kluster](tutorial-managed-cluster-deploy-app.md)

Den här delen av serien handlar om hur du:

> [!div class="checklist"]
> * Skala en Service Fabric hanterad klusternod

## <a name="prerequisites"></a>Förutsättningar

* Ett Service Fabric hanterat kluster (se [*distribuera ett hanterat kluster*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) eller senare (se [*Installera Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Skala ett Service Fabric hanterat kluster
Ändra antalet instanser för att öka eller minska antalet noder i nodtypen som du vill skala. Du kan hitta namnen på nodnamn i Azure Resource Manager-mallen (ARM-mallen) från kluster distributionen eller i Service Fabric Explorer.  

> [!NOTE]
> Om nodtypen är primär kan du inte gå under 3 noder för ett Basic SKU-kluster och 5 noder för ett standard-SKU-kluster.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Klustret börjar uppgradera automatiskt och efter några minuter visas ytterligare noder.

## <a name="next-steps"></a>Nästa steg

I det här steget skalade vi en nodtyp i ett Service Fabric hanterat kluster. Mer information om hur du lägger till och tar bort nodtyper finns i:

> [!div class="nextstepaction"]
> [Lägga till och ta bort nodtyper till ett Service Fabric hanterat kluster](tutorial-managed-cluster-add-remove-node-type.md)
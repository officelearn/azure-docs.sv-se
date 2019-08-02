---
title: Lägga till en nodtyp i ett Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du skalar ut ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 1414e656a358af1e258c823cc7ec747fefa986ba
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598685"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skala ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer
Den här artikeln beskriver hur du skalar ett Azure Service Fabric-kluster genom att lägga till en ny nodtyp i ett befintligt kluster. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att lägga till en ny nodtyp (skalnings uppsättning för virtuell dator) i ett befintligt kluster.  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Lägg till ytterligare en skalnings uppsättning i ett befintligt kluster
Att lägga till en ny nodtyp (som backas upp av en skalnings uppsättning för virtuella datorer) till ett befintligt kluster liknar [uppgradering av den primära nodtypen](service-fabric-scale-up-node-type.md), förutom att du inte använder samma NodeTypeRef. Det går inte att inaktivera några aktivt använda skalnings uppsättningar för virtuella datorer och du förlorar inte kluster tillgänglighet om du inte uppdaterar den primära nodtypen. 

NodeTypeRef-egenskapen har deklarerats i skalnings uppsättningen för den virtuella datorn Service Fabric tilläggs egenskaper:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Dessutom måste du lägga till den nya nodtypen till Service Fabric kluster resurs:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skalar upp den primära nodtypen](service-fabric-scale-up-node-type.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


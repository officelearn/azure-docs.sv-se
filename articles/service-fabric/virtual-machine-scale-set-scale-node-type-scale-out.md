---
title: Lägg till en nodtyp i ett Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig mer om att skala ut ett Service Fabric-kluster genom att lägga till en Virtual Machine Scale Sets.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: 01d4af8349d3f5a0f58c4c3fa56b489d739c7b42
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301714"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skala ut ett Service Fabric-kluster genom att lägga till en VM-skalningsuppsättning
Den här artikeln beskriver hur du skalar en Azure Service Fabric-kluster genom att lägga till en ny nodtyp i ett befintligt kluster. Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Virtual machine scale sets är en Azure-beräkningsresurs som används för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [ställa in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster, kan du skala ett kluster horisontellt genom att lägga till en ny nodtyp (virtual machine scale Sets) i ett befintligt kluster.  Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.  När klustret skalas skalas programmen automatiskt samt.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Lägg till en ytterligare skalningsuppsättning i ett befintligt kluster
Att lägga till en ny nodtyp (som backas upp av en VM-skalningsuppsättning) i ett befintligt kluster liknar [uppgradering av den primära nodtypen](service-fabric-scale-up-node-type.md), men du inte använder samma NodeTypeRef; naturligtvis kommer inte att inaktivera alla används aktivt VM-skalningsuppsättningar, och du skriver inte förlora klustret om du inte uppdaterar den primära nodtypen. 

NodeTypeRef egenskap har deklarerats i den virtuella datorn tilläggsegenskaper för Service Fabric-skalningsuppsättning:
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

Dessutom behöver du lägga till den här nya nodtyp till Service Fabric-kluster-resursen:

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
* Lär dig hur du [skala upp den primära nodtypen](service-fabric-scale-up-node-type.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


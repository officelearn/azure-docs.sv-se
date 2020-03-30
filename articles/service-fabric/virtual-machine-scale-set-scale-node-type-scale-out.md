---
title: Lägga till en nodtyp i ett Azure Service Fabric-kluster
description: Lär dig hur du skalar ut ett Service Fabric-kluster genom att lägga till en skaluppsättning för virtuella datorer.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463971"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skala ut ett Service Fabric-kluster genom att lägga till en skaluppsättning för virtuella datorer
I den här artikeln beskrivs hur du skalar ett Azure Service Fabric-kluster genom att lägga till en ny nodtyp i ett befintligt kluster. Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas en nod. Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster [ställs in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att lägga till en ny nodtyp (skaluppsättning för virtuell dator) i ett befintligt kluster.  Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.  När klustret skalas skalas även dina program automatiskt.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Lägga till ytterligare en skalningsuppsättning i ett befintligt kluster
Att lägga till en ny nodtyp (som backas upp av en skaluppsättning för virtuella datorer) i ett befintligt kluster liknar [uppgradering av den primära nodtypen,](service-fabric-scale-up-node-type.md)förutom att du inte använder samma NodeTypeRef. uppenbarligen inte kommer att inaktivera någon aktivt använd virtuell dator skalning uppsättningar, och du kommer inte att förlora kluster tillgänglighet om du inte uppdaterar den primära nodtypen. 

NodeTypeRef-egenskapen deklareras i egenskaperna för service fabric-tillägg för den virtuella datorns skaluppsättning:
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

Dessutom måste du lägga till den här nya nodtypen i klusterresursen Service Fabric:

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
* Läs mer om [programskalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av den flytande Azure-beräkningSDK.Scale an Azure cluster programmematically using the fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


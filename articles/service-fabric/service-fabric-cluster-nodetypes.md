---
title: Azure Service Fabric-nodtyper och VM-skalningsuppsättningar | Microsoft Docs
description: Lär dig hur Azure Service Fabric-noden typer som är relaterade till VM-skalningsuppsättning anger och hur du ansluter via en fjärranslutning till en skala ange instans eller en klusternod.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660622"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric-nodtyper och VM-skalningsuppsättningar
[VM-skalningsuppsättningar](/azure/virtual-machine-scale-sets) är en Azure-beräkningsresurs. Du kan använda skalningsuppsättningar för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skala.  Service Fabric-körningen som installerats på varje virtuell dator i skalningsuppsättningen har angetts. Du kan oberoende skala varje nodtyp upp eller ned, ändra den operativsystem-SKU som körs på varje nod i klustret, ha olika portar öppna och använda olika kapacitet.

Följande bild visar ett kluster med två nodtyper med namnet FrontEnd och BackEnd. Varje nodtyp har fem noder.

![Ett kluster med två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa VM-skalningsuppsättningsinstanser till noder
I bilden ovan visas skalningsuppsättningsinstanser startar instansen 0 och sedan öka med 1. Numreringen återspeglas i nod-namn. Noden BackEnd_0 är till exempel instansen 0 av skalningsuppsättningen serverdel. Den här specifika skalningsuppsättningen har fem instanser, med namnet BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalningsuppsättning skapas en ny instans. Ny scale set-instansnamnet är vanligtvis skalningsuppsättningen namn samt nästa instansnummer. I vårt exempel är det BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mappa scale set-belastningsutjämnare till nodtyper och skalningsuppsättningar
Om du har distribuerat ditt kluster i Azure-portalen eller använt exempelmallen för Azure Resource Manager, visas alla resurser under en resursgrupp. Du kan se belastningsutjämnare för varje scale set eller noden. Namnet på belastningsutjämnaren använder följande format: **LB -&lt;nodtypnamn&gt;**. Ett exempel är LB-sfcluster4doc-0, enligt följande bild:

![Resurser][Resources]


## <a name="next-steps"></a>Nästa steg
* Se den [översikt över ”distribuera var som helst”-funktionen och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster security](service-fabric-cluster-security.md).
* [Fjärranslutning](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) till en specifik scale set-instans
* [Uppdatera RDP-portintervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distributionen
* [Ändra administratörens användarnamn och lösenord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella datorer klustret

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png

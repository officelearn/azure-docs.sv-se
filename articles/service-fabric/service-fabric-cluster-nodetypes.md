---
title: Azure Service Fabric-nodtyper och den virtuella datorn skala anger | Microsoft Docs
description: Lär dig hur Azure Service Fabric-noden typer som relaterar till virtuella datorn anger och hur du kan fjärransluta till en skala ange instans eller den klusternod.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: ce3b3e6f3c7ee0c7198c27ed0ca0e3610d26f143
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Skala anger Azure Service Fabric-nodtyper och den virtuella datorn
[Skaluppsättningar för den virtuella datorn](/azure/virtual-machine-scale-sets) är en Azure compute resurs. Du kan använda skaluppsättningar för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skala.  Ange den Service Fabric som är installerad på varje virtuell dator i skalan. Du kan oberoende skala varje nodtyp upp eller ned, ändra OS-SKU som körs på varje nod i klustret, har olika uppsättningar av portar är öppna och använda olika kapacitetsdata.

Följande bild visar ett kluster med två nodtyper heter FrontEnd och BackEnd. Varje nodtyp har fem noder.

![Ett kluster med två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa virtuella scale set instanser till noder
I föregående bild visas skala ange instanser start på instansen 0 och sedan öka med 1. Numreringen avspeglas i nod-namn. Noden BackEnd_0 är till exempel instans 0 för BackEnd-skaluppsättning. Den här viss skaluppsättning har fem instanser som heter BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalningsuppsättning skapas en ny instans. Nya scale set-instansnamnet är vanligtvis skaluppsättning namn plus nästa instansnummer. I vårt exempel är BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mappa scale set belastningsutjämnare till nodtyper och skala uppsättningar
Om du distribuerade klustret i Azure-portalen eller exempelmall för Azure Resource Manager, visas alla resurser i en resursgrupp. Du kan se belastningsutjämnare för varje scale set eller nod. Belastningsutjämnarens namn används följande format: **LB -&lt;namnet nodtypen&gt;**. LB-sfcluster4doc-0 är ett exempel som visas i följande bild:

![Resurser][Resources]


## <a name="next-steps"></a>Nästa steg
* Finns det [översikt över funktionen ”distribuera överallt” och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Fjärranslutning](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) till en specifik skala set-instans
* [Uppdatera RDP-porten intervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distribution
* [Ändra det användarnamn och lösenord administratör](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för kluster för virtuella datorer

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png

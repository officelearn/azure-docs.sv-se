---
title: Azure Service Fabric fristående kluster skalning | Microsoft Docs
description: Lär dig mer om skalning Service Fabric fristående kluster i eller ut och upp eller ned.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: eedf80ec82a748f5da8e51aed8b4d403dffe4169
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599864"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skala Service Fabric fristående kluster
Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurserna för noderna).  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

Varför ska du skala klustret? Program krav ändras med tiden.  Du kan behöva öka kluster resurserna för att möta ökad program belastning eller nätverks trafik eller minska kluster resurserna när efter frågan går vidare.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller vågrät skalning
Ändrar antalet noder i klustret.  När de nya noderna ansluter till klustret, flyttar [kluster resurs hanteraren](service-fabric-cluster-resource-manager-introduction.md) tjänster till dem som minskar belastningen på de befintliga noderna.  Du kan också minska antalet noder om klustrets resurser inte används effektivt.  När noder lämnar klustret flyttas tjänsterna från dessa noder och belastningen ökar på de återstående noderna.  Att minska antalet noder i ett kluster som körs i Azure kan spara pengar, eftersom du betalar för antalet virtuella datorer som du använder och inte arbets belastningen på de virtuella datorerna.  

- Fördelar Oändlig skala i teorin.  Om ditt program har utformats för skalbarhet kan du aktivera obegränsad tillväxt genom att lägga till fler noder.  Verktyget i moln miljöer gör det enkelt att lägga till eller ta bort noder, så det är enkelt att justera kapaciteten och du betalar bara för de resurser du använder.  
- Nack delarna Program måste utformas [för skalbarhet](service-fabric-concepts-scalability.md).  Program databaser och persistence kan kräva ytterligare arkitektur arbete för att skala.  [Pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillstånds känsliga tjänster, men gör det mycket enklare att skala dina program data.

Med fristående kluster kan du distribuera Service Fabric-kluster lokalt eller i valfri moln leverantör.  Nodtyper består av fysiska datorer eller virtuella datorer, beroende på din distribution. Jämfört med kluster som körs i Azure är processen för skalning av ett fristående kluster lite mer engagerad.  Du måste manuellt ändra antalet noder i klustret och sedan köra en uppgradering av kluster konfigurationen.

Borttagning av noder kan initiera flera uppgraderingar. Vissa noder är markerade med `IsSeedNode=”true”` tagg och kan identifieras genom att fråga kluster manifestet med hjälp av [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Borttagning av sådana noder kan ta längre tid än andra eftersom startnoderna måste flyttas runt i sådana scenarier. Klustret måste ha minst tre primära noder av Node-typ.

> [!WARNING]
> Vi rekommenderar att du inte sänker antalet noder under [kluster storleken på Tillförlitlighets nivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) för klustret. Detta påverkar möjligheten för Service Fabric system tjänsterna att replikeras över klustret och kommer att göra eller möjligen förstöra klustret.
>

När du skalar ett fristående kluster bör du ha följande rikt linjer i åtanke:
- Utbyte av primära noder bör utföras en nod efter en annan, i stället för att ta bort och sedan lägga till i batchar.
- Innan du tar bort en nodtyp kontrollerar du om det finns noder som refererar till nodtypen. Ta bort de här noderna innan du tar bort motsvarande nodtyp. När alla motsvarande noder har tagits bort kan du ta bort NodeType från kluster konfigurationen och påbörja en konfigurations uppgradering med [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Mer information finns i [skala ett fristående kluster](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ned eller lodrät skalning 
Ändrar resurserna (CPU, minne eller lagring) för noder i klustret.
- Fördelar Program varan och program arkitekturen är oförändrade.
- Nack delarna Begränsad skala, eftersom det finns en gräns för hur mycket du kan öka resurser på enskilda noder. Nedtid, eftersom du måste göra fysiska eller virtuella datorer offline för att kunna lägga till eller ta bort resurser.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


---
title: Azure Service Fabric fristående skalning av klustret | Microsoft Docs
description: Mer information om skalning fristående i Service Fabric-kluster i eller ut och upp eller ned.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 05049b9b08b4630c4299a6d3054c7815b082af52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516039"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skala fristående Service Fabric-kluster
Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster, kan du skala klustret horisontellt (ändra antalet noder) eller lodrätt (ändra resurser noder).  Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.  När klustret skalas skalas programmen automatiskt samt.

Varför skala klustret? Programbegäran ändras med tiden.  Du kan behöva öka klusterresurser för att uppfylla ökade programtrafik för arbetsbelastning eller ditt nätverk eller minska klusterresurser när behovet sjunker.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller horisontell skalning
Ändrar antalet noder i klustret.  När de nya noderna ansluta till klustret, den [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) flyttar tjänster till dem som minskar belastningen på de befintliga noderna.  Du kan också minska antalet noder om klustrets resurser inte som används effektivt.  Eftersom noder lämna klustret, tjänster flytta utanför dessa noder och belastningen ökar på övriga noder.  Minska antalet noder i ett kluster som körs i Azure kan du spara pengar, eftersom du betalar för antalet virtuella datorer du användning och inte arbetsbelastningen för dessa virtuella datorer.  

- Fördelar: Oändlig skala, i teorin.  Om programmet har utformats för skalbarhet, kan du aktivera Obegränsad tillväxt genom att lägga till fler noder.  Verktygsuppsättningen i miljöer i molnet gör det enkelt att lägga till eller ta bort noder, så att det är enkelt att justera kapacitet och du betalar bara för de resurser du använder.  
- Nackdelar: Program måste vara [utformats för skalbarhet](service-fabric-concepts-scalability.md).  Databaser och persistence kan kräva ytterligare arkitektoniska arbete att skala samt.  [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillståndskänsliga tjänster, men gör det mycket enklare att skala dina programdata.

Fristående kluster kan du distribuera Service Fabric-kluster lokalt eller i molnleverantör önskar.  Nodtyper består av fysiska datorer eller virtuella datorer, beroende på din distribution. Jämfört med kluster som körs i Azure, är skala ett fristående kluster lite mer komplicerat.  Du måste manuellt ändra antalet noder i klustret och sedan köra en uppgradering av klustret konfiguration.

Borttagning av noder kan starta flera uppgraderingar. Vissa noder har markerats med `IsSeedNode=”true”` tagga och kan identifieras genom att fråga klustret manifest med [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Borttagning av dessa noder kan ta längre tid än andra eftersom startvärdesnoder måste flyttas i sådana scenarier. Klustret måste ha minst tre primära noden typ noder.

> [!WARNING]
> Vi rekommenderar att du inte minskar antalet noder nedan den [klusterstorleken av tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) för klustret. Detta påverkar möjligheten för Service Fabric-systemtjänster att replikeras över klustret, och kommer göra instabil eller eventuellt förstöra klustret.
>

Tänk på följande riktlinjer när du skalar ett fristående kluster:
- Ersättning av primära noder måste vara utförs en nod efter en annan, i stället för att ta bort och sedan lägga till i batchar.
- Kontrollera om det finns några noder som refererar till nodtyp innan du tar bort en nodtyp. Ta bort dessa noder innan du tar bort motsvarande nodtyp. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och börja en konfiguration uppgradera med [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Mer information finns i [skala ett fristående kluster](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ner eller vertikal skalning 
Ändrar resurser (CPU, minne eller lagring) av noder i klustret.
- Fördelar: Arkitektur för programvara och programmet förblir densamma.
- Nackdelar: Begränsad skala, eftersom det inte finns en gräns för hur mycket du kan öka resurser på enskilda noder. Driftstopp, eftersom du behöver vidta fysiska eller virtuella datorer offline för att lägga till eller ta bort resurser.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


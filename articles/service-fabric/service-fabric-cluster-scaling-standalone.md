---
title: Fristående klusterskalning för Azure Service Fabric
description: Lär dig mer om skalning av fristående serviceprogramkluster in eller ut och uppåt eller nedåt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451918"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Fristående kluster för skalning av service fabric
Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra nodernas resurser).  Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.  När klustret skalas skalas även dina program automatiskt.

Varför skala klustret? Ansökan kräver förändring med tiden.  Du kan behöva öka klusterresurserna för att möta ökad programarbetsbelastning eller nätverkstrafik eller minska klusterresurser när efterfrågan sjunker.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller vågrät skalning
Ändrar antalet noder i klustret.  När de nya noderna har sammanfogat [klustret flyttar Klusterresurshanteraren](service-fabric-cluster-resource-manager-introduction.md) tjänster till dem vilket minskar belastningen på befintliga noder.  Du kan också minska antalet noder om klustrets resurser inte används effektivt.  När noder lämnar klustret flyttas tjänsterna från dessa noder och belastningen ökar på de återstående noderna.  Om du minskar antalet noder i ett kluster som körs i Azure kan du spara pengar, eftersom du betalar för antalet virtuella datorer som du använder och inte arbetsbelastningen på dessa virtuella datorer.  

- Fördelar: Oändlig skala, i teorin.  Om ditt program är utformat för skalbarhet kan du aktivera obegränsad tillväxt genom att lägga till fler noder.  Verktygen i molnmiljöer gör det enkelt att lägga till eller ta bort noder, så det är enkelt att justera kapaciteten och du betalar bara för de resurser du använder.  
- Nackdelar: Program måste [vara utformade för skalbarhet](service-fabric-concepts-scalability.md).  Programdatabaser och persistens kan kräva ytterligare arkitektoniskt arbete för att skala också.  [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillståndskänsliga tjänster gör det dock mycket enklare att skala dina programdata.

Fristående kluster gör att du kan distribuera Service Fabric-kluster lokalt eller i den molnleverantör du väljer.  Nodtyper består av fysiska datorer eller virtuella datorer, beroende på din distribution. Jämfört med kluster som körs i Azure är processen att skala ett fristående kluster lite mer involverad.  Du måste manuellt ändra antalet noder i klustret och sedan köra en uppgradering av klusterkonfigurationen.

Borttagning av noder kan initiera flera uppgraderingar. Vissa noder är `IsSeedNode=”true”` markerade med tagg och kan identifieras genom att fråga klustermanifestet med [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Det kan ta längre tid än andra att ta bort sådana noder, eftersom utsädesnoderna måste flyttas i sådana scenarier. Klustret måste underhålla minst tre primära nodtypnoder.

> [!WARNING]
> Vi rekommenderar att du inte sänker nodantalet under [klusterstorleken för tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) för klustret. Detta kommer att störa möjligheten för Service Fabric System Services som ska replikeras över klustret och kommer att destabilisera eller eventuellt förstöra klustret.
>

Tänk på skalning av ett fristående kluster:
- Byte av primära noder bör utföras en nod efter den andra, i stället för att ta bort och sedan lägga till i batchar.
- Innan du tar bort en nodtyp kontrollerar du om det finns några noder som refererar till nodtypen. Ta bort dessa noder innan du tar bort motsvarande nodtyp. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och påbörja en konfigurationsuppgradering med [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Mer information finns i [skala ett fristående kluster](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ned eller lodrät skalning 
Ändrar resurser (CPU, minne eller lagring) för noder i klustret.
- Fördelar: Programvaru- och programarkitekturen förblir densamma.
- Nackdelar: Finit skala, eftersom det finns en gräns för hur mycket du kan öka resurserna på enskilda noder. Driftstopp, eftersom du måste koppla från fysiska eller virtuella datorer för att lägga till eller ta bort resurser.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [programskalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av den flytande Azure-beräkningSDK.Scale an Azure cluster programmematically using the fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


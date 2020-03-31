---
title: Resurshanteraren för service fabric-kluster – placeringsprinciper
description: Översikt över ytterligare placeringsprinciper och regler för Service Fabric Services
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834422"
---
# <a name="placement-policies-for-service-fabric-services"></a>Placeringsprinciper för serviceinfrastrukturtjänster
Placeringsprinciper är ytterligare regler som kan användas för att styra tjänstplacering i vissa specifika, mindre vanliga scenarier. Några exempel på dessa scenarier är:

- Ditt Service Fabric-kluster sträcker sig över geografiska avstånd, till exempel flera lokala datacenter eller i Azure-regioner
- Din miljö sträcker sig över flera områden med geopolitisk eller juridisk kontroll, eller något annat fall där du har politiska gränser som du behöver för att genomdriva
- Det finns kommunikationsprestanda eller latensöverväganden på grund av stora avstånd eller användning av långsammare eller mindre tillförlitliga nätverkslänkar
- Du måste hålla vissa arbetsbelastningar samlokaliserade som en bästa insats, antingen med andra arbetsbelastningar eller i närheten av kunder

De flesta av dessa krav överensstämmer med klustrets fysiska layout, representerad som feldomäner i klustret. 

De avancerade placeringsprinciper som hjälper till att hantera dessa scenarier är:

1. Ogiltiga domäner
2. Obligatoriska domäner
3. Önskade domäner
4. Tillåt inte replikförpackning

De flesta av följande kontroller kan konfigureras via nodegenskaper och placeringsbegränsningar, men vissa är mer komplicerade. För att göra det enklare innehåller Service Fabric Cluster Resource Manager dessa ytterligare placeringsprinciper. Placeringsprinciper konfigureras per namngiven tjänstinstans. De kan också uppdateras dynamiskt.

## <a name="specifying-invalid-domains"></a>Ange ogiltiga domäner
Med principen **Ogiltig domänplacering** kan du ange att en viss feldomän är ogiltig för en viss tjänst. Denna policy säkerställer att en viss tjänst aldrig körs inom ett visst område, till exempel av geopolitiska eller företagspolitiska skäl. Flera ogiltiga domäner kan anges via separata principer.

<center>

![Ogiltigt domänexempel][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Ange obligatoriska domäner
Den domänplaceringsprincip som krävs kräver att tjänsten endast finns i den angivna domänen. Flera obligatoriska domäner kan anges via separata principer.

<center>

![Exempel på obligatorisk domän][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Ange en önskad domän för de primära replikerna för en tillståndskänslig tjänst
Den prioriterade primära domänen anger vilken feldomän som primärt ska anges. Den primära hamnar i denna domän när allt är hälsosamt. Om domänen eller den primära repliken misslyckas eller stängs av flyttas primärt till någon annan plats, helst i samma domän. Om den nya platsen inte finns i den önskade domänen flyttar Klusterresurshanteraren tillbaka den till önskad domän så snart som möjligt. Naturligtvis är den här inställningen bara meningsfull för tillståndskänsliga tjänster. Den här principen är mest användbar i kluster som sträcker sig över Azure-regioner eller flera datacenter men har tjänster som föredrar placering på en viss plats. Att hålla primärfärger nära sina användare eller andra tjänster bidrar till att ge lägre latens, särskilt för läsningar, som hanteras av primärfärger som standard.

<center>

![Önskade primära domäner och redundans][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Kräver replikdistribution och otillåten förpackning
Repliker distribueras _normalt_ över fel- och uppgraderingsdomäner när klustret är felfritt. Det finns dock fall där mer än en replik för en viss partition kan hamna tillfälligt packad i en enda domän. Anta till exempel att klustret har nio noder i tre feldomäner, fd:/0, fd:/1 och fd:/2. Låt oss också säga att din tjänst har tre repliker. Låt oss säga att noderna som användes för dessa repliker i FD:/1 och fd:/2 gick ner. Normalt föredrar Klusterresurshanteraren andra noder i samma feldomäner. I det här fallet, låt oss säga på grund av kapacitetsproblem ingen av de andra noderna i dessa domäner var giltiga. Om Cluster Resource Manager skapar ersättningar för dessa repliker måste den välja noder i fd:/0. Om du gör _det_ skapas dock en situation där feldomänbegränsningen överträds. Packning repliker ökar risken för att hela replikuppsättningen kan gå ner eller gå förlorade. 

> [!NOTE]
> Mer information om begränsningar och villkorsprioriteringar i allmänhet finns i [det här avsnittet](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Om du någonsin har sett ett`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`hälsomeddelande som " ", då har du träffat detta tillstånd eller något liknande. Vanligtvis packas bara en eller två repliker ihop tillfälligt. Så länge det finns färre än ett kvorum av repliker i en viss domän, du är säker. Packning är sällsynt, men det kan hända, och oftast dessa situationer är övergående eftersom noderna kommer tillbaka. Om noderna håller sig nere och Cluster Resource Manager behöver skapa ersättningar finns det vanligtvis andra noder som är tillgängliga i de ideala feldomänerna.

Vissa arbetsbelastningar skulle föredra att alltid ha målantalet repliker, även om de är packade i färre domäner. Dessa arbetsbelastningar satsar mot totalt samtidiga permanenta domänfel och kan vanligtvis återställa lokalt tillstånd. Andra arbetsbelastningar tar hellre driftstopp tidigare än riskkorrigering eller förlust av data. De flesta produktionsarbetsbelastningar körs med fler än tre repliker, fler än tre feldomäner och många giltiga noder per feldomän. På grund av detta tillåter standardbeteendet domänpaketering som standard. Standardbeteendet gör att normal balansering och redundans kan hantera dessa extrema fall, även om det innebär tillfällig domänförpackning.

Om du vill inaktivera sådan förpackning för en viss `RequireDomainDistribution` arbetsbelastning kan du ange principen för tjänsten. När den här principen har angetts säkerställer Klusterresurshanteraren att inga två repliker från samma partition körs i samma fel- eller uppgraderingsdomän.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Nu, skulle det vara möjligt att använda dessa konfigurationer för tjänster i ett kluster som inte var geografiskt spänns över? Det kan du, men det finns ingen bra anledning också. De domänkonfigurationer som krävs, ogiltiga och önskade bör undvikas om inte scenarierna kräver dem. Det är inte meningsfullt att försöka tvinga en viss arbetsbelastning att köras i ett enda rack, eller att föredra något segment av ditt lokala kluster framför en annan. Olika maskinvarukonfigurationer bör spridas över feldomäner och hanteras via normala placeringsbegränsningar och nodegenskaper.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster [finns i Läs mer om att konfigurera tjänster](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

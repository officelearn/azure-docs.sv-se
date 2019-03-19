---
title: Service Fabric Cluster Resource Manager - Placeringsprinciper | Microsoft Docs
description: Översikt över ytterligare placeringsprinciper och regler för Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 985d41d3a00974e25c9abc4709c5bf5e662f7a50
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086045"
---
# <a name="placement-policies-for-service-fabric-services"></a>Placeringsprinciper för service fabric-tjänster
Placeringsprinciper är ytterligare regler som kan användas för att styra tjänsteplacering i vissa specifika, mindre vanliga scenarier. Några exempel på dessa scenarier är:

- Service Fabric-klustret sträcker sig över geografiska avstånd, till exempel flera lokala Datacenter eller i Azure-regioner
- Din miljö sträcker sig över flera områden i geopolitisk eller juridiska eller några andra fall där du har principen gränser som du behöver tillämpa
- Det finns kommunikation prestanda eller latens saker på grund av stora avstånd eller användning av långsammare eller mindre tillförlitliga nätverkslänkar
- Du måste behålla vissa arbetsbelastningar samordnad efter bästa förmåga, antingen med andra arbetsbelastningar eller närhet till kunder

De flesta av dessa krav överensstämmer med fysiska layouten för klustret, som feldomäner i klustret. 

De avancerade placering principerna som hjälper dig att lösa de här scenarierna är:

1. Ogiltig domäner
2. Nödvändiga domäner
3. Prioriterade domäner
4. Inte tillåta replikering paketering av statistik

De flesta av följande kontroller kan konfigureras via nodegenskaper och placeringsbegränsningar, men vissa är mer komplicerad. Om du vill göra det enklare, ger Service Fabric Cluster Resource Manager dessa ytterligare placeringsprinciper. Placeringsprinciper är konfigurerade på basis av per heter service-instans. De kan också uppdateras dynamiskt.

## <a name="specifying-invalid-domains"></a>Om du anger ogiltiga domäner
Den **InvalidDomain** placering principen kan du ange att en viss Feldomän är ogiltig för en specifik tjänst. Den här principen säkerställer att en viss tjänst aldrig körs i ett visst område, till exempel för orsaker i geopolitisk eller företagets policy. Flera ogiltig domäner kan anges via separata principer.

<center>

![Ogiltig domän-exempel][Image1]
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
## <a name="specifying-required-domains"></a>Ange nödvändiga domäner
Domän placering principen kräver att tjänsten finns bara i den angivna domänen. Du kan ange flera nödvändiga domäner via separata principer.

<center>

![Domän-exempel][Image2]
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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Ange en önskad domän för de primära replikerna av en tillståndskänslig tjänst
Den primära domänen med önskad anger feldomänen för att placera primärt i. Primärt som identisk i den här domänen när allt är felfri. Om domänen eller den primära repliken misslyckas eller stängs av, flyttar den primära servern till en annan plats, helst i samma domän. Om den här nya platsen finns inte i den primära domänen, flyttar Cluster Resource Manager den tillbaka till den primära domänen så snart som möjligt. Den här inställningen är naturligt bara meningsfullt för tillståndskänsliga tjänster. Den här principen används ofta i kluster som omfattas i Azure-regioner eller flera datacenter, men har tjänster som föredrar placering i en viss plats. Att hålla USA: s presidentval nära användarna eller andra tjänster och ger kortare svarstider, särskilt för läsningar som hanteras av USA: s presidentval som standard.

<center>

![Prioriterade primära domäner och redundans][Image3]
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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Kräver distribution av repliken och inte tillåta paketering av statistik
Repliker är _normalt_ fördelade på fel- och uppgraderingsdomäner när klustret är felfritt. Men finns det fall där fler än en replik för en given partition kan det sluta tillfälligt packat i en enda domän. Exempel: Anta att klustret har nio noder i tre feldomäner, fd: / 0, fd: / 1 och fd: / 2. Även anta att tjänsten har tre repliker. Anta som de noder som användes för dessa repliker i fd: / 1 och fd: / 2 bröts. Klusterresurshanteraren föredrar normalt andra noder i dessa samma feldomäner. I det här fallet vi antar att på grund av kapacitetsproblem ingen av de andra noderna i dessa domäner är giltiga. Om Cluster Resource Manager skapas ersättningar för dessa repliker måste det skulle behöva välja noder i fd: / 0. Dock gör _som_ skapar en situation där Feldomän villkoret överskrids. Paketera repliker ökar risken för att hela replikuppsättningens kunde stängs av eller att gå förlorade. 

> [!NOTE]
> Mer information om begränsningar och begränsning prioriteter i allmänhet finns i [det här avsnittet](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Om du någonsin fått ett meddelande om hälsotillstånd som ”`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`”, och du har uppnått det här tillståndet eller liknande, den. Vanligtvis bara en eller två repliker är packade tillsammans tillfälligt. Så länge det finns färre än ett kvorum av repliker i en viss domän, är du säker. Paketering av statistik är sällsynt, men kan det hända. vanligtvis sådana situationer är tillfälligt eftersom noderna kommer tillbaka. Om noderna Håll ned och Cluster Resource Manager behöver skapa ersättningar, finns vanligtvis det andra noder i de perfekta feldomänerna.

Vissa arbetsbelastningar föredrar alltid med antalet repliker, även om de är förpackade till färre domäner. Dessa arbetsbelastningar är slår vad om mot fel på totalt antal samtidiga permanent domän och kan vanligtvis att återställa lokala tillstånd. Andra arbetsbelastningar vidtar i stället stilleståndstid tidigare än risken är korrekt eller förlust av data. De flesta produktionsarbetsbelastningar kör med fler än tre kopior, fler än tre feldomäner och många giltig noder per feldomän. Därför kan standardbeteendet domän paketering av statistik som standard. Standardbeteendet kan normalt belastningsutjämning och redundans för att hantera dessa extrema fall, även om det innebär att tillfälliga domän paketering av statistik.

Om du vill inaktivera sådana paketering av statistik för en viss arbetsbelastning, kan du ange den `RequireDomainDistribution` principen på tjänsten. När den här principen anges medför Cluster Resource Manager inga två repliker från samma partition som körs i samma domän för fel eller uppgradering.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Nu, skulle det vara möjligt att använda de här konfigurationerna för tjänster i ett kluster som inte geografiskt erbjöds? Du kan, men det är inte en bra anledning för. Konfigurationerna som krävs, ogiltig och önskad domän bör undvikas, såvida inte scenarierna som kräver att användaren. Det meningsfullt inte någon att försöka framtvinga en viss arbetsbelastning för att köra i ett enda rack eller föredrar vissa segment i ditt lokala kluster framför en annan. Olika maskinvarukonfigurationer bör fördelade över feldomäner och hanteras via normala placeringsbegränsningar och nodegenskaper.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

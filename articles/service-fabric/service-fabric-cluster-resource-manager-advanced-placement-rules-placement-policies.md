---
title: Service Fabric klustret Resource Manager - Placeringsprinciper | Microsoft Docs
description: Översikt över ytterligare placeringsprinciper och regler för Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 66c51b08884c9d7a4d522c94f7b81774ec7a8bda
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642010"
---
# <a name="placement-policies-for-service-fabric-services"></a>Placeringsprinciper för service fabric-tjänster
Placeringsprinciper finns ytterligare regler som kan användas för att styra tjänsten placering i vissa specifika, mindre vanliga scenarier. Det är några exempel på dessa scenarier:

- Service Fabric-kluster omfattar geografiska avstånd, till exempel flera lokala Datacenter eller i Azure-regioner
- Din miljö sträcker sig över flera områden av geopolitiska eller juridiska kontroll eller några andra fall där du har princip gränser som du behöver tillämpa
- Det finns kommunikation prestanda eller latens saker på grund av stora avstånd eller användning av långsammare eller mindre tillförlitliga nätverkslänkar
- Du måste behålla vissa arbetsbelastningar samordnat som en bästa prestanda, antingen med andra arbetsbelastningar eller närhet till kunder

De flesta av dessa krav överensstämmer med fysiska layouten för klustret, visas i form av feldomäner i klustret. 

De avancerade placeringsprinciper som hjälper dig att lösa dessa scenarier är:

1. Ogiltig domäner
2. Nödvändiga domäner
3. Prioritera domäner
4. Inte tillåta replikering förpackning

De flesta av följande kontroller kan konfigureras via egenskaper för en nod och placeringen, men vissa är mer komplicerad. För att göra det enklare tillhandahåller Service Fabric klustret Resource Manager dessa ytterligare placeringsprinciper. Placering principerna är konfigurerade för tjänst per namngivna instansen. De kan också uppdateras dynamiskt.

## <a name="specifying-invalid-domains"></a>Ange ogiltig domäner
Den **InvalidDomain** placering principen kan du ange att en viss Feldomän är ogiltig för en specifik tjänst. Den här principen ser till att en viss tjänst aldrig kör i ett visst område, till exempel grund geopolitiska eller företagets policy. Ogiltig flera domäner kan anges via olika principer.

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
Nödvändiga placering domänprincipen kräver att tjänsten finns endast i den angivna domänen. Flera domäner som krävs kan anges via olika principer.

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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Ange en önskad domän för primära replikerna för en tillståndskänslig service
Önskad primär domän anger feldomänen för att placera primärt i. Primärt avslutas med den här domänen när allt är felfri. Om domänen eller den primära repliken misslyckas eller stängs av, flyttar den primära servern till en annan plats, helst i samma domän. Om den nya platsen inte är i önskad domän kan flyttas klustret Resource Manager den tillbaka till önskad domän så snart som möjligt. Den här inställningen är naturligt bara meningsfullt för tillståndskänsliga tjänster. Den här principen är mest användbara i kluster som omfattas i Azure-regioner eller flera Datacenter har men tjänster som föredrar placering på en viss plats. Att hålla primärfärgerna nära användarna eller andra tjänster ger kortare svarstid, särskilt för läsningar som hanteras av primärfärgerna som standard.

<center>
![Önskad primära domänerna och redundans][Image3]
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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Kräver distribution av repliken och inte tillåta förpackning
Repliker är _normalt_ fördelad över fel- och domäner när klustret är felfri. Det finns dock fall där fler än en replik för en given partition kan hamna som är tillfälligt packade i en enda domän. Till exempel: Antag att klustret har nio noder i tre feldomäner, fd: / 0, fd: / 1 och fd: / 2. Även anta att tjänsten har tre repliker. Anta att som de noder som användes för dessa repliker i fd: / 1 och fd: / 2 avslutades. Normalt föredrar klustret Resource Manager andra noder i de samma feldomäner. I det här fallet anta på grund av problem med kapacitet för de andra noderna i dessa domäner inte har någon giltig. Om klustret Resource Manager bygger ersättningar för dessa repliker, måste det Välj noder i fd: / 0. Dock gör _som_ skapar en situation där Feldomän villkoret överskrids. Packa repliker ökar kan risken för att hela replikuppsättningen kraschar eller gå förlorade. 

> [!NOTE]
> Mer information om begränsningar och begränsning prioriteter i allmänhet kolla [i det här avsnittet](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Om du aldrig ser ett meddelande om hälsa som ”`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`”, och du har nått det här villkoret eller något som liknar den. Vanligtvis bara en eller två repliker är packade tillsammans tillfälligt. Så länge det är färre än ett kvorum av repliker i en viss domän, är du säker. Packkorg är sällsynt, men kan det hända, och vanligen dessa situationer är tillfälligt eftersom noderna kommer tillbaka. Om noderna Håll ned och klustret Resource Manager behöver skapa ersättningar, finns vanligtvis det andra noder i de perfekta feldomäner.

Vissa arbetsbelastningar föredrar alltid ha mål antalet repliker, även om de packade till färre domäner. Dessa arbetsbelastningar vadslagning mot totala samtidiga permanent domän fel och kan vanligtvis återställa lokala tillstånd. Andra arbetsbelastningar tar snarare avbrottstiden tidigare än risken är korrekt eller förlust av data. De flesta produktionsarbetsbelastningar kör med fler än tre replikeringar och fler än tre feldomäner många giltig noder per feldomän. Därför kan det fördefinierade beteendet för domänen packkorg som standard. Standardbeteendet kan normal belastningsutjämning och redundans för att hantera dessa extrema fall, även om tillfällig domän packkorg innebär.

Om du vill inaktivera sådana packkorg för en viss arbetsbelastning, kan du ange den `RequireDomainDistribution` principen på tjänsten. När den här principen anges garanterar klustret Resource Manager inga två repliker från samma partition som körs i samma domän som fel eller en uppgradering.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Nu, skulle det vara möjligt att använda de här konfigurationerna för tjänster i ett kluster som inte geografiskt omfattas? Du kan, men det är inte en bra anledning för. Konfigurationerna som krävs, ogiltig och önskad domän bör undvikas om scenarier som inte behöver dem. Den passar inte någon att tvinga en viss arbetsbelastning att köra i ett enda rack eller föredrar en annan vissa segmentet i din lokala klustret. Olika maskinvarukonfigurationer bör sprids feldomäner och hanteras via normala placeringen och egenskaper för en nod.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

---
title: Service Fabric klustret Resource Manager - integrering | Microsoft Docs
description: En översikt över integreringspunkter mellan kluster Resource Manager och Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3f93ca94d5aa3e95637a53a4c8fe3d9d264dd58c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208292"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Klustret resource manager-integrering med hantering av Service Fabric-kluster
Resurshanteraren för Service Fabric klustret enheten inte uppgraderingar i Service Fabric, men den är inblandad. Det första sättet som klustret Resource Manager hjälper med management är genom att spåra tillståndet som klustret och tjänster i den. Klustret Resource Manager skickar ut hälsorapporter när den inte kan lägga till klustret i önskad konfiguration. Till exempel om det inte finns tillräcklig kapacitet skickar klustret Resource Manager ut hälsa varningar och fel som tyder på problem. En annan typ av integrering har att göra med så här fungerar uppgraderingar. Klustret Resource Manager ändrar sitt beteende något under uppgraderingar.  

## <a name="health-integration"></a>Integrering av hälsotillstånd
Klustret Resource Manager spårar ständigt regler som du har definierat placera dina tjänster. Den spårar också återstående kapacitet för varje mått på noderna i klustret och i klustret som helhet. Om den inte kan uppfylla dessa regler eller om det finns tillräcklig kapacitet orsakat hälsa varningar och fel. Om exempelvis en nod är över kapacitet och klustret Resource Manager kommer att försöka åtgärda situationen genom att flytta tjänster. Om den inte kan korrigera situationen skickar en varning om hälsa som anger vilken nod är överbelastade och för vilka mått.

Ett annat exempel på resurshanterare hälsovarningar är överträdelser av placeringsbegränsningar. Till exempel om du har definierat begränsningen placering (som `“NodeColor == Blue”`) och Resource Manager identifierar ett brott mot denna begränsning, den genererar en varning om hälsa. Detta gäller för anpassade villkor och standardbegränsningar (till exempel fel och uppgradera domänen begränsningar).

Här är ett exempel på en sådan hälsorapport. I det här fallet är hälsorapporten för en av systemtjänsten partitioner. Hälsotillstånd meddelandet anger repliker av den partitionen tillfälligt packas i för få uppgradera domäner.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Här är vad meddelandet hälsa tala om för oss är:

1. Alla repliker själva är felfria: varje har AggregatedHealthState: Ok
2. Uppgradera domän distribution villkoret överskrids för närvarande. Det innebär att en viss uppgradera domän har flera repliker från den här partitionen än den borde.
3. Vilken nod som innehåller repliken orsakar överträdelse. I det här fallet är den noden med namnet ”Node.8”
4. Om en uppgradering för närvarande pågår för detta partitions (”för närvarande uppgradering--false”)
5. Principen för programdistribution för den här tjänsten: ”Distribution princip--packkorg”. Detta styrs av den `RequireDomainDistribution` [policy placering](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). ”Packa” anger att i det här fallet DomainDistribution _inte_ krävs, så att vi vet att policy placering inte har angetts för den här tjänsten. 
6. När rapporten har hänt - 8/10/2015 7:13:02 PM.

Information som den här befogenheter aviseringar som utlöses i produktion så att du vet något är fel och används också för att identifiera och stoppa felaktiga uppgraderingar. I så fall skulle vi vill se om vi kan ta reda på varför resurshanteraren var tvungen att packa replikerna i domänen uppgradera. Packa vanligtvis beror på tillfälliga noder i andra uppgradera domäner har ned, till exempel.

Att anta klustret Resource Manager försöker placera vissa tjänster, men det inte finns några lösningar som fungerar. När tjänster inte kan placeras, är det vanligtvis av något av följande skäl:

1. Vissa övergående tillstånd har gjort det omöjligt att placera den här tjänstinstansen eller replik korrekt
2. Tjänstens placering kraven är unsatisfiable.

I dessa fall kan hjälpa dig att avgöra varför tjänsten kan inte placeras i hälsorapporter från klustret Resource Manager. Den här processen kallas begränsningen eliminering sekvensen. Under den går systemet igenom konfigurerade begränsningar som påverkar tjänsten och poster de undanröjer. Det här sättet när tjänsterna inte kunna släppas, du kan se vilka noder har tagits bort och varför.

## <a name="constraint-types"></a>Villkorstyper
Vi pratar om var och en av de olika begränsningarna i dessa rapporter om hälsotillstånd. Du ser hälsa meddelanden relaterade till dessa villkor när repliker inte kan placeras.

* **ReplicaExclusionStatic** och **ReplicaExclusionDynamic**: dessa villkor anger att en lösning avvisades eftersom två tjänstobjekt från samma partition måste placeras på samma nod. Detta är inte tillåten eftersom sedan fel på noden påverkar alltför partitionen. ReplicaExclusionStatic och ReplicaExclusionDynamic är nästan samma regel och verkligen är viktiga inte skillnaderna. Om du ser en begränsning eliminering sekvens som innehåller antingen ReplicaExclusionStatic eller ReplicaExclusionDynamic begränsningen tror klustret Resource Manager att det inte finns tillräckligt med noder. Detta kräver återstående lösningar för att använda dessa ogiltig placeringar som inte är tillåtet. Begränsningarna i sekvensen kommer vanligtvis berätta varför noder som elimineras i första hand.
* **PlacementConstraint**: Om du ser det här meddelandet innebär att vi elimineras vissa noder eftersom de inte matchar tjänstens placeringen. Vi spåra ut konfigurerade placeringen som en del av det här meddelandet. Detta är normalt om du har en begränsning för placering som definierats. Men om placering begränsningen felaktigt orsakar för många noder elimineras det här är hur du vill se.
* **NodeCapacity**: den här begränsningen innebär att klustret Resource Manager inte kunde placera replikerna på de angivna noderna eftersom som skulle placera dem över kapacitet.
* **Tillhörighet**: det här villkoret visar vi inte kunde placera repliken på de berörda noderna eftersom det skulle orsaka en överträdelse av begränsningen tillhörighet. Mer information om mappning mellan är i [i den här artikeln](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** och **UpgradeDomain**: den här begränsningen eliminerar noder om placera repliken på de angivna noderna skulle orsaka packa i ett visst fel eller en domän. Flera exempel diskutera detta villkor visas i avsnittet på [fel- och begränsningar för domänen och resultatet](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: du normalt inte bör se den här begränsningen att ta bort noder från lösningen eftersom den körs som en optimering som standard. Prioriterade platsbegränsningen finns under uppgraderingar. Du uppgraderar används den för att flytta tjänster till där de fanns när uppgraderingen är igång.

## <a name="blocklisting-nodes"></a>Blocklisting noder
Ett annat hälsa meddelande klustret Resource Manager-rapporter är när noder är blocklisted. Du kan se blocklisting som en tillfällig begränsning som tillämpas automatiskt åt dig. Noder får blocklisted när de har upprepade fel när den startas instanser av den typ av tjänst. Noderna är blocklisted på grundval av per tjänsttyp. En nod kan vara blocklisted för en tjänst men inte en annan. 

Du ser blocklisting startar ofta under utveckling: vissa bugg gör din tjänstvärden kraschar vid start. Service Fabric försöker skapa tjänstevärden några gånger och felet kvarstår. Efter några försök noden hämtar blocklisted och klustret Resource Manager kommer att försöka skapa tjänsten någon annanstans. Om felet upprepas på flera noder, är det möjligt att alla giltiga noder i klustret hamnar blockeras. Blocklisting kan också ta bort så många noder att det finns inte tillräckligt med har starta tjänsten för att uppfylla önskade skalan. Normalt visas ytterligare fel eller varningar från klustret Resource Manager som anger som tjänsten är lägre än den önskade replik eller instansantal, samt hälsa meddelanden som anger vad felet är som leder till blocklisting i först Placera.

Blocklisting är inte ett permanent villkor. Noden tas bort från blocklist och Service Fabric kan aktivera tjänsterna på noden igen efter några minuter. Om tjänster fortsätter att misslyckas, är noden blocklisted för tjänsten igen. 

### <a name="constraint-priorities"></a>Begränsningen prioriteter

> [!WARNING]
> Ändra begränsningen prioriteter rekommenderas inte och kan ha betydande negativa effekter på klustret. I informationen nedan ges för referens för begränsningen standardegenskaper och deras beteende. 
>

Med alla dessa villkor, kan du ha tänker ”Hej – jag tror att fel domän begränsningar är viktigaste i systemet. För att säkerställa domänbegränsningar fel inte bröt mot, jag vill bryta mot andra begränsningar ”.

Begränsningar kan konfigureras med olika prioritet. Dessa är:

   - ”hårda” (0)
   - ”soft” (1)
   - ”optimering” (2)
   - ”off” (-1). 
   
De flesta av begränsningarna som är konfigurerade som hårda begränsningar som standard.

Det är ovanligt att ändra prioriteten för begränsningar. Det har förekommit gånger där begränsningen prioriteter behövs för att ändra vanligtvis för att kringgå vissa fel eller problem som påverkade miljön. Normalt möjlighet att begränsningen prioritet infrastruktur har arbetat mycket bra, men det behövs inte ofta. I de flesta fall är allt placerad på deras Standardprioriteter. 

Prioritetsnivå inte innebär att ett visst villkor _kommer_ ha brutits, eller som ska alltid vara uppfyllda. Begränsningen prioriteter definiera begränsningar är framtvingade i vilken ordning. Prioriteringar definiera kompromisser när är det omöjligt att uppfylla alla villkor. Vanligtvis uppfyllas alla villkor om det inte finns något annat i miljön. Några exempel på scenarier som kommer att leda till överträdelser av villkor är i konflikt villkor eller stort antal samtidiga fel.

I avancerade fall kan ändra du begränsningen prioriteter. Anta exempelvis att du vill se till att tillhörighet skulle alltid ha brutits när det är nödvändigt för att lösa problem med noden kapacitet. För att åstadkomma detta kan du ange prioritet för mappning mellan begränsningen på ”soft” (1) och lämna kapacitet begränsningen anges till ”hårda” (0).

Standardvärden för prioritet för olika villkor anges i följande konfiguration:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Fel domän- och begränsningar för domänen
Klustret Resource Manager vill hålla services sprids ut bland fel- och domäner. Den modeller detta som en begränsning i klustret Resource Manager-motorn. Mer information om hur de används och deras specifika beteende, finns i artikeln på [klusterkonfigurationen](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Klustret Resource Manager kan behöva packa några repliker i en uppgraderingsdomän för att kunna hantera uppgraderingar, fel eller andra kränkningar begränsningen. Packa i fel eller uppgradera domäner normalt sker endast om det finns några fel eller andra omsättning i systemet som förhindrar korrekt placering. Om du vill förhindra packkorg även under sådana situationer kan du använda den `RequireDomainDistribution` [policy placering](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Observera att detta kan påverka tjänstetillgänglighet och tillförlitlighet som en sidoeffekt, så den noggrant igenom.

Om miljön är korrekt konfigurerade uppfylls alla begränsningar fullständigt, även under uppgraderingar. Det viktiga är att klustret Resource Manager bevakar för dina villkor. När den identifierar en överträdelse det omedelbart rapporterar den och försöker åtgärda problemet.

## <a name="the-preferred-location-constraint"></a>Prioriterade platsbegränsningen
PreferredLocation-begränsning är lite annorlunda eftersom den har två olika användningsområden. En används för den här begränsningen under programuppgraderingar. Klustret Resource Manager hanterar automatiskt den här begränsningen under uppgraderingar. Den används för att säkerställa att när uppgraderingar är klara att repliker gå tillbaka till sina ursprungliga platser. Andra PreferredLocation begränsningen används för den [ `PreferredPrimaryDomain` policy placering](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Båda dessa är optimeringar och därför PreferredLocation begränsningen är den enda begränsningen inställd på ”optimering” som standard.

## <a name="upgrades"></a>Uppgraderingar
Klustret Resource Manager hjälper också under program- och klusteruppgradering under vilken den har två jobb:

* Se till att reglerna för klustret inte äventyras
* Försök att gå smidigt uppgraderingen

### <a name="keep-enforcing-the-rules"></a>Behåll tvingande regler
Det huvudsakliga bör vara medveten om är att tillämpas reglerna – strikt begränsningarna som placeringen och kapacitet - fortfarande under uppgraderingar. Placeringen se till att dina arbetsbelastningar endast köras var de ska kunna, även under uppgraderingar. När tjänster är mycket begränsad, kan uppgraderingar ta längre tid. När tjänsten eller den körs på noden tas för en uppdatering kan det finnas några alternativ för där du kan gå.

### <a name="smart-replacements"></a>Smart ersättningar
När en uppgradering startar tar resurshanteraren en ögonblicksbild av den aktuella ordningen i klustret. Eftersom varje uppgradera domän är klar försöker den returnera de tjänster som fanns i uppgradera domänen till deras ursprungliga sortering. Det här sättet finns högst två övergångar för en tjänst under uppgraderingen. Det finns en flytta bort från den aktuella noden och en flytta tillbaka. Returnerar det kluster eller en tjänst till hur den var innan du uppgraderar betyder också uppgraderingen inte påverkar layouten för klustret. 

### <a name="reduced-churn"></a>Minskad omsättning
En annan sak som sker under uppgraderingar är som klustret Resource Manager-stänger av belastningsutjämning. Hindrar NLB förhindrar onödiga reaktioner uppgraderingen, som att flytta tjänster i noder som har tömts för uppgraderingen. Om uppgraderingen i fråga är en uppgradering av klustret, balanserade hela klustret inte under uppgraderingen. Begränsningen kontrollerar förblir aktiv, endast transport baserat på proaktiv fördelningen av mått är inaktiverad.

### <a name="buffered-capacity--upgrade"></a>Buffrat kapacitet och uppgradering
Vanligtvis vill du att uppgraderingen ska kunna slutföra även om klustret är begränsad eller nära full. Hantera kapacitet i klustret är även viktigare under uppgraderingar än vanligt. Beroende på antalet uppgraderingsdomäner, måste mellan 5 och 20 procent av kapacitet migreras som uppgraderingen samlar i klustret. Arbetet har gå någonstans. Det är där begreppet [buffras kapaciteter](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) är användbart. Buffrat kapacitet följs under normal drift. Klustret Resource Manager kan fylla noder upp till sina total kapacitet (förbrukar bufferten) under uppgraderingar om det behövs.

## <a name="next-steps"></a>Nästa steg
* Börja från början och [få en introduktion till Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

---
title: Service Fabric Cluster Resource Manager - integrering | Microsoft Docs
description: En översikt över integreringspunkter mellan Cluster Resource Manager och Service Fabric Management.
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
ms.openlocfilehash: 7a1bab75521730f7e80e5b86112bbb0aed129f88
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917882"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Klustret resource manager-integrering med hantering av Service Fabric
Service Fabric Cluster Resource Manager enhet inte uppgraderingar i Service Fabric, men den ingår. Det första sättet som Cluster Resource Manager hjälper med management är genom att spåra önskat tillstånd för klustret och tjänster i den. Cluster Resource Manager skickar ut hälsorapporter när det inte placerar klustret i önskad konfiguration. Till exempel om det finns inte tillräckligt med kapacitet skickar Cluster Resource Manager ut hälsotillståndsvarningar och fel som tyder på problemet. En annan typ av integrering har att göra med så här fungerar uppgraderingar. Klusterresurshanteraren ändrar något sitt beteende under uppgraderingar.  

## <a name="health-integration"></a>Health-integrering
Klusterresurshanteraren spårar ständigt de regler som du har definierat för att placera dina tjänster. Den spårar också återstående kapacitet för varje mått på noderna i klustret och i klustret som helhet. Om den inte kan uppfylla dessa regler eller om det finns inte tillräckligt med kapacitet, genereras health varningar och fel. Exempel: om en nod är överbelastade och Cluster Resource Manager försöker åtgärda situationen genom att flytta tjänster. Om det går inte att komma tillrätta med situationen genererar en varning för hälsotillstånd som anger vilken nod som är överbelastade och vilka mått.

Ett annat exempel på hälsovarningar för Resource Manager är överträdelser av placeringsbegränsningar. Exempel: Om du har definierat en begränsning av placeringen (till exempel `“NodeColor == Blue”`) och Resource Manager identifierar ett brott mot denna begränsning, den genererar en varning om hälsotillstånd. Detta gäller för anpassade villkor och standardbegränsningar (till exempel Feldomän och Uppgraderingsdomänen begränsningar).

Här är ett exempel på en sådan hälsorapport. I det här fallet är hälsorapporten för en av systemtjänsten partitioner. Hälsotillstånd meddelandet anger repliker av den partitionen är tillfälligt förpackade i för få uppgradera domäner.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


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

Här är vad meddelandet hälsotillstånd tala om för oss är:

1. Alla repliker själva är felfri: var och en har AggregatedHealthState: Ok
2. Uppgradera domän distribution villkoret överskrids för närvarande. Det innebär att en viss uppgradera domän har flera repliker från den här partitionen än den borde.
3. Vilken nod innehåller repliken orsakar överträdelsen. I det här fallet är det nod med namnet ”Node.8”
4. Om en uppgradering för närvarande pågår för detta partitionera (”för närvarande uppgradering--FALSKT”)
5. Principen för programvarudistribution för den här tjänsten: ”Distribution princip--förpackning”. Detta styrs av den `RequireDomainDistribution` [placering princip](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). ”Paketering” anger att i det här fallet DomainDistribution _inte_ krävs, så att vi vet att placeringen principen inte har angetts för den här tjänsten. 
6. När rapporten har hänt – 8/10/2015 19:13:02: 00

Information som den här befogenheter aviseringar som utlöses i produktionsmiljön så att du vet något har gått fel och används också för att identifiera och stoppa felaktig uppgraderingar. I det här fallet vill vi skulle se om vi kan ta reda på varför Resource Manager var tvungen att bygga repliker i Uppgraderingsdomänen. Vanligtvis späcka beror på tillfälliga noder i den andra uppgradera domäner har ned, till exempel.

Vi antar att Cluster Resource Manager försöker placera vissa tjänster, men det inte finns några lösningar som fungerar. När tjänster inte kan placeras, är det vanligtvis för någon av följande orsaker:

1. Vissa övergående tillstånd har gjort det omöjligt att placera den här tjänstinstansen eller repliken korrekt
2. Tjänstens placering kraven är unsatisfiable.

I dessa fall hälsorapporter från Cluster Resource Manager hjälper dig att avgöra varför tjänsten kan inte placeras. Vi kallar den här processen begränsningen eliminering sekvensen. Under den går systemet igenom de konfigurera begränsningar som påverkar tjänsten och -poster de undanröjer. Det här sättet när tjänster är inte kunna lagras, du kan se vilka noder som har tagits bort och varför.

## <a name="constraint-types"></a>Villkorstyper
Nu ska vi prata om var och en av de olika begränsningarna i dessa rapporter om hälsotillstånd. Du kan se hälsotillstånd meddelanden relaterade till de här begränsningarna när repliker inte kan placeras.

* **ReplicaExclusionStatic** och **ReplicaExclusionDynamic**: de här begränsningarna indikerar att en lösning avvisades eftersom två service-objekt från samma partition måste placeras på samma nod. Detta är inte tillåten eftersom sedan fel på noden påverkar alltför partitionen. ReplicaExclusionStatic och ReplicaExclusionDynamic är nästan samma regel och skillnaderna verkligen har betydelse inte. Om du ser en begränsning eliminering sekvens som innehåller antingen ReplicaExclusionStatic eller ReplicaExclusionDynamic begränsning tror Klusterresurshanteraren att det inte finns tillräckligt många noder. Detta kräver återstående lösningar för att använda de här ogiltiga placeringar, vilket inte är tillåtet. Andra villkor i följd kommer vanligtvis berätta för oss varför noder som elimineras i första hand.
* **PlacementConstraint**: Om du ser det här meddelandet innebär att vi eliminerat vissa noder eftersom de inte matchade placeringsbegränsningar för tjänsten. Vi spåra ut konfigurerade placeringen som en del av det här meddelandet. Detta är normalt om du har en begränsning för placering som definierats. Men om placering begränsningen felaktigt orsakar för många noder elimineras det här är hur du skulle se.
* **NodeCapacity**: den här begränsningen innebär att Cluster Resource Manager inte kunde placera replikerna på de angivna noderna eftersom som placerar dem över kapacitet.
* **Tillhörighet**: det här villkoret visar vi inte kunde placera repliken på de berörda noderna eftersom det skulle orsaka en överträdelse av tillhörighet begränsningen. Mer information om mappning mellan och är i [i den här artikeln](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** och **UpgradeDomain**: den här begränsningen eliminerar noder om placera repliken på de angivna noderna skulle orsaka paketering i ett visst fel eller en uppgraderingsdomän. Flera exempel diskutera den här begränsningen visas i avsnittet på [fel- och begränsningar för domänen och resulterande beteende](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: du normalt inte bör se den här begränsningen ta bort noder från lösningen eftersom den körs som en optimering som standard. Prioriterade platsbegränsningen finns också under uppgraderingar. Under uppgraderingen används för att flytta tjänster till där de fanns när uppgraderingen är igång.

## <a name="blocklisting-nodes"></a>Blocklisting noder
Ett annat hälsotillstånd meddelande Cluster Resource Manager-rapporter är när noderna är blocklisted. Du kan tänka dig blocklisting som en tillfällig begränsning som tillämpas automatiskt åt dig. Noder får blocklisted när de uppstår upprepade fel vid start instanser av den typ av tjänst. Noder är blocklisted på basis av per tjänsttyp. En nod kan vara blocklisted för en tjänsttyp men inte en annan. 

Du ser blocklisting startar ofta under utvecklingen: vissa fel orsakar den tjänstevärden kraschar vid start. Service Fabric försöker skapa tjänstevärden några gånger och felet kvarstår. Efter några försök noden hämtar blocklisted och Cluster Resource Manager försöker att skapa någon annanstans-tjänsten. Om den misslyckade håller händer på flera noder, är det möjligt att alla giltiga noder i klustret hamnar blockeras. Blocklisting kan också ta bort så många noder att finns inte tillräckligt med kan starta tjänsten för att uppfylla önskad skala. Vanligtvis visas ytterligare fel eller varningar från Cluster Resource Manager som anger som tjänsten är under den önskade repliken eller instansantalet, samt hälsotillstånd meddelanden som indikerar felet nyheter som leder till blocklisting i först Placera.

Blocklisting är inte ett permanent villkor. Noden tas bort från den på blockeringslistan och Service Fabric kan aktivera tjänster i den noden igen efter några minuter. Om tjänster fortsätter att misslyckas, är noden blocklisted för den typ av tjänst igen. 

### <a name="constraint-priorities"></a>Begränsningen prioriteringar

> [!WARNING]
> Ändra begränsningen prioriteringar rekommenderas inte och kan ha stor negativ inverkan på ditt kluster. Den informationen nedan har angetts för referens för begränsningen Standardprioriteter och deras beteende. 
>

Med alla de här begränsningarna kan ha tycker du ”Hej – jag tror att fel domän begränsningar är viktigaste i mitt system. För att säkerställa domänbegränsningar fel inte bröt mot kan jag vill bryta mot andra villkor ”.

Begränsningar kan konfigureras med olika prioritet. Dessa är:

   - ”hård” (0)
   - ”Mjuk” (1)
   - ”optimering” (2)
   - ”off” (-1). 
   
De flesta av begränsningarna är som standard konfigurerade som hårda begränsningar.

Det är ovanligt att ändra prioriteten för begränsningar. Det har skett gånger där begränsningen prioriteringar behövs för att ändra, vanligtvis för att kringgå vissa andra fel eller funktion som påverkar miljön. Allmänt flexibiliteten i begränsningen prioritet infrastruktur har arbetat mycket bra, men den behövs inte ofta. I de flesta fall sitter allt vid sin Standardprioriteter. 

Prioritetsnivåer inte innebär att en viss begränsning _kommer_ ha brutits, eller som den ska alltid vara uppfyllda. Begränsningen prioriteringar definierar en order där begränsningar tillämpas. Prioriteter definiera kompromisser när det är omöjligt att räcka åt alla begränsningar. Alla begränsningar kan vanligtvis vara uppfyllda om det inte finns något annat i miljön. Några exempel på scenarier som leder till begränsningen överträdelser är i konflikt begränsningar eller stort antal samtidiga fel.

I avancerade fall kan ändra du begränsningen prioriteringar. Anta exempelvis att du vill se till att tillhörighet skulle alltid ha brutits vid behov för att lösa problem för nod-kapacitet. Du kan ange prioritet för mappning mellan begränsningen till ”mjuk” (1) och lämna den kapacitet för begränsning angiven till ”hård” (0) för att uppnå detta.

Prioritet standardvärden för de olika begränsningarna anges i följande konfiguration:

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

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Begränsningar för fel domän och uppgradera domän
Klusterresurshanteraren vill hålla tjänster sprids ut bland fel- och uppgraderingsdomäner. Modellerar detta som en begränsning i Cluster Resource Manager-motorn. Mer information om hur de används och deras specifika beteende, finns i artikeln på [klusterkonfiguration](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Cluster Resource Manager kan behöva bygga ett par repliker i en uppgraderingsdomän för att kunna hantera uppgraderingar, fel eller andra begränsningen överträdelser. Paketering i fel eller uppgradera domäner normalt inträffar bara när det finns flera fel eller andra omsättning i systemet som förhindrar korrekt placering. Om du vill förhindra förpackning även under sådana situationer kan du använda den `RequireDomainDistribution` [placering princip](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Observera att detta kan påverka tjänstens tillgänglighet och tillförlitlighet som en sidoeffekt, så fundera över den noggrant.

Om miljön är korrekt konfigurerade respekteras alla begränsningar fullständigt, även under uppgraderingar. Huvudsaken är att Cluster Resource Manager bevakar för dina villkor. När den identifierar en överträdelse det omedelbart rapporterar det och försöker åtgärda problemet.

## <a name="the-preferred-location-constraint"></a>Prioriterade platsbegränsningen
PreferredLocation-begränsning är lite annorlunda eftersom den har två olika användningsområden. Användningen av den här begränsningen är under programuppgraderingar. Cluster Resource Manager hanterar automatiskt den här begränsningen under uppgraderingar. Den används för att se till att när uppgraderingar är klara att repliker gå tillbaka till sina ursprungliga platser. Andra PreferredLocation begränsningen används för den [ `PreferredPrimaryDomain` placering princip](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Båda dessa är optimeringar och kan därför PreferredLocation begränsningen är den enda begränsningen inställd på ”optimering” som standard.

## <a name="upgrades"></a>Uppgraderingar
Cluster Resource Manager hjälper även under program- och klusteruppgradering, då den har två jobb:

* Se till att reglerna för klustret inte komprometteras
* Försök att uppgraderingen går smidigt

### <a name="keep-enforcing-the-rules"></a>Behåll tvingande regler
Det viktigaste du ska tänka på är att tillämpas reglerna – strikt begränsningarna som placeringsbegränsningar och kapaciteter - fortfarande under uppgraderingar. Placeringsbegränsningar se till att dina arbetsbelastningar endast köras var de ska kunna, även under uppgraderingar. När tjänster är mycket begränsad, kan uppgraderingen ta längre tid. När tjänsten eller den körs på noden är online för en uppdatering kan det finnas flera alternativ för där det går.

### <a name="smart-replacements"></a>Smart ersättningar
När en uppgradering startar tar en ögonblicksbild av den aktuella ordningen på klustret i Resource Manager. Eftersom varje Uppgraderingsdomänen är klar försöker denna att returnera de tjänster som fanns i Uppgraderingsdomänen till deras ursprungliga placering. Det här sättet finns högst två övergångar för en tjänst under uppgraderingen. Det finns en flytta bort från den berörda noden och en flytta tillbaka. Returnera det kluster eller en tjänst till den såg ut före uppgraderingen innebär också uppgraderingen inte påverkar layouten för klustret. 

### <a name="reduced-churn"></a>Minskad omsättning
En annan sak som sker under uppgraderingar är att Cluster Resource Manager-stänger av belastningsutjämning. Hindrar belastningsutjämning förhindrar onödig reaktioner för uppgradering, t.ex. Flytta tjänster till noder som har töms för uppgraderingen. Om uppgraderingen i fråga är en uppgradering av klustret, belastningsutjämnad hela klustret inte under uppgraderingen. Begränsningen kontroller förblir aktiva, endast transport baserat på proaktiv fördelningen av mått är inaktiverat.

### <a name="buffered-capacity--upgrade"></a>Buffrade kapacitet och uppgradering
Allmänt vill du att uppgraderingen ska slutföra även om klustret är begränsad eller nära fullständig. Hantera kapacitet i klustret är ännu viktigare under uppgraderingar än vanligt. Beroende på antalet uppgraderingsdomäner, måste mellan 5 och 20 procent av kapaciteten som migreras uppgraderingen går igenom i klustret. Arbetet har gå någonstans. Det är där begreppet [buffras kapaciteter](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) är användbart. Buffrade kapacitet respekteras under normal drift. Cluster Resource Manager kan fylla noder upp till deras total kapacitet (förbrukar bufferten) under uppgraderingar om det behövs.

## <a name="next-steps"></a>Nästa steg
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

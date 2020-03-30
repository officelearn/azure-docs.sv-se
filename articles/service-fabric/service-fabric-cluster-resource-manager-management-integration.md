---
title: Klusterresurshanteraren - Hanteringsintegrering
description: En översikt över integrationspunkterna mellan Cluster Resource Manager och Service Fabric Management.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258751"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrering av klusterresurshanteraren med klusterhantering för Service Fabric
Service Fabric Cluster Resource Manager kör inte uppgraderingar i Service Fabric, men det är involverat. Det första sättet som Cluster Resource Manager hjälper till med hanteringen är genom att spåra önskat tillstånd för klustret och tjänsterna i det. Klusterresurshanteraren skickar ut hälsorapporter när det inte kan placera klustret i önskad konfiguration. Om det till exempel inte finns tillräcklig kapacitet skickar Cluster Resource Manager ut hälsovarningar och fel som anger problemet. En annan del av integrationen har att göra med hur uppgraderingar fungerar. Klusterresurshanteraren ändrar sitt beteende något under uppgraderingar.  

## <a name="health-integration"></a>Integrering av hälsa
Klusterresurshanteraren spårar ständigt de regler som du har definierat för att placera dina tjänster. Den spårar också den återstående kapaciteten för varje mått på noderna och i klustret och i klustret som helhet. Om den inte kan uppfylla dessa regler eller om det inte finns tillräcklig kapacitet, hälsovarningar och fel avges. Om en nod till exempel är över kapacitet och Klusterresurshanteraren försöker åtgärda situationen genom att flytta tjänster. Om den inte kan korrigera situationen avger den en hälsovarning som anger vilken nod som är över kapacitet och för vilka mått.

Ett annat exempel på Resurshanterarens hälsovarningar är överträdelser av placeringsbegränsningar. Om du till exempel har definierat `“NodeColor == Blue”`ett placeringsvillkor (till exempel ) och Resurshanteraren upptäcker ett brott mot den begränsningen, avger den en hälsovarning. Detta gäller för anpassade begränsningar och standardbegränsningar (till exempel begränsningarna feldomän och uppgraderingsdomän).

Här är ett exempel på en sådan hälsorapport. I det här fallet är hälsorapporten för en av systemtjänstens partitioner. Hälsomeddelandet anger att replikerna för den partitionen är tillfälligt packade i för få uppgraderingsdomäner.

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

Här är vad detta hälsomeddelande säger oss är:

1. Alla repliker själva är friska: Var och en har AggregatedHealthState: Ok
2. Distributionsbegränsningen för uppgraderingsdomänen bryts för närvarande. Det innebär att en viss uppgraderingsdomän har fler repliker från den här partitionen än den borde.
3. Vilken nod innehåller repliken som orsakar överträdelsen. I det här fallet är det noden med namnet "Nod.8"
4. Om en uppgradering för närvarande sker för den här partitionen ("Uppgradering för närvarande – falskt")
5. Distributionsprincipen för den här tjänsten: "Distributionspolicy -- Packning". Detta styrs `RequireDomainDistribution` av [placeringsprincipen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Packning" anger att domändistribution i det här fallet _inte_ behövdes, så vi vet att placeringsprincipen inte angavs för den här tjänsten. 
6. När rapporten hände - 2015-08-10 19:13:02

Information som denna befogenheter varnar för att brand i produktionen så att du vet att något har gått fel och används också för att upptäcka och stoppa dåliga uppgraderingar. I det här fallet vill vi se om vi kan ta reda på varför Resource Manager var tvungen att packa replikerna i uppgraderingsdomänen. Vanligtvis packning är övergående eftersom noderna i de andra uppgraderingsdomänerna var nere, till exempel.

Anta att Cluster Resource Manager försöker placera vissa tjänster, men det finns inga lösningar som fungerar. När tjänster inte kan placeras är det oftast av något av följande skäl:

1. Vissa tillfälliga tillstånd har gjort det omöjligt att placera denna tjänstinstans eller replik korrekt
2. Tjänstens placeringskrav är otåliga.

I dessa fall hjälper hälsorapporter från Cluster Resource Manager dig att avgöra varför tjänsten inte kan placeras. Vi kallar den här processen för begränsningselimineringssekvensen. Under den går systemet igenom de konfigurerade begränsningar som påverkar tjänsten och registrerar vad de eliminerar. På så sätt när tjänster inte kan placeras kan du se vilka noder som har tagits bort och varför.

## <a name="constraint-types"></a>Villkorstyper
Låt oss tala om var och en av de olika begränsningarna i dessa hälsorapporter. Hälsomeddelanden som är relaterade till dessa begränsningar visas när repliker inte kan placeras.

* **ReplicaExclusionStatic** och **ReplicaExclusionDynamic**: Dessa begränsningar anger att en lösning avvisades eftersom två tjänstobjekt från samma partition måste placeras på samma nod. Detta är inte tillåtet eftersom då fel på den noden skulle alltför påverka den partitionen. ReplicaExclusionStatic och ReplicaExclusionDynamic är nästan samma regel och skillnaderna spelar egentligen ingen roll. Om du ser en begränsningselimineringssekvens som innehåller begränsningen ReplicaExclusionStatic eller ReplicaExclusionDynamic, anser Klusterresurshanteraren att det inte finns tillräckligt många noder. Detta kräver återstående lösningar för att använda dessa ogiltiga placeringar som inte är till för att tillåtas. De andra begränsningarna i sekvensen talar vanligtvis om för oss varför noder elimineras från början.
* **PlacementConstraint**: Om du ser det här meddelandet betyder det att vi har tagit bort vissa noder eftersom de inte matchade tjänstens placeringsbegränsningar. Vi spårar de för närvarande konfigurerade placeringsbegränsningarna som en del av det här meddelandet. Detta är normalt om du har definierat ett placeringsvillkor. Men om placeringsvillkor felaktigt orsakar för många noder som ska elimineras är det så här du skulle märka det.
* **NodeCapacity**: Det här villkoret innebär att klusterresurshanteraren inte kunde placera replikerna på de angivna noderna eftersom det skulle placera dem över kapaciteten.
* **Tillhörighet:** Den här begränsningen anger att vi inte kunde placera repliken på de berörda noderna eftersom det skulle orsaka ett brott mot tillhörighetsbegränsningen. Mer information om tillhörighet finns i [den här artikeln](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** och **UpgradeDomain**: Den här begränsningen eliminerar noder om placeringen av repliken på de angivna noderna skulle orsaka packning i en viss fel- eller uppgraderingsdomän. Flera exempel som diskuterar denna begränsning presenteras i ämnet om [fel och uppgradera domänbegränsningar och resulterande beteende](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Du bör normalt inte se den här begränsningen ta bort noder från lösningen eftersom den körs som en optimering som standard. Det önskade platsvillkoret finns också under uppgraderingar. Under uppgraderingen används den för att flytta tillbaka tjänster till den plats där de var när uppgraderingen startade.

## <a name="blocklisting-nodes"></a>Blockera teckningsnoder
Ett annat hälsomeddelande som Cluster Resource Manager rapporterar är när noder blockeras. Du kan se blockering som ett tillfälligt villkor som tillämpas automatiskt för dig. Noder blockeras när de upplever upprepade fel när instanser av den tjänsttypen startas. Noder blockeras per tjänst-typ. En nod kan blockeras för en tjänsttyp men inte en annan. 

Du ser blockering kick in ofta under utvecklingen: vissa bugg orsakar din tjänst värd att krascha vid start. Service Fabric försöker skapa tjänstvärden några gånger och felet fortsätter att inträffa. Efter några försök blockeras noden och Klusterresurshanteraren försöker skapa tjänsten någon annanstans. Om det felet fortsätter att inträffa på flera noder är det möjligt att alla giltiga noder i klustret blockeras. Blockering kan också ta bort så många noder att inte tillräckligt kan starta tjänsten för att uppfylla önskad skala. Du ser vanligtvis ytterligare fel eller varningar från Cluster Resource Manager som anger att tjänsten ligger under det önskade antalet repliker eller instanser, samt hälsomeddelanden som anger vad felet är som leder till blockeringen i den första Plats.

Blockering är inte ett permanent villkor. Efter några minuter tas noden bort från blockeringslistan och Service Fabric kan aktivera tjänsterna på den noden igen. Om tjänsterna fortsätter att misslyckas blockeras noden för den tjänsttypen igen. 

### <a name="constraint-priorities"></a>Prioriteringar för begränsning

> [!WARNING]
> Det rekommenderas inte att ändra villkorsprioriteringar och kan ha betydande negativa effekter på klustret. Nedanstående information tillhandahålls som referens för standardbegränsningsprioriteringarna och deras beteende. 
>

Med alla dessa begränsningar, kanske du har tänkt "Hej - Jag tror att fel domän begränsningar är det viktigaste i mitt system. För att säkerställa att feldomänbegränsningen inte bryts, är jag villig att bryta mot andra begränsningar."

Begränsningar kan konfigureras med olika prioritetsnivåer. Dessa är:

   - "hårt" (0)
   - "mjuk" (1)
   - "optimering" (2)
   - "av" (-1). 
   
De flesta begränsningarna är som standard konfigurerade som hårda begränsningar.

Det är ovanligt att du ändrar prioriteten för begränsningar. Det har funnits tillfällen då begränsning prioriteringar som behövs för att ändra, oftast för att komma runt några andra fel eller beteende som påverkade miljön. Generellt flexibiliteten i begränsningen prioritet infrastruktur har fungerat mycket bra, men det behövs inte ofta. För det mesta allt sitter på deras standard prioriteringar. 

Prioritetsnivåerna betyder inte att en viss begränsning _kommer att_ brytas, eller att den alltid kommer att uppfyllas. Begränsningsprioriteringar definierar en ordning i vilken begränsningar tillämpas. Prioriteringar definierar kompromisser när det är omöjligt att uppfylla alla begränsningar. Vanligtvis alla begränsningar kan uppfyllas om det inte finns något annat som händer i miljön. Några exempel på scenarier som leder till begränsningsöverträdelser är motstridiga begränsningar eller ett stort antal samtidiga fel.

I avancerade situationer kan du ändra begränsningsprioriteringarna. Anta till exempel att du vill se till att tillhörighet alltid skulle brytas när det behövs för att lösa problem med nodkapacitet. För att uppnå detta kan du ställa in prioriteten för tillhörighetsbegränsningen till "mjuk" (1) och lämna kapacitetsbegränsningen inställd på "hård" (0).

Standardprioritetvärdena för de olika begränsningarna anges i följande konfiguration:

KlusterManifest.xml

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

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Domänbegränsningar för feldomäner och uppgradering
Klusterresurshanteraren vill hålla tjänsterna utspridda mellan fel- och uppgraderingsdomäner. Detta modelleras som ett villkor i Cluster Resource Manager-motorn. Mer information om hur de används och deras specifika beteende finns i artikeln om [klusterkonfiguration](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Klusterresurshanteraren kan behöva packa ett par repliker i en uppgraderingsdomän för att hantera uppgraderingar, fel eller andra begränsningsöverträdelser. Packning i fel- eller uppgraderingsdomäner sker normalt endast när det finns flera fel eller annan omsättning i systemet som förhindrar korrekt placering. Om du vill förhindra packning även under dessa `RequireDomainDistribution` situationer, kan du använda [placeringsprincipen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Observera att detta kan påverka tjänstens tillgänglighet och tillförlitlighet som en bieffekt, så tänk noga.

Om miljön är korrekt konfigurerad respekteras alla begränsningar fullt ut, även vid uppgraderingar. Det viktigaste är att Cluster Resource Manager håller utkik efter dina begränsningar. När den upptäcker en överträdelse den omedelbart rapporterar den och försöker rätta till problemet.

## <a name="the-preferred-location-constraint"></a>Önskat platsvillkor
PreferredLocation-begränsningen är lite annorlunda, eftersom den har två olika användningsområden. En användning av den här begränsningen är under programuppgraderingar. Klusterresurshanteraren hanterar automatiskt den här begränsningen under uppgraderingar. Det används för att säkerställa att när uppgraderingar är klara att repliker återgå till sina första platser. Den andra användningen av PreferredLocation-begränsningen [ `PreferredPrimaryDomain` ](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)är för placeringsprincipen . Båda dessa är optimeringar, och därför är PreferredLocation-begränsningen den enda begränsningen inställd på "Optimering" som standard.

## <a name="upgrades"></a>Uppgraderingar
Klusterresurshanteraren hjälper också till under program- och klusteruppgraderingar, under vilka den har två jobb:

* se till att klustrets regler inte äventyras
* försök att hjälpa uppgraderingen att gå smidigt

### <a name="keep-enforcing-the-rules"></a>Fortsätt att tillämpa reglerna
Det viktigaste att vara medveten om är att reglerna - de strikta begränsningar som placering begränsningar och kapacitet - fortfarande tillämpas under uppgraderingar. Placeringsbegränsningar säkerställer att dina arbetsbelastningar endast körs där de tillåts, även under uppgraderingar. När tjänsterna är mycket begränsade kan uppgraderingar ta längre tid. När tjänsten eller noden som körs på tas ned för en uppdatering kan det finnas få alternativ för vart den kan gå.

### <a name="smart-replacements"></a>Smarta ersättare
När en uppgradering startar tar Resource Manager en ögonblicksbild av den aktuella ordningen för klustret. När varje uppgraderingsdomän är klar försöker den returnera de tjänster som fanns i uppgraderingsdomänen till deras ursprungliga ordning. På så sätt finns det högst två övergångar för en tjänst under uppgraderingen. Det finns ett drag ut ur den drabbade noden och en flytta tillbaka in. Att återställa klustret eller tjänsten till hur det var innan uppgraderingen också säkerställer att uppgraderingen inte påverkar klustrets layout. 

### <a name="reduced-churn"></a>Minskad omsättning
En annan sak som händer under uppgraderingar är att Cluster Resource Manager inaktiverar balansering. Om du förhindrar balansering förhindrar du onödiga reaktioner på själva uppgraderingen, som att flytta tjänster till noder som tömdes för uppgraderingen. Om uppgraderingen i fråga är en klusteruppgradering balanseras inte hela klustret under uppgraderingen. Begränsningskontroller förblir aktiva, endast förflyttning baserat på proaktiv avvägning av mått är inaktiverad.

### <a name="buffered-capacity--upgrade"></a>Uppgradering av buffrad kapacitet &
I allmänhet vill du att uppgraderingen ska slutföras även om klustret är begränsat eller nära fullt. Hantera kapaciteten i klustret är ännu viktigare under uppgraderingar än vanligt. Beroende på antalet uppgraderingsdomäner måste mellan 5 och 20 procent av kapaciteten migreras när uppgraderingen rullar genom klustret. Det arbetet måste gå någonstans. Det är här begreppet [buffrad kapacitet](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) är användbar. Buffrad kapacitet respekteras under normal drift. Klusterresurshanteraren kan fylla noder upp till sin totala kapacitet (förbruka bufferten) under uppgraderingar om det behövs.

## <a name="next-steps"></a>Nästa steg
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

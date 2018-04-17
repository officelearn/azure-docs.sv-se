---
title: Azure Service Fabric-kluster och skalning | Microsoft Docs
description: Lär dig mer om att skala Service Fabric-kluster i eller ut och uppåt eller nedåt.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 897370e48b4eb0db5c99c1102910c89f6f56cc23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="scaling-service-fabric-clusters"></a>Skala Service Fabric-kluster
Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som din mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurser noder).  Du kan skala klustret när som helst, även om arbetsbelastningar som körs på klustret.  När klustret skalas skalas dina program automatiskt samt.

Varför skala klustret? Programbegäran ändras med tiden.  Du kan behöva öka klusterresurser att uppfylla ökade programmet serverbelastning eller nätverkstrafik eller minska klusterresurser när status för begäran.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller teckenbredden
Ändrar antalet noder i klustret.  När nya noder ansluta till klustret i [klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md) flyttar tjänster till dem som minskar belastningen på de befintliga noderna.  Du kan också minska antalet noder om klustrets resurser inte används effektivt.  Noder lämnar klustret, tjänster flytta utanför de noderna och belastningen ökar på övriga noder.  Minska antalet noder i ett kluster som körs i Azure kan du spara pengar, eftersom du betalar för antal virtuella datorer du användning och inte arbetsbelastningen på dessa virtuella datorer.  

- Fördelar: Oändlig skala, i teorin.  Om programmet har utformats för skalbarhet, kan du aktivera Obegränsad tillväxt genom att lägga till fler noder.  Verktygsuppsättning i molnmiljöer gör det enkelt att lägga till eller ta bort noder, så det är lätt att justera kapaciteten och du betalar bara för de resurser du använder.  
- Nackdelar: Program måste vara [utformats för skalbarhet och](service-fabric-concepts-scalability.md).  Databaser och beständiga kan kräva ytterligare arkitektur arbete att skala samt.  [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillståndskänsliga tjänster, men gör det enklare att skala dina programdata.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ner eller lodräta skalning 
Ändrar resurser (CPU, minne eller lagring) av noder i klustret.
- Fördelar: Programvara och programarkitektur förblir densamma.
- Nackdelar: Begränsad skala, eftersom det inte finns en gräns för hur mycket du kan öka resurser på enskilda noder. Driftstopp, eftersom du behöver göra fysiska eller virtuella datorer offline för att lägga till eller ta bort resurser.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Skala ett Azure-kluster in eller ut
Skaluppsättningar för den virtuella datorn är en Azure compute-resurs som du kan använda för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [har angetts som en separat skaluppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan skalas i ut oberoende av varandra, har olika uppsättningar av öppna portar och kan ha olika kapacitetsdata. 

Tänk på följande riktlinjer när skalning ett Azure-kluster:
- primär nodtyper kör produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
- icke-primär nodtyper kör tillståndskänslig produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
- icke-primär nodtyper kör tillståndslös produktionsarbetsbelastningar bör alltid ha två eller flera noder.
- Varje nodtyp av [hållbarhet nivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) guld eller Silver bör alltid ha fem eller fler noder.
- Inte ta bort slumpmässiga VM-instanser noder från en nodtyp, alltid använda virtual machine scale set skala ned funktionen. Borttagning av slumpmässiga VM-instanser kan påverkas negativt system kunna belastningsutjämna korrekt.
- Om du använder Autoskala regler, ange regler så att skala (att ta bort VM-instanser) görs en nod i taget. Det är inte säkert att skala ned mer än en instans i taget.

Eftersom typer för Service Fabric-nod i klustret består av virtuella datorn anger i serverdelen, kan du [ställa in automatisk skalning regler eller skala manuellt](service-fabric-cluster-scale-up-down.md) varje nod typ för virtuella datorns skaluppsättning.

### <a name="programmatic-scaling"></a>Programmässiga skalning
I många fall är [skala ett kluster manuellt eller med Autoskala regler](service-fabric-cluster-scale-up-down.md) är bra lösningar. För mer avancerade scenarier, men kanske de inte rätt storlek. Eventuella nackdelar med dessa metoder är:

- Skalning manuellt måste du logga in och uttryckligen begära skalning åtgärder. Om skalning åtgärder krävs ofta eller vid oväntade tidpunkter, kanske inte en bra lösning i den här metoden.
- När Autoskala regler för att ta bort en instans från en skaluppsättning för virtuell dator, de inte bort automatiskt kunskap om noden från det associera Service Fabric-klustret såvida inte nodtypen har en hållbarhet Silver eller guld. Eftersom Autoskala regler fungerar i skala anger (istället för på Service Fabric-nivå), Autoskala regler kan ta bort Service Fabric-noder utan att dem avslutas. Borttagningen oartigt nod lämnar 'ghost-tillstånd för Service Fabric-noden efter efter skala i operations. En person (eller en tjänst) skulle behöva regelbundet Rensa borttagna noden tillstånd i Service Fabric-klustret.
- En nodtyp med hållbarhet guld eller Silver rensas automatiskt bort noder, så det behövs ingen ytterligare rensning.
- Även om det finns [många mått](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) stöds av Autoskala regler, är det fortfarande en begränsad uppsättning. Om ditt scenario anrop för att skala baserat på vissa mått som inte omfattas i uppsättningen kan sedan kanske Autoskala regler inte ett bra alternativ.

Hur ska du bör Service Fabric skalning beror på ditt scenario. Om det är ovanligt att skalning, räcker troligen möjligheten att lägga till eller ta bort noder manuellt. För mer komplicerade scenarier erbjuder Autoskala regler och SDK exponera möjlighet att skala programmässigt kraftfulla alternativ.

Azure API: er finns som tillåter program programmässigt arbetar med virtuella skala uppsättningar och Service Fabric-kluster. Om den befintliga Autoskala alternativ inte fungerar för ditt scenario, gör dessa API: er det möjligt att implementera anpassade skalning logik. 

En metod för att implementera funktionen ”home tillverkade” automatisk skalning är att lägga till en ny tillståndslösa tjänsten Service Fabric-programmet för att hantera skalning åtgärder. Skapa en egen skalning service ger största möjliga kontroll och anpassningsbarheten över ditt program är skalning beteende. Detta kan vara användbart för scenarier som kräver exakt kontroll över när eller hur ett program skalas in eller ut. Den här kontrollen har dock en kompromiss förenklar koden. Med den här metoden innebär att du behöver egna skalning kod, som är icke-trivial. I tjänstens `RunAsync` metod, en uppsättning utlösare kan avgöra om skalning krävs (inklusive kontrollerar parametrar, till exempel klustrets maximala storlek och skala cooldowns).   

API: et för virtuella scale set interaktioner (både att kontrollera det aktuella antalet instanser för virtuella datorer och ändra den) är den [flytande Azure Management Compute biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Flytande beräknings-bibliotek innehåller en lätt att använda API för att interagera med virtuella datorer.  Om du vill interagera med själva Service Fabric-klustret använder [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Skalning koden behöver inte köras som en tjänst i klustret för att skalas, även om. Båda `IAzure` och `FabricClient` kan fjärransluta till deras associerade Azure-resurser, så att tjänsten skalning enkelt kan vara ett konsolprogram eller Windows-tjänsten körs från utanför Service Fabric-programmet.

Baserat på dessa begränsningar, kan du [implementera anpassade mer automatisk skalning modeller](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Skala ett fristående kluster in eller ut
Fristående kluster kan du distribuera Service Fabric-kluster lokalt eller i molntjänstleverantör du väljer.  Nodtyper består av fysiska datorer eller virtuella datorer, beroende på din distribution. Jämfört med kluster som körs i Azure är för skalning ett fristående kluster lite mer komplicerad.  Du måste manuellt ändra antalet noder i klustret och sedan köra en uppgradering för konfiguration av klustret.

Borttagning av noder kan starta flera uppgraderingar. Vissa noder har markerats med `IsSeedNode=”true”` tagga och kan identifieras genom att fråga klustret manifest med [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Borttagning av dessa noder kan ta längre tid än andra eftersom seed-noder måste flyttas i dessa scenarier. Klustret måste upprätthålla minst tre primära noden typen noder.

Tänk på följande riktlinjer när skalning ett fristående kluster:
- Ersättning av primära noder måste vara utförs en nod efter en annan, i stället för att ta bort och sedan lägga till i batchar.
- Kontrollera om det finns några noder som refererar till nodtypen innan du tar bort en nodtyp. Ta bort dessa noder innan du tar bort motsvarande nodtypen. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och börja en konfiguration uppgradera med hjälp av [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Mer information finns i [skala ett fristående kluster](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Skala ett Azure-kluster uppåt eller nedåt
Skaluppsättningar för den virtuella datorn är en Azure compute-resurs som du kan använda för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [har angetts som en separat skaluppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat.  Skala en nodtyp uppåt eller nedåt innebär att ändra SKU av de virtuella datorinstanserna i skaluppsättning. 

> [!WARNING]
> Vi rekommenderar att du inte ändrar den SKU VM scale set/typen om det körs på [Silver hållbarhet eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra storlek på VM-SKU är skadliga data på plats-infrastruktur. Det är möjligt att det kan leda till dataförlust tillståndskänsliga tjänster eller orsaka andra oförutsedda driftsproblem, även för tillståndslösa arbetsbelastningar utan några möjlighet att fördröja eller övervaka den här ändringen. 
>

Tänk på följande riktlinjer när skalning ett Azure-kluster:
- Om skalning av primära nodtypen du bör aldrig skala det ned mer än vad den [tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) tillåter.

Processen för att skala en nodtyp uppåt eller nedåt är olika beroende på om det är en icke-primär eller primära nodtypen.

### <a name="scaling-non-primary-node-types"></a>Skalning icke-primär nodtyper
Skapa en ny nodtyp med de resurser du behöver.  Uppdatera placeringen körs tjänster att inkludera den nya noden.  Gradvis (en i taget), minska instansantal av gamla nod typen instansantalet noll så att klustret tillförlitlighet inte påverkas.  Tjänster migreras gradvis till vilken nod som den gamla nodtypen decommisioned.

### <a name="scaling-the-primary-node-type"></a>Skala den primära nodtypen
Vi rekommenderar att du inte ändrar VM SKU av den primära nodtypen. Om du behöver mer kapacitet för klustret, rekommenderar vi att lägga till flera instanser. 

Om inte kan du skapa ett nytt kluster och [Återställ programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från ditt gamla kluster. Du behöver inte återställa alla service systemtillståndet kan de återskapas när du distribuerar ditt program till det nya klustret. Om du bara tillståndslösa program som körs på klustret och sedan behöver du bara distribuera ditt program till det nya klustret, du har inte något att återställa. Om du vill gå stöds inte vägen och vill ändra VM SKU ange sedan gör ändringar till virtuella datorns skaluppsättning Model definition så att den återger den nya SKU: N. Om klustret har endast en nodtyp, kontrollerar du att alla tillståndskänsliga program svarar på alla [tjänsten replik Livscykelhändelser](service-fabric-reliable-services-lifecycle.md) (t.ex. replik i build har fastnat) i rimlig tid och att tjänsten repliken återskapa varaktighet är mindre än fem minuter (Silver hållbarhet nivå). 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [programmet skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med flytande Azure compute SDK.
* [Skala ett standaone kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


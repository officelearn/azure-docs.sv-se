---
title: Azure Service Fabric-kluster och skalning | Microsoft Docs
description: Lär dig mer om skalning av Azure Service Fabric-kluster i eller ut och upp eller ned.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: 527ab4ee0edaf3ac2048403d7063edef8fc58ae8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451985"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skala Azure Service Fabric-kluster
Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster, kan du skala klustret horisontellt (ändra antalet noder) eller lodrätt (ändra resurser noder).  Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.  När klustret skalas skalas programmen automatiskt samt.

Varför skala klustret? Programbegäran ändras med tiden.  Du kan behöva öka klusterresurser för att uppfylla ökade programtrafik för arbetsbelastning eller ditt nätverk eller minska klusterresurser när behovet sjunker.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller horisontell skalning
Ändrar antalet noder i klustret.  När de nya noderna ansluta till klustret, den [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) flyttar tjänster till dem som minskar belastningen på de befintliga noderna.  Du kan också minska antalet noder om klustrets resurser inte som används effektivt.  Eftersom noder lämna klustret, tjänster flytta utanför dessa noder och belastningen ökar på övriga noder.  Minska antalet noder i ett kluster som körs i Azure kan du spara pengar, eftersom du betalar för antalet virtuella datorer du användning och inte arbetsbelastningen för dessa virtuella datorer.  

- Fördelar: Oändlig skala, i teorin.  Om programmet har utformats för skalbarhet, kan du aktivera Obegränsad tillväxt genom att lägga till fler noder.  Verktygsuppsättningen i miljöer i molnet gör det enkelt att lägga till eller ta bort noder, så att det är enkelt att justera kapacitet och du betalar bara för de resurser du använder.  
- Nackdelar: Program måste vara [utformats för skalbarhet](service-fabric-concepts-scalability.md).  Databaser och persistence kan kräva ytterligare arkitektoniska arbete att skala samt.  [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillståndskänsliga tjänster, men gör det mycket enklare att skala dina programdata.

Virtual machine scale sets är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [ställa in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp skalas sedan in eller ut oberoende av varandra, ha olika portar öppna och ha olika kapacitet. 

Tänk på följande riktlinjer när du skalar en Azure-kluster:
- primär nodtyper som kör produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
- icke-primär nodtyper tillståndskänsliga produktionsarbetsbelastningar körs bör alltid ha fem eller fler noder.
- icke-primär nodtyper tillståndslösa produktionsarbetsbelastningar körs bör alltid ha två eller flera noder.
- Alla nod slags [hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) guld eller Silver alltid ska ha fem eller fler noder.
- Inte ta bort slumpmässiga VM-instanser/noder från en nodtyp, alltid använda VM scale set skala ned funktionen. Borttagning av slumpmässiga VM-instanser kan negativt påverka system möjligheten att belastningsutjämna korrekt.
- Om du använder regler för automatisk skalning, ange reglerna så att skala i (ta bort VM-instanser) görs en nod i taget. Det är inte säkert att skala ned fler än en instans i taget.

Eftersom Service Fabric-nodtyper i klustret består av VM-skalningsuppsättningar i serverdelen, kan du [konfigurera regler för automatisk skalning eller manuellt skala](service-fabric-cluster-scale-up-down.md) varje nod typ/virtual machine scale Sets.

### <a name="programmatic-scaling"></a>Programmässig skalning
I många fall är [skala ett kluster manuellt eller med regler för automatisk skalning](service-fabric-cluster-scale-up-down.md) är bra lösningar. För mer avancerade scenarier, men kan de inte vara rätt passning. Eventuella nackdelar med dessa metoder är:

- Skala manuellt måste du logga in och uttryckligen begära skalningsåtgärder. Om skalningsåtgärder krävs ofta eller vid oväntade tidpunkter, kanske inte en bra lösning i den här metoden.
- När regler för automatisk skalning tar bort en instans från en skalningsuppsättning för virtuell dator kan de inte bort automatiskt kunskaper om noden från det associera Service Fabric-klustret om inte nodtyp har en tillförlitlighet nivå av Silver eller Gold. Eftersom regler för automatisk skalning fungerar i den skala som anger (i stället för på Service Fabric-nivå), regler för automatisk skalning kan ta bort Service Fabric-noder utan att stänga av dem på ett smidigt sätt. Borttagningen oartigt nod lämnar 'ghost-tillstånd för Service Fabric-noden efter efter att skala in operations. En person (eller en tjänst) behöver du regelbundet Rensa borttagna noden tillstånd i Service Fabric-klustret.
- En nodtyp med en hållbarhetsnivå guld eller Silver rensas automatiskt borttagna noder, så det behövs inga ytterligare Rensa.
- Även om det finns [många mått](../azure-monitor/platform/autoscale-common-metrics.md) stöds av regler för automatisk skalning, det är fortfarande en begränsad uppsättning. Om ditt scenario anrop för att skala baserat på vissa mått som inte omfattas i uppsättningen kan sedan kanske regler för automatisk skalning inte ett bra alternativ.

Hur ska du itu med Service Fabric skalning beror på ditt scenario. Om det är ovanligt att skala, räcker troligen möjligheten att lägga till eller ta bort noder manuellt. Erbjuder kraftfulla alternativ för mer komplicerade scenarier kan regler för automatisk skalning och SDK: er som exponerar möjligheten att skala programmässigt.

Azure API: er finns som tillåter program att arbeta programmässigt med VM-skalningsuppsättningar och Service Fabric-kluster. Om befintliga alternativ för automatisk skalning inte fungerar för ditt scenario, gör du möjligt att implementera anpassad logik på skalning med hjälp av dessa API: er. 

En metod för att implementera funktionen ”home-tillverkade” automatisk skalning är att lägga till en ny tillståndslös tjänst i Service Fabric-program för att hantera skalningsåtgärder. Skapa din egen skala tjänsten ger högsta grad av kontroll och anpassningsbarhet över ditt program är skalning beteende. Detta kan vara användbart för scenarier som kräver fullständig kontroll över när eller hur ett program kan skalas in eller ut. Den här kontrollen innehåller dock en kompromiss med kod komplexitet. Med den här metoden innebär att du behöver egna skalning kod, som är icke-trivialt. Inom tjänstens `RunAsync` metod, en uppsättning utlösare kan avgöra om skalning krävs (inklusive kontrollerar parametrar, till exempel klustrets maximala storlek och skala cooldowns).   

Det API som används för VM scale set interaktioner (både att kontrollera det aktuella antalet instanser av virtuella datorer och ändra den) är den [fluent Azure Management Compute biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Fluent beräknings-biblioteket erbjuder en enkel att använda API för att interagera med VM-skalningsuppsättningar.  För att interagera med själva Service Fabric-klustret, använda [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Skalning koden behöver inte köra som en tjänst i klustret för att skalas, men. Båda `IAzure` och `FabricClient` kan fjärransluta till deras associerade Azure-resurser, så att tjänsten skalning kan enkelt ett konsolprogram eller Windows-tjänst som körs från utanför Service Fabric-program.

Baserat på dessa begränsningar, kan du [implementera mer anpassade modeller för automatisk skalning](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ner eller vertikal skalning 
Ändrar resurser (CPU, minne eller lagring) av noder i klustret.
- Fördelar: Arkitektur för programvara och programmet förblir densamma.
- Nackdelar: Begränsad skala, eftersom det inte finns en gräns för hur mycket du kan öka resurser på enskilda noder. Driftstopp, eftersom du behöver vidta fysiska eller virtuella datorer offline för att lägga till eller ta bort resurser.

Virtual machine scale sets är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [ställa in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat.  Skala en nodtyp upp eller ned innebär att du ändrar SKU: N för de virtuella datorinstanserna i skalningsuppsättningen. 

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM-SKU på en skala set/nodtyp, såvida inte den körs på [Silver hållbarhet med eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra VM SKU-storlek är en data-destruktiv plats infrastruktur-åtgärd. Det är möjligt att det kan leda till dataförlust för tillståndskänsliga tjänster eller orsakar andra oförutsedda driftsproblem, även för tillståndslösa arbetsbelastningar utan några möjligheten att fördröja eller övervaka den här ändringen. 
>

När du skalar en Azure-kluster, ha följande riktlinjer i åtanke:
- Om ned primära nodtypen du bör aldrig Nedskalning den mer än vad den [tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) tillåter.

Skala en nodtyp upp eller ned skiljer sig beroende på om det är en icke-primär eller primära nodtypen.

### <a name="scaling-non-primary-node-types"></a>Skalning av icke-primär nodtyper
Skapa en ny nodtyp med de resurser du behöver.  Uppdatera placeringsbegränsningar för att köra tjänster att inkludera den nya noden.  Gradvis (en i taget) minska instansantalet för den gamla nodtyper instans till noll så att tillförlitligheten för klustret inte påverkas.  Tjänster migreras gradvis till den nya nodtypen som den gamla nodtypen är inaktiverad.

### <a name="scaling-the-primary-node-type"></a>Skala den primära nodtypen
Vi rekommenderar att du inte ändrar VM-SKU på den primära nodtypen. Om du behöver mer kapacitet i klustret, rekommenderar vi att lägga till fler instanser. 

Om att inte möjligt, du kan skapa ett nytt kluster och [Återställ programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från ditt gamla kluster. Du behöver inte återställa alla systemtillstånd för tjänsten, de återskapas när du distribuerar ditt program till det nya klustret. Om du bara köra tillståndslösa program i klustret och sedan behöver du bara distribuera program till det nya klustret, du har inget att återställa. Om du vill gå stöds inte vägen och vill ändra VM-SKU, ange sedan gör ändringar i VM-skalningsuppsättningen modell-definitionen för att återspegla den nya SKU: N. Om klustret har endast en nodtyp, kontrollerar du att alla dina tillståndskänsliga program att besvara alla [tjänsten Livscykelhändelser för repliken](service-fabric-reliable-services-lifecycle.md) (t.ex. repliken i bygger har fastnat) i god tid och att din tjänsterepliken återskapa varaktighet är mindre än fem minuter (för Silver hållbarhetsnivå). 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


---
title: Azure Service Fabric kluster skalning
description: Lär dig mer om hur du skalar Azure Service Fabric-kluster in eller ut och upp eller ned. När programkraven ändras, så kan Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258699"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skala Azure Service Fabric-kluster
Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra nodernas resurser).  Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.  När klustret skalas skalas även dina program automatiskt.

Varför skala klustret? Ansökan kräver förändring med tiden.  Du kan behöva öka klusterresurserna för att möta ökad programarbetsbelastning eller nätverkstrafik eller minska klusterresurser när efterfrågan sjunker.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller vågrät skalning
Ändrar antalet noder i klustret.  När de nya noderna har sammanfogat [klustret flyttar Klusterresurshanteraren](service-fabric-cluster-resource-manager-introduction.md) tjänster till dem vilket minskar belastningen på befintliga noder.  Du kan också minska antalet noder om klustrets resurser inte används effektivt.  När noder lämnar klustret flyttas tjänsterna från dessa noder och belastningen ökar på de återstående noderna.  Om du minskar antalet noder i ett kluster som körs i Azure kan du spara pengar, eftersom du betalar för antalet virtuella datorer som du använder och inte arbetsbelastningen på dessa virtuella datorer.  

- Fördelar: Oändlig skala, i teorin.  Om ditt program är utformat för skalbarhet kan du aktivera obegränsad tillväxt genom att lägga till fler noder.  Verktygen i molnmiljöer gör det enkelt att lägga till eller ta bort noder, så det är enkelt att justera kapaciteten och du betalar bara för de resurser du använder.  
- Nackdelar: Program måste [vara utformade för skalbarhet](service-fabric-concepts-scalability.md).  Programdatabaser och persistens kan kräva ytterligare arkitektoniskt arbete för att skala också.  [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillståndskänsliga tjänster gör det dock mycket enklare att skala dina programdata.

Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster [ställs in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan skalas in eller ut oberoende av varandra, ha olika uppsättningar portar öppna och ha olika kapacitetsmått. 

Tänk på följande när du skalar ett Azure-kluster:
- primära nodtyper som kör produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
- Icke-primära nodtyper som kör tillståndskänsliga produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
- Icke-primära nodtyper som kör tillståndslösa produktionsarbetsbelastningar bör alltid ha två eller flera noder.
- Alla nodtyper av [hållbarhetsnivå för](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) guld eller silver bör alltid ha fem eller fler noder.
- Ta inte bort slumpmässiga VM-instanser/noder från en nodtyp, använd alltid den virtuella datorns skala uppsättning skala ner-funktionen. Borttagningen av slumpmässiga VM-instanser kan påverka systemens förmåga att belastningsbalansen på rätt sätt.
- Om du använder regler för automatisk skalning anger du reglerna så att skalning i (ta bort VM-instanser) görs en nod i taget. Det är inte säkert att skala ned mer än en instans i taget.

Eftersom nodtyperna Service Fabric i klustret består av skaluppsättningar för virtuella datorer i serverdelen kan du [ställa in regler för automatisk skalning eller manuellt skala](service-fabric-cluster-scale-up-down.md) varje nodtyp/skaluppsättning för virtuell dator.

### <a name="programmatic-scaling"></a>Programmatisk skalning
I många fall är [skalning av ett kluster manuellt eller med regler för automatisk skalning](service-fabric-cluster-scale-up-down.md) bra lösningar. För mer avancerade scenarier, men de kanske inte är rätt passform. Potentiella nackdelar med dessa metoder är:

- Manuellt skalning kräver att du loggar in och uttryckligen begär skalningsåtgärder. Om skalningsåtgärder krävs ofta eller vid oförutsägbara tidpunkter kanske den här metoden inte är en bra lösning.
- När regler för automatisk skalning tar bort en instans från en skalningsuppsättning för virtuella datorer tar de inte automatiskt bort kunskap om den noden från det associerade Service Fabric-klustret om inte nodtypen har en hållbarhetsnivå för Silver eller Guld. Eftersom regler för automatisk skalning fungerar på skalningsuppsättningsnivå (i stället för på service fabric-nivå) kan regler för automatisk skalning ta bort Service Fabric-noder utan att stänga av dem på ett smidigt sätt. Den här oförskämda nodborttagningen lämnar nodtillståndet "ghost" Service Fabric bakom efter skalningsåtgärder. En enskild (eller en tjänst) måste regelbundet rensa borttagna nodtillstånd i service fabric-klustret.
- En nodtyp med en hållbarhetsnivå av Guld eller Silver rensar automatiskt borttagna noder, så ingen ytterligare rensning behövs.
- Även om det finns [många mått](../azure-monitor/platform/autoscale-common-metrics.md) som stöds av regler för automatisk skalning, är det fortfarande en begränsad uppsättning. Om ditt scenario kräver skalning baserat på vissa mått som inte omfattas av den uppsättningen, kanske regler för automatisk skalning inte är ett bra alternativ.

Hur du ska närma dig skala för Service Fabric beror på ditt scenario. Om skalning är ovanligt är förmodligen möjligheten att lägga till eller ta bort noder manuellt tillräcklig. För mer komplexa scenarier, regler för automatisk skalning och SDK:er som exponerar möjligheten att skala programmässigt erbjuder kraftfulla alternativ.

Azure API:er finns som gör att program kan arbeta program med skalningsuppsättningar för virtuella datorer och Service Fabric-kluster. Om befintliga alternativ för automatisk skalning inte fungerar för ditt scenario gör dessa API:er det möjligt att implementera anpassad skalningslogik. 

En metod för att implementera den här "hemgjorda" automatisk skalningsfunktionen är att lägga till en ny tillståndslös tjänst i Service Fabric-programmet för att hantera skalningsåtgärder. Att skapa din egen skalningstjänst ger högsta möjliga kontroll och anpassningsbarhet över programmets skalningsbeteende. Detta kan vara användbart för scenarier som kräver exakt kontroll över när eller hur ett program skalas in eller ut. Men denna kontroll kommer med en avvägning av koden komplexitet. Med den här metoden innebär att du måste äga skalningskod, vilket inte är trivialt. Inom tjänstens `RunAsync` metod kan en uppsättning utlösare avgöra om skalning krävs (inklusive kontrollparametrar som maximal klusterstorlek och skalning av cooldowns).   

API:et som används för interaktioner med skala för virtuella datorer (både för att kontrollera det aktuella antalet instanser av virtuella datorer och ändra den) är azure [management compute-biblioteket flytande.](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/) Det flytande beräkningsbiblioteket ger ett lättanvänt API för att interagera med skaluppsättningar för virtuella datorer.  Om du vill interagera med själva Service Fabric-klustret använder du [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Skalningskoden behöver dock inte köras som en tjänst i klustret som ska skalas. Både `IAzure` `FabricClient` och kan fjärransluta till sina associerade Azure-resurser, så skalningstjänsten kan enkelt vara ett konsolprogram eller Windows-tjänst som körs utanför Programmet Service Fabric.

Baserat på dessa begränsningar kanske du vill [implementera mer anpassade automatiska skalningsmodeller](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ned eller lodrät skalning 
Ändrar resurser (CPU, minne eller lagring) för noder i klustret.
- Fördelar: Programvaru- och programarkitekturen förblir densamma.
- Nackdelar: Finit skala, eftersom det finns en gräns för hur mycket du kan öka resurserna på enskilda noder. Driftstopp, eftersom du måste koppla från fysiska eller virtuella datorer för att lägga till eller ta bort resurser.

Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster [ställs in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat.  Skalning av en nodtyp uppåt eller nedåt innebär att SKU för instanser av den virtuella datorn ändras i skalningsuppsättningen. 

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM SKU för en skalningsuppsättning/nodtyp om den inte körs vid [Silver hållbarhet eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra VM SKU Storlek är en datadestruktiv på plats infrastruktur drift. Utan viss möjlighet att fördröja eller övervaka den här ändringen är det möjligt att åtgärden kan orsaka dataförlust för tillståndskänsliga tjänster eller orsaka andra oförutsedda driftproblem, även för tillståndslösa arbetsbelastningar. 
>

Tänk på hur du skalar ett Azure-kluster:
- Om du skalar ned en primär nodtyp bör du aldrig skala ned den mer än vad [tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) tillåter.

Processen att skala en nodtyp uppåt eller nedåt är olika beroende på om det är en icke-primär eller primär nodtyp.

### <a name="scaling-non-primary-node-types"></a>Skala icke-primära nodtyper
Skapa en ny nodtyp med de resurser du behöver.  Uppdatera placeringsbegränsningarna för att köra tjänster så att de nya nodtypen inkluderas.  Gradvis (en i taget), minska instansantalet för det gamla nodtypinstansantalet till noll så att klustrets tillförlitlighet inte påverkas.  Tjänster migreras gradvis till den nya nodtypen när den gamla nodtypen inaktiveras.

### <a name="scaling-the-primary-node-type"></a>Skala den primära nodtypen
Vi rekommenderar att du inte ändrar VM SKU för den primära nodtypen. Om du behöver mer klusterkapacitet rekommenderar vi att du lägger till fler instanser. 

Om det inte är möjligt kan du skapa ett nytt kluster och [återställa programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från det gamla klustret. Du behöver inte återställa något systemtjänsttillstånd, de återskapas när du distribuerar dina program till det nya klustret. Om du bara körde tillståndslösa program i klustret, då allt du gör är att distribuera dina program till det nya klustret, har du inget att återställa. Om du bestämmer dig för att gå den som inte stöds vägen och vill ändra VM SKU, sedan göra ändringar i den virtuella datorn skala uppsättning modelldefinition för att återspegla den nya SKU. Om klustret bara har en nodtyp kontrollerar du att alla tillståndskänsliga program svarar på alla livscykelhändelser för [tjänstreplik](service-fabric-reliable-services-lifecycle.md) (som repliken i build har fastnat) i tid och att varaktigheten för återskapning av tjänstrepliken är kortare än fem minuter (för silverhållbarhetsnivå). 

## <a name="next-steps"></a>Nästa steg
* Läs mer om [programskalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av den flytande Azure-beräkningSDK.Scale an Azure cluster programmematically using the fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).


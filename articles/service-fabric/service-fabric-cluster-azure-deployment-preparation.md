---
title: Planera en Azure Service Fabric-klusterdistribution
description: Lär dig mer om att planera och förbereda för en distribution av Service Fabric-kluster till Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193484"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planera och förbereda för en klusterdistribution

Det är mycket viktigt att planera och förbereda för en distribution av produktionskluster.  Det finns många faktorer att tänka på.  I den här artikeln får du hjälp med stegen för att förbereda klusterdistributionen.

## <a name="read-the-best-practices-information"></a>Läs information om bästa praxis
För att hantera Azure Service Fabric-program och kluster framgångsrikt finns det åtgärder som vi rekommenderar att du utför för att optimera tillförlitligheten i din produktionsmiljö.  Mer information finns i [metodtips för Service Fabric-program och kluster .](service-fabric-best-practices-overview.md)

## <a name="select-the-os-for-the-cluster"></a>Välj operativsystemet för klustret
Service Fabric gör det möjligt att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux.  Innan du distribuerar klustret måste du välja OS: Windows eller Linux.  Varje nod (virtuell dator) i klustret kör samma operativsystem, du kan inte blanda Windows och Linux virtuella datorer i samma kluster.

## <a name="capacity-planning"></a>Kapacitetsplanering
Vid distribution till en produktionsmiljö är det viktigt med kapacitetsplanering. Här är några saker att tänka på i samband med den här processen.

* Det ursprungliga antalet nodtyper för klustret 
* Egenskaperna för varje nodtyp (storlek, antal instanser, primär, internet inför, antal virtuella datorer, etc.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

### <a name="select-the-initial-number-of-node-types"></a>Välj det ursprungliga antalet nodtyper
Först måste du ta reda på vad klustret du skapar kommer att användas för. Vilka typer av program planerar du att distribuera till det här klustret? Har din ansökan har flera tjänster, och gör någon av dem måste vara offentliga eller internet inför? Har dina tjänster (som utgör ditt program) har olika infrastrukturbehov såsom större RAM eller högre CPU-cykler? Ett Service Fabric-kluster kan bestå av mer än en nodtyp: en primär nodtyp och en eller flera icke-primära nodtyper. Varje nodtyp mappas till en skaluppsättning för den virtuella datorn. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. [Nodegenskaper och placeringsbegränsningar][placementconstraints] kan ställas in för att begränsa specifika tjänster till specifika nodtyper.  Mer information finns [i Antalet nodtyper som klustret behöver börja med](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Välj nodegenskaper för varje nodtyp
Nodtyper definierar VM SKU, tal och egenskaper för de virtuella datorerna i den associerade skalningsuppsättningen.

Den minsta storleken på virtuella datorer för varje nodtyp bestäms av den [hållbarhetsnivå][durability] du väljer för nodtypen.

Det minsta antalet virtuella datorer för den primära nodtypen bestäms av den [tillförlitlighetsnivå][reliability] du väljer.

Se minimirekommendationerna för [primära nodtyper,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance) [tillståndskänsliga arbetsbelastningar på icke-primära nodtyper](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)och [tillståndslösa arbetsbelastningar på icke-primära nodtyper](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Fler än det minsta antalet noder bör baseras på antalet repliker av programmet/tjänsterna som du vill köra i den här nodtypen.  [Kapacitetsplanering för Service Fabric-program](service-fabric-capacity-planning.md) hjälper dig att uppskatta de resurser du behöver för att köra dina program. Du kan alltid skala klustret uppåt eller nedåt senare för att justera för att ändra programarbetsbelastningen. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Använda efemära OS-diskar för skalningsuppsättningar för virtuella datorer

*Efemära OS-diskar* är lagring som skapas på den lokala virtuella datorn (VM) och sparas inte på fjärr-Azure Storage. De rekommenderas för alla nodtyper för Service Fabric (Primär och Sekundär), eftersom efemära OS-diskar jämfört med traditionella beständiga OS-diskar jämfört med traditionella beständiga OS-diskar:

* Minska läs-/skrivsvarstiden till OS-disk
* Aktivera snabbare hantering av återställnings-/ominställning av nodhantering
* Minska de totala kostnaderna (diskarna är gratis och medför ingen extra lagringskostnad)

Efemära OS-diskar är inte en specifik Service Fabric-funktion, utan snarare en funktion i *Azure-skalningsuppsättningarna* för virtuella datorer som mappas till nodtyper för Service Fabric. Om du använder dem med Service Fabric krävs följande i klustrets Azure Resource Manager-mall:

1. Kontrollera att nodtyperna anger [Azure VM-storlekar](../virtual-machines/windows/ephemeral-os-disks.md) som stöds för Efemära OPERATIVSYSTEM-diskar och att vm-storleken har tillräcklig cachestorlek för att stödja dess diskstorlek (se *Anmärkning* nedan.) Till exempel:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Var noga med att välja en VM-storlek med en cachestorlek som är lika med eller större än os-diskstorleken för den virtuella datorn själv, annars kan din Azure-distribution resultera i fel (även om den först accepteras).

2. Ange en version för`vmssApiVersion`skalningsuppsättning för virtuella datorer ( ) av `2018-06-01` eller senare:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. I avsnittet skala för virtuell dator i `Local` distributionsmallen anger du alternativet för: `diffDiskSettings`

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Mer information och ytterligare konfigurationsalternativ finns i [Efemära OS-diskar för virtuella Azure-datorer](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Välj hållbarhets- och tillförlitlighetsnivåer för klustret
Hållbarhetsnivån används för att ange för systemet de privilegier som dina virtuella datorer har med den underliggande Azure-infrastrukturen. I den primära nodtypen tillåter den här behörigheten Service Fabric att pausa alla infrastrukturbegäranden på vm-nivå (till exempel en omstart av den virtuella datorn, ominställd för den virtuella datorn eller vm-migrering) som påverkar kvorumkraven för systemtjänsterna och dina tillståndskänsliga tjänster. I de icke-primära nodtyperna tillåter den här behörigheten Service Fabric att pausa alla infrastrukturbegäranden på vm-nivå (till exempel omstart av virtuella datorer, ominställd för virtuella datorer och vm-migrering) som påverkar kvorumkraven för dina tillståndskänsliga tjänster.  Fördelar med de olika nivåerna och rekommendationerna på vilken nivå som ska användas och när, se [Klustrets hållbarhetsegenskaper][durability].

Tillförlitlighetsnivån används för att ange antalet repliker av de systemtjänster som du vill köra i det här klustret på den primära nodtypen. Ju fler repliker, desto mer tillförlitliga systemtjänster finns i klustret.  Fördelar med de olika nivåerna och rekommendationerna på vilken nivå som ska användas och när finns [i Klustrets tillförlitlighetsegenskaper][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Aktivera omvänd proxy och/eller DNS
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slutpunkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. För att göra det enklare att ansluta mellan tjänster tillhandahåller Service Fabric ytterligare tjänster: En [DNS-tjänst](service-fabric-dnsservice.md) och en [omvänd proxytjänst](service-fabric-reverseproxy.md).  Båda tjänsterna kan aktiveras när du distribuerar ett kluster.

Eftersom många tjänster, särskilt behållartjänster, kan ha ett befintligt URL-namn är det praktiskt att kunna lösa dessa med hjälp av standard-DNS-protokollet (i stället för Naming Service-protokollet) särskilt i scenarierna "lyft och skift". Detta är precis vad DNS-tjänsten gör. Det gör att du kan mappa DNS-namn till ett tjänstnamn och därmed lösa slutpunkts-IP-adresser.

De omvända proxyadresserna i klustret som exponerar HTTP-slutpunkter (inklusive HTTPS). Den omvända proxyn förenklar i hög grad att anropa andra tjänster genom att tillhandahålla ett specifikt URI-format.  Den omvända proxyn hanterar också de steg för att lösa, ansluta och försöka igen som krävs för att en tjänst ska kunna kommunicera med en annan.

## <a name="prepare-for-disaster-recovery"></a>Förbereda för haveriberedskap
En viktig del av att leverera hög tillgänglighet är att se till att tjänster kan överleva alla olika typer av fel. Detta är särskilt viktigt för fel som är oplanerade och utanför din kontroll. [Förbered för haveriberedskap](service-fabric-disaster-recovery.md) beskriver några vanliga fellägen som kan vara katastrofer om de inte modelleras och hanteras korrekt. Den diskuterar också mildrande åtgärder och åtgärder att vidta om en katastrof hände ändå.

## <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap
Är ditt program och kluster redo att ta produktionstrafik? Innan du distribuerar klustret till produktion, kör igenom [checklistan för produktionsberedskap](service-fabric-production-readiness-checklist.md). Se till att programmet och klustret fungerar smidigt genom att arbeta igenom objekten i den här checklistan. Vi rekommenderar starkt att alla dessa artiklar kontrolleras innan de går i produktion.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Service Fabric-kluster som kör Windows](service-fabric-best-practices-overview.md)
* [Skapa ett Service Fabric-kluster som kör Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
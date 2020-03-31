---
title: Planera klusterkapaciteten för serviceinfrastruktur
description: Service Fabric kluster kapacitet planering överväganden. Nodetyper, operationer, hållbarhet och tillförlitlighet nivåer
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258920"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric kluster kapacitet planering överväganden
Vid distribution till en produktionsmiljö är det viktigt med kapacitetsplanering. Här är några av de objekt som du måste tänka på som en del av den processen.

* Antalet nodtyper som klustret behöver för att börja med
* Egenskaperna för varje nodtyp (storlek, primär, internetvänd, antal virtuella datorer osv.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

> [!NOTE]
> Du bör granska alla **inte tillåtna uppgraderingsprincipvärden** under planeringen. Detta för att säkerställa att du ställer in värdena på rätt sätt och för att minska bränningen av klustret senare på grund av oföränderliga systemkonfigurationsinställningar. 
> 

Låt oss kort granska var och en av dessa punkter.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Antalet nodtyper som klustret behöver för att börja med
Först måste du ta reda på vad klustret du skapar kommer att användas för.  Vilka typer av program planerar du att distribuera till det här klustret? Om du inte är tydlig med syftet med klustret är du troligen ännu inte redo att gå in i kapacitetsplaneringsprocessen.

Fastställa antalet nodtyper som klustret behöver börja med.  Varje nodtyp mappas till en skaluppsättning för den virtuella datorn. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Så beslutet av antalet nodtyper kommer i huvudsak ner till följande överväganden:

* Har din ansökan har flera tjänster, och gör någon av dem måste vara offentliga eller internet inför? Typiska program innehåller en frontend gateway-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med frontend-tjänsterna. Så i det här fallet, du sluta med minst två nod typer.
* Har dina tjänster (som utgör ditt program) har olika infrastrukturbehov såsom större RAM eller högre CPU-cykler? Låt oss till exempel anta att det program som du vill distribuera innehåller en frontend-tjänst och en backend-tjänst. Front-end-tjänsten kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna för internet.  Backend-tjänsten är dock beräkningsintensiv och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är internetvända.
  
  I det här exemplet rekommenderar vi att du bestämmer dig för att placera alla tjänster på en nodtyp, rekommenderar vi att du placerar dem i ett kluster med två nodtyper.  På så sätt kan varje nodtyp ha olika egenskaper som internetanslutning eller vm-storlek. Antalet virtuella datorer kan också skalas oberoende av sig.  
* Eftersom du inte kan förutsäga framtiden går du med fakta som du känner till och väljer antalet nodtyper som dina program behöver börja med. Du kan alltid lägga till eller ta bort nodtyper senare. Ett Service Fabric-kluster måste ha minst en nodtyp.

## <a name="the-properties-of-each-node-type"></a>Egenskaperna för varje nodtyp
**Nodtypen** kan ses som likvärdig med roller i Molntjänster. Nodtyper definierar vm-storlekar, antalet virtuella datorer och deras egenskaper. Varje nodtyp som definieras i ett Service Fabric-kluster mappar till en [skalningsuppsättning för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Varje nodtyp är en distinkt skaluppsättning och kan skalas upp eller ned oberoende av varandra, ha olika uppsättningar portar öppna och ha olika kapacitetsmått. Mer information om relationerna mellan nodtyper och skalningsuppsättningar för virtuella datorer finns i [klusternodtyper](service-fabric-cluster-nodetypes.md)för service fabric.

Ett Service Fabric-kluster kan bestå av mer än en nodtyp. I så fall består klustret av en primär nodtyp och en eller flera icke-primära nodtyper.

En enskild nodtyp kan inte skalas på ett tillförlitligt sätt utöver 100 noder per skala för virtuell dator för SF-program. för att uppnå mer än 100 noder på ett tillförlitligt sätt, kräver att du lägger till ytterligare skaluppsättningar för virtuella datorer.

### <a name="primary-node-type"></a>Primär nodtyp

Service fabric-systemtjänsterna (till exempel Tjänsten Klusterhanteraren eller Image Store-tjänsten) placeras på den primära nodtypen. 

![Skärmbild av ett kluster med två nodtyper][SystemServices]

* Den **minsta storleken på virtuella datorer** för den primära nodtypen bestäms av den **hållbarhetsnivå** du väljer. Standardstandardnivån för hållbarhet är Brons. Mer information [finns i klustrets hållbarhetsegenskaper.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)  
* Det **minsta antalet virtuella datorer** för den primära nodtypen bestäms av den **tillförlitlighetsnivå** du väljer. Standardsäkerhetsnivån är Silver. Mer information [finns i klustrets tillförlitlighetsegenskaper.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)  

Från Azure Resource Manager-mallen konfigureras den primära `isPrimary` nodtypen med attributet under [nodtypdefinitionen](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Icke-primär nodtyp

I ett kluster med flera nodtyper finns det en primär nodtyp och resten är icke-primära.

* Den **minsta storleken på virtuella datorer** för icke-primära nodtyper bestäms av den **hållbarhetsnivå** du väljer. Standardstandardnivån för hållbarhet är Brons. Mer information finns [i Klustrets hållbarhetsegenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Det **minsta antalet virtuella datorer** för icke-primära nodtyper är en. Du bör dock välja det här numret baserat på antalet repliker av programmet/tjänsterna som du vill köra i den här nodtypen. Antalet virtuella datorer i en nodtyp kan ökas när du har distribuerat klustret.

## <a name="the-durability-characteristics-of-the-cluster"></a>Klustrets egenskaper för hållbarhet
Hållbarhetsnivån används för att ange för systemet de privilegier som dina virtuella datorer har med den underliggande Azure-infrastrukturen. I den primära nodtypen tillåter den här behörigheten Service Fabric att pausa alla infrastrukturbegäranden på vm-nivå (till exempel en omstart av den virtuella datorn, ominställd för den virtuella datorn eller vm-migrering) som påverkar kvorumkraven för systemtjänsterna och dina tillståndskänsliga tjänster. I de icke-primära nodtyperna tillåter den här behörigheten Service Fabric att pausa alla infrastrukturbegäranden på vm-nivå (till exempel omstart av virtuella datorer, ominställd för virtuella datorer och vm-migrering) som påverkar kvorumkraven för dina tillståndskänsliga tjänster.

| Hållbarhetsnivå  | Minsta antal virtuella datorer krävs | VM-sku:er som stöds                                                                  | Uppdateringar du gör i skalningsuppsättningen för den virtuella datorn                               | Uppdateringar och underhåll som initierats av Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Guld             | 5                              | SKU:er som är fullständiga för en enskild kund (till exempel L32s, GS5, G5, DS15_v2, D15_v2) | Kan fördröjas tills det har godkänts av Service Fabric-klustret | Kan pausas i 2 timmar per UD för att ge ytterligare tid för repliker att återställa från tidigare fel |
| Silver           | 5                              | Virtuella datorer med en kärna eller högre med minst 50 GB lokal SSD                      | Kan fördröjas tills det har godkänts av Service Fabric-klustret | Kan inte försenas under någon längre tid                                                    |
| Brons           | 1                              | Virtuella datorer med minst 50 GB lokal SSD                                              | Kommer inte att försenas av Service Fabric-klustret           | Kan inte försenas under någon längre tid                                                    |

> [!WARNING]
> Nodtyper som körs med bronshållbarhet får _inga privilegier_. Det innebär att infrastrukturjobb som påverkar dina tillståndskänsliga arbetsbelastningar inte stoppas eller försenas, vilket kan påverka dina arbetsbelastningar. Använd endast Brons för nodtyper som bara kör tillståndslösa arbetsbelastningar. För produktionsarbetsbelastningar rekommenderas att köra Silver eller högre. 
> 
> Oavsett hållbarhetsnivå kommer [Deallocation-åtgärden](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) på VM-skalningsuppsättningen att förstöra klustret

**Fördelar med att använda hållbarhetsnivåer i silver eller guld**
 
- Minskar antalet obligatoriska steg i en skalningsåtgärd (det vill ha noddeaktivering och Ta bort-ServiceFabricNodeState anropas automatiskt).
- Minskar risken för dataförlust på grund av en kundinitierad VM SKU-ändringsåtgärd eller Azure-infrastrukturåtgärder.

**Nackdelar med att använda Silver eller Gold hållbarhet nivåer**
 
- Distributioner till din skalningsuppsättning för virtuella datorer och andra relaterade Azure-resurser kan fördröjas, kan time out eller kan blockeras helt av problem i klustret eller på infrastrukturnivå. 
- Ökar antalet [repliklivscykelhändelser](service-fabric-reliable-services-lifecycle.md) (till exempel primära swappar) på grund av automatiska nodavaktiveringar under Azure-infrastrukturåtgärder.
- Tar noder ur drift under perioder medan Azure-plattform programuppdateringar eller maskinvaruunderhåll aktiviteter sker. Du kan se noder med status Inaktivera/inaktiverad under dessa aktiviteter. Detta minskar kapaciteten för klustret tillfälligt, men bör inte påverka tillgängligheten för ditt kluster eller program.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Rekommendationer för när du ska använda hållbarhetsnivåer i Silver eller Guld

Använd silver- eller guldhållbarhet för alla nodtyper som är värd för tillståndskänsliga tjänster som du förväntar dig att skala in (minska antalet vm-instanser) ofta, och du föredrar att distributionsåtgärderna försenas och att kapaciteten minskas till förmån för att förenkla dessa inskalningsfunktioner Verksamhet. Skalningsscenarierna (lägga till virtuella datorer-instanser) spelar inte in i ditt val av hållbarhetsnivån, endast skalning gör det.

### <a name="changing-durability-levels"></a>Ändra hållbarhetsnivåer
- Nodtyper med hållbarhetsnivåer av Silver eller Guld kan inte nedgraderas till Brons.
- Uppgradering från brons till silver eller guld kan ta några timmar.
- När du ändrar hållbarhetsnivå måste du uppdatera den i både tjänstinfrastrukturtilläggskonfigurationen i den virtuella datorns skalningsuppsättningsresurs och i nodtypdefinitionen i klusterresursen Service Fabric. Dessa värden måste matcha.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Driftrekommendationer för nodtypen som du har ställt in på silver- eller guldhållbarhetsnivå.

- Håll ditt kluster och program felfria hela tiden och se till att program svarar på alla livscykelhändelser för [tjänstreplik](service-fabric-reliable-services-lifecycle.md) (som replik i build har fastnat) i tid.
- Anta säkrare sätt att göra en VM SKU-ändring (Skala upp/ned): Om du ändrar vm-SKU för en skalningsuppsättning för virtuella datorer krävs ett antal steg och överväganden. Här är den process du kan följa för att undvika vanliga problem.
    - **För icke-primära nodtyper:** Vi rekommenderar att du skapar en ny skaluppsättning för virtuella datorer, ändrar begränsningen för tjänstplacering så att den omfattar den nya typen för skala/nodtyp för virtuell dator och sedan minskar antalet gamla instanser för den virtuella datorns skalningsuppsättning till noll, en nod i taget (det här är för att se till att borttagningen av noderna inte påverkar klustrets tillförlitlighet).
    - **För den primära nodtypen:** Om den VM SKU du har valt är kapacitet och du vill byta till en större VM SKU, följ vår vägledning om [vertikal skalning för en primär nodtyp](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Behåll minst fem noder för alla skalningsuppsättningar för virtuella datorer som har hållbarhetsnivå för guld eller silver aktiverat.
- Varje skala för virtuell dator med hållbarhetsnivå Silver eller Guld måste mappas till sin egen nodtyp i service fabric-klustret. Om du mappar flera skalningsuppsättningar för virtuella datorer till en nodtyp förhindrar samordningen mellan Service Fabric-klustret och Azure-infrastrukturen att fungera korrekt.
- Ta inte bort slumpmässiga VM-instanser, använd alltid skala nedskalningsfunktionen för virtuell dator. Borttagningen av slumpmässiga VM-instanser har en potential att skapa obalanser i VM-instansen som är spridd över UD och FD. Den här obalansen kan påverka systemens förmåga att belastningsbalansen korrekt mellan tjänstinstanserna/tjänstreplikerna.
- Om du använder Automatisk skalning anger du reglerna så att skalan i (borttagning av VM-instanser) endast görs en nod i taget. Det är inte säkert att skala ned mer än en instans i taget.
- Om du tar bort eller frigör virtuella datorer på den primära nodtypen bör du aldrig minska antalet tilldelade virtuella datorer under vad tillförlitlighetsnivån kräver. Dessa åtgärder kommer att blockeras på obestämd tid i en skala som med en hållbarhetsnivå av Silver eller Guld.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Klustrets tillförlitlighetsegenskaper
Tillförlitlighetsnivån används för att ange antalet repliker av de systemtjänster som du vill köra i det här klustret på den primära nodtypen. Ju fler repliker, desto mer tillförlitliga systemtjänster finns i klustret.  

Tillförlitlighetsnivån kan ta följande värden:

* Platinum - Kör systemtjänsterna med antalet målreplikuppsättningar på nio
* Guld - Kör systemtjänsterna med antalet målreplikuppsättningar på sju
* Silver - Kör systemtjänsterna med antalet målreplikuppsättningar på fem 
* Brons - Kör systemtjänsterna med antalet målreplikuppsättningar på tre

> [!NOTE]
> Den tillförlitlighetsnivå du väljer avgör det minsta antalet noder som din primära nodtyp måste ha. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Rekommendationer för tillförlitlighetsnivån

När du ökar eller minskar storleken på klustret (summan av VM-instanser i alla nodtyper) måste du uppdatera tillförlitligheten för klustret från en nivå till en annan. Om du gör det utlöser du de klusteruppgraderingar som behövs för att ändra antalet systemtjänster replikuppsättning. Vänta tills den pågående uppgraderingen har slutförts innan du gör några andra ändringar i klustret, till exempel lägga till noder.  Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller genom att köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Här är rekommendationen om att välja tillförlitlighetsnivå.  Antalet frönoder anges också till det minsta antalet noder för en tillförlitlighetsnivå.  För ett kluster med guldtillförlitlighet finns det till exempel 7 frönoder.

| **Antal klusternoder** | **Tillförlitlighetsnivå** |
| --- | --- |
| 1 |Ange inte parametern Tillförlitlighetsnivå, systemet beräknar det |
| 3 |Brons |
| 5 eller 6|Silver |
| 7 eller 8 |Guld |
| 9 och uppåt |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Primär nodtyp - kapacitetsvägledning

Här är vägledningen för planering av den primära nodtypkapaciteten:

- **Antal VM-instanser för att köra valfri produktionsarbetsbelastning i Azure:** Du måste ange en minsta storlek på primärnodtyp på 5 och en tillförlitlighetsnivå för silver.  
- **Antal VM-instanser för att köra testarbetsbelastningar i Azure** Du kan ange en minsta primärnodtypsstorlek på 1 eller 3. Det enda nodklustret, körs med en särskild konfiguration och därför stöds inte skala ut ur det klustret. Det enda nodklustret, har ingen tillförlitlighet och därför måste du ta bort/inte ange att konfigurationen (det räcker inte att ange konfigurationsvärdet). Om du ställer in ett nodkluster som konfigurerats via portalen tas konfigurationen automatiskt hand om. Ett och tre nodkluster stöds inte för att köra produktionsarbetsbelastningar. 
- **VM SKU:** Primär nodtyp är där systemtjänsterna körs, så den VM SKU du väljer för den, måste ta hänsyn till den totala toppbelastningen som du planerar att placera i klustret. Här är en analogi för att illustrera vad jag menar här - Tänk på den primära nodtypen som din "Lungor", det är det som ger syre till din hjärna, och så om hjärnan inte får tillräckligt med syre, lider din kropp. 

Eftersom kapacitetsbehoven för ett kluster bestäms av arbetsbelastningen som du planerar att köra i klustret, kan vi inte ge dig kvalitativ vägledning för din specifika arbetsbelastning, men här är den breda vägledningen som hjälper dig att komma igång

För produktionsarbetsbelastningar: 

- Vi rekommenderar att du avsätter dina kluster primära NodeType till systemtjänster och använder placeringsbegränsningar för att distribuera ditt program till sekundära NodeTypes.
- Den rekommenderade VM SKU är standard D2_V2 eller motsvarande med minst 50 GB lokal SSD.
- Den minsta använda VM SKU som stöds är Standard_D2_V3 eller standard D1_V2 eller motsvarande med minst 50 GB lokal SSD. 
- Vår rekommendation är minst 50 GB. För dina arbetsbelastningar, särskilt när du kör Windows-behållare, krävs större diskar. 
- Partiella virtuella VM-sku:er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- En serie VM-SKU:er stöds inte för produktionsarbetsbelastningar av prestandaskäl.
- Virtuella datorer med låg prioritet stöds inte.

> [!WARNING]
> Att ändra den primära noden VM SKU-storlek på ett kluster som körs är en skalningsåtgärd och dokumenteras i skala [för virtuell dator.](virtual-machine-scale-set-scale-node-type-scale-out.md)

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Icke-primär nodtyp - kapacitetsvägledning för tillståndskänsliga arbetsbelastningar

Den här vägledningen är för tillståndskänsliga arbetsbelastningar med hjälp av [driftsinfrastruktur tillförlitliga samlingar eller tillförlitliga aktörer](service-fabric-choose-framework.md) som du kör i den icke-primära nodtypen.

**Antal VM-instanser:** För produktionsarbetsbelastningar som är tillståndskänsliga rekommenderar vi att du kör dem med ett minsta antal och målreplikantalet 5. Det innebär att du i stabilt tillstånd hamnar med en replik (från en replikuppsättning) i varje feldomän och uppgraderingsdomän. Hela tillförlitlighetsnivåkonceptet för den primära nodtypen är ett sätt att ange den här inställningen för systemtjänster. Så samma hänsyn gäller för din stateful tjänster också.

Så för produktionsarbetsbelastningar är den minsta rekommenderade icke-primära nodtypstorleken 5, om du kör tillståndskänsliga arbetsbelastningar i den.

**VM SKU:** Det här är nodtypen där programtjänsterna körs, så den VM SKU du väljer för den måste ta hänsyn till den högsta belastning som du planerar att placera i varje nod. Kapacitetsbehoven för nodtypen bestäms av arbetsbelastningen som du planerar att köra i klustret, så vi kan inte ge dig kvalitativ vägledning för din specifika arbetsbelastning, men här är den breda vägledningen som hjälper dig att komma igång

För produktionsarbetsbelastningar 

- Den rekommenderade VM SKU är standard D2_V2 eller motsvarande med minst 50 GB lokal SSD.
- Den minsta använda VM SKU som stöds är Standard_D2_V3 eller standard D1_V2 eller motsvarande med minst 50 GB lokal SSD. 
- Partiella virtuella VM-sku:er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- En serie VM-SKU:er stöds inte för produktionsarbetsbelastningar av prestandaskäl.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Icke-primär nodtyp - kapacitetsvägledning för tillståndslösa arbetsbelastningar

Den här vägledningen för tillståndslösa arbetsbelastningar som du kör på den icke-primära nodtypen.

**Antal VM-instanser:** För produktionsarbetsbelastningar som är tillståndslösa är den minsta icke-primära nodtypstorleken 2 som stöds. På så sätt kan du köra två tillståndslösa instanser av ditt program och låta din tjänst överleva förlusten av en VM-instans. 

**VM SKU:** Det här är nodtypen där programtjänsterna körs, så den VM SKU du väljer för den måste ta hänsyn till den högsta belastning som du planerar att placera i varje nod. Kapacitetsbehoven för nodtypen bestäms av arbetsbelastningen som du planerar att köra i klustret. Vi kan inte ge dig kvalitativ vägledning för din specifika arbetsbelastning.  Men här är den breda vägledning som hjälper dig att komma igång.

För produktionsarbetsbelastningar 

- Rekommenderad VM SKU är standard D2_V2 eller motsvarande. 
- Den minsta använda VM SKU som stöds är Standard D1 eller Standard D1_V2 eller motsvarande. 
- Partiella virtuella VM-sku:er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- En serie VM-SKU:er stöds inte för produktionsarbetsbelastningar av prestandaskäl.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
När du är klar med kapacitetsplaneringen och konfigurerat ett kluster läser du följande:

* [Säkerhet för klustersäkerhet för serviceinfrastruktur](service-fabric-cluster-security.md)
* [Skala för kluster av servicetyg](service-fabric-cluster-scaling.md)
* [Planering av katastrofåterställning](service-fabric-disaster-recovery.md)
* [Förhållandet mellan nodetyper och skalningsuppsättning för virtuell dator](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

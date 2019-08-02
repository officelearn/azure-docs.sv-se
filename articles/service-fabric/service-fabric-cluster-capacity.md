---
title: Planera kapaciteten för Service Fabric klustret | Microsoft Docs
description: Överväganden vid planering av Service Fabric kluster kapacitet. Nodetypes, åtgärder, tålighet och Tillförlitlighets nivåer
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2019
ms.author: chackdan
ms.openlocfilehash: 2d13364093776028f96b75c5bfef252e2fdfc790
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679403"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Överväganden vid planering av Service Fabric kluster kapacitet
För produktions distribution är kapacitets planering ett viktigt steg. Här följer några av de objekt som du måste ta hänsyn till som en del av processen.

* Antalet nodtyper som ditt kluster måste börja med
* Egenskaperna för varje nodtyp (storlek, primär, Internet som är riktad, antal virtuella datorer osv.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

> [!NOTE]
> Du bör minimalt granska alla **inte tillåtna** värden för uppgraderings principer under planeringen. Detta är att se till att du ställer in värdena på rätt sätt och minimerar bränningen av klustret senare på grund av system konfigurations inställningar som inte kan ändras. 
> 

Låt oss kort granska vart och ett av dessa objekt.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Antalet nodtyper som ditt kluster måste börja med
Först måste du ta reda på vad klustret som du skapar ska användas för.  Vilka typer av program som du planerar att distribuera till det här klustret? Om du inte är klar med klustrets syfte är du troligen inte redo att ange kapacitets planerings processen ännu.

Upprätta antalet nodtyper som klustret måste börja med.  Varje nodtyp mappas till en skalnings uppsättning för virtuella datorer. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Beslutet om antalet nodtyper är i grunden följande överväganden:

* Har ditt program flera tjänster och behöver de vara offentliga eller Internet riktade? Typiska program innehåller en frontend Gateway-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med klient dels tjänsterna. I så fall har du minst två nodtyper.
* Har dina tjänster (som utgör ditt program) olika infrastruktur behov, till exempel större RAM-minne eller högre CPU-cykler? Anta till exempel att vi antar att det program som du vill distribuera innehåller en frontend-tjänst och en backend-tjänst. Frontend-tjänsten kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna för Internet.  Server dels tjänsten är dock en beräknings intensiv och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är Internet-riktade.
  
  I det här exemplet, även om du kan välja att placera alla tjänster på en nodtyp, rekommenderar vi att du placerar dem i ett kluster med två nodtyper.  Detta gör att varje nodtyp kan ha distinkta egenskaper som Internet anslutning eller storlek på virtuell dator. Antalet virtuella datorer kan också skalas oberoende av varandra.  
* Eftersom du inte kan förutsäga framtiden går du med fakta du känner till och väljer antalet nodtyper som dina program behöver starta med. Du kan alltid lägga till eller ta bort nodtyper senare. Ett Service Fabric-kluster måste ha minst en nodtyp.

## <a name="the-properties-of-each-node-type"></a>Egenskaperna för varje nodtyp
**Nodtypen** kan visas som likvärdig med roller i Cloud Services. Nodtyper definierar VM-storlekar, antalet virtuella datorer och deras egenskaper. Varje nodtyp som definieras i ett Service Fabric-kluster mappar till en [skalnings uppsättning för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Varje nodtyp är en distinkt skalnings uppsättning och kan skalas upp eller ned oberoende av varandra, ha olika uppsättningar portar öppna och ha olika kapacitets mått. Mer information om relationerna mellan nodtyper och skalnings uppsättningar för virtuella datorer, hur du använder RDP i en av instanserna, hur du öppnar nya portar och så vidare finns i [Service Fabric klusternoder](service-fabric-cluster-nodetypes.md).

Ett Service Fabric-kluster kan bestå av fler än en nodtyp. I så fall består klustret av en primär nodtyp och en eller flera typer som inte är av primär typ.

En enskild nodtyp kan inte på ett tillförlitligt sätt skala bortom 100 noder per virtuell dators skalnings uppsättning för SF-program; att uppnå fler än 100 noder på ett tillförlitligt sätt kräver att du lägger till ytterligare skalnings uppsättningar för virtuella datorer.

### <a name="primary-node-type"></a>Typ av primär nod

Service Fabric system tjänster (till exempel kluster hanterarens tjänst eller Avbildningsarkiv tjänst) placeras på den primära nodtypen. 

![Skärm bild av ett kluster som har två nodtyper][SystemServices]

* **Minimi storleken på virtuella datorer** för den primära nodtypen bestäms av den **hållbarhets nivå** som du väljer. Standard nivån för hållbarhet är brons. Mer information finns i [klustrets hållbarhets egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) .  
* Det **minsta antalet virtuella datorer** för den primära nodtypen avgörs av den **Tillförlitlighets nivå** som du väljer. Standard nivån för tillförlitlighet är silver. Se [Tillförlitlighets egenskaperna för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) om du vill ha mer information.  

Från Azure Resource Manager-mallen konfigureras den primära nodtypen med `isPrimary` attributet under [nodtypen definition](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ av icke-primär nod

I ett kluster med flera nodtyper, finns det en primär nodtyp och resten är icke-primära.

* **Minimi storleken på virtuella datorer** för icke-primära nodtyper bestäms av den **hållbarhets nivå** du väljer. Standard nivån för hållbarhet är brons. Mer information finns i [klustrets hållbarhets egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Det **minsta antalet virtuella datorer** för icke-primära nodtyper är en. Du bör dock välja det här numret baserat på antalet repliker av de program/tjänster som du vill köra i den här nodtypen. Antalet virtuella datorer i en nodtyp kan ökas när du har distribuerat klustret.

## <a name="the-durability-characteristics-of-the-cluster"></a>Klustrets hållbarhets egenskaper
Hållbarhets nivån används för att ange systemet de privilegier som dina virtuella datorer har med den underliggande Azure-infrastrukturen. I den primära nodtypen kan du med den här behörigheten Service Fabric pausa alla infrastruktur förfrågningar för virtuella datorer (till exempel en VM-omstart, återställning av virtuella datorer eller migrering av virtuella datorer) som påverkar kraven på kvorum för system tjänsterna och dina tillstånds känsliga tjänster. I de icke-primära nodtypen kan den här behörigheten Service Fabric pausa alla infrastruktur begär Anden för virtuella datorer (t. ex. omstart av virtuell dator, avbildning av virtuell dator och migrering av virtuella datorer) som påverkar kraven för de tillstånds känsliga tjänsterna.

| Hållbarhets nivå  | Minsta antal virtuella datorer som krävs | VM-SKU: er som stöds                                                                  | Uppdateringar som du gör i skalnings uppsättningen för den virtuella datorn                               | Uppdateringar och underhåll som har startats av Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Guld             | 5                              | Fullständiga SKU: er dedikerade till en enskild kund (till exempel L32s, GS5, G5, DS15_v2, D15_v2) | Kan förskjutas tills de godkänts av Service Fabrics klustret | Kan pausas i 2 timmar per UD för att tillåta ytterligare tid för repliker att återställas från tidigare fel |
| Silver           | 5                              | Virtuella datorer med en enda kärna eller över minst 50 GB lokal SSD                      | Kan förskjutas tills de godkänts av Service Fabrics klustret | Kan inte fördröjas under en viss tids period                                                    |
| Brons           | 1                              | Virtuella datorer med minst 50 GB lokal SSD                                              | Kommer inte att fördröjas av Service Fabric-klustret           | Kan inte fördröjas under en viss tids period                                                    |

> [!WARNING]
> Nodtyper som kör med brons hållbarhet får _inga privilegier_. Det innebär att infrastruktur jobb som påverkar dina tillstånds lösa arbets belastningar inte kommer att stoppas eller fördröjas, vilket kan påverka dina arbets belastningar. Använd endast brons för nodtyper som bara kör tillstånds lösa arbets belastningar. För produktions arbets belastningar rekommenderas att du kör silver eller högre. 
> 
> Oavsett vilken hållbarhets nivå som används [](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) förstörs klustret om du avallokerar en skalnings uppsättning för virtuella datorer

**Fördelar med att använda silver eller Gold-hållbarhets nivåer**
 
- Minskar antalet steg som krävs i en skalnings åtgärd (det vill säga Node-inaktive ring och Remove-ServiceFabricNodeState anropas automatiskt).
- Minskar risken för data förlust på grund av en kundinitierad VM SKU-ändrings åtgärd eller åtgärder för Azure-infrastruktur.

**Nack delar med att använda silver-eller guld hållbarhets nivåer**
 
- Distributioner till skalnings uppsättningen för den virtuella datorn och andra relaterade Azure-resurser kan fördröjas, kan ta tid ut eller blockeras helt av problem i klustret eller på infrastruktur nivå. 
- Ökar antalet händelser för [replik livs cykeln](service-fabric-reliable-services-lifecycle.md) (till exempel primära växlingar) på grund av automatiserade nod-inaktivitet under Azures infrastruktur åtgärder.
- Tar noder ur drift under tids perioder medan Azure-plattformens program uppdateringar eller aktiviteter för maskin varu underhåll inträffar. Du kan se noder med status inaktive rad eller inaktive rad under dessa aktiviteter. Detta minskar kapaciteten för klustret tillfälligt, men påverkar inte tillgängligheten för ditt kluster eller program.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Rekommendationer för när du ska använda silver eller Golds hållbarhets nivåer

Använd silver eller guld tålighet för alla nodtyper som är värdar för tillstånds känsliga tjänster som du förväntar dig att skala in (minska antalet virtuella dator instanser) ofta, och du vill att distributions åtgärder ska fördröjas och att kapaciteten minskar för att förenkla denna skalning verksamhetshanteraren. De skalbara scenarierna (tillägg av virtuella dator instanser) spelas inte in i ditt val av hållbarhets nivån, bara skala i.

### <a name="changing-durability-levels"></a>Ändra hållbarhets nivåer
- Nodtyper med hållbarhets nivåer för silver eller guld kan inte nedgraderas till brons.
- Det kan ta några timmar att uppgradera från brons till silver eller guld.
- När du ändrar hållbarhets nivå måste du uppdatera den i både Service Fabric tilläggs konfiguration i den virtuella datorns skalnings uppsättnings resurs och i definitionen av nodtypen i Service Fabric kluster resursen. Dessa värden måste vara identiska.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Drift rekommendationer för nodtypen som du har angett till silver eller Gold-hållbarhets nivån.

- Se till att ditt kluster och program är felfritt och se till att programmen svarar på alla [livs cykel händelser för tjänste repliken](service-fabric-reliable-services-lifecycle.md) (t. ex. replik i build är fastnat) inom rimlig tid.
- Införa säkrare sätt att göra en VM-SKU-ändring (skala upp/ned): Att ändra den virtuella datorns SKU för en virtuell dators skalnings uppsättning måste ha ett antal steg och överväganden. Här följer processen som du kan följa för att undvika vanliga problem.
    - **För icke-primära nodtyper:** Vi rekommenderar att du skapar en ny skalnings uppsättning för virtuella datorer, ändrar tjänst placerings begränsningen till att inkludera den nya skalnings uppsättningen för virtuella datorer/nodtypen och sedan minskar antalet gamla instanser av virtuell dators skalnings uppsättning till noll, en nod i taget (detta är att göra att borttagning av noderna inte påverkar tillförlitligheten för klustret.
    - **För typen av primär nod:** Om den virtuella datorns SKU som du har valt har kapacitet och du vill byta till en större VM-SKU följer du våra rikt linjer om [vertikal skalning för en primär nodtyp](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Behåll det lägsta antalet fem noder för alla skalnings uppsättningar för virtuella datorer som har en hög grad av guld eller silver aktiverat.
- Varje skalnings uppsättning för virtuella datorer med silver eller guld måste mappas till en egen nodtyp i Service Fabric klustret. Om du mappar flera skalnings uppsättningar för virtuella datorer till en enda nodtyp förhindrar det att samordningen mellan Service Fabric-klustret och Azure-infrastrukturen fungerar som de ska.
- Ta inte bort slumpmässiga VM-instanser, Använd alltid skalnings funktionen skalnings uppsättning för virtuella datorer. Borttagning av slumpmässiga VM-instanser har möjlighet att skapa obalanser i den virtuella dator instansens spridning över UD och FD. Den här obalansen kan påverka systemets möjlighet att korrekt belastningsutjämna mellan tjänst instanser/tjänst repliker.
- Om du använder autoskalning ställer du in reglerna så att skala i (borttagning av VM-instanser) bara är utförd en nod i taget. Att skala ned fler än en instans i taget är inte säkert.
- Om du tar bort eller frigör virtuella datorer på den primära nodtypen, bör du aldrig minska antalet allokerade virtuella datorer under vad Tillförlitlighets nivån kräver. De här åtgärderna kommer att blockeras oändligt i en skalnings uppsättning med en hållbarhets nivå på silver eller guld.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Tillförlitlighets egenskaperna för klustret
Tillförlitlighets nivån används för att ange antalet repliker av de system tjänster som du vill köra i det här klustret på den primära nodtypen. Antalet repliker, desto mer pålitliga system tjänsterna finns i klustret.  

Tillförlitlighets nivån kan ha följande värden:

* Platina – kör System tjänsterna med ett mål replik uppsättnings antal sju
* Guld – kör System tjänsterna med ett mål replik uppsättnings antal sju
* Silver – kör System tjänsterna med ett mål replik uppsättnings antal fem 
* Brons – kör System tjänsterna med ett mål replik uppsättnings antal tre

> [!NOTE]
> Den Tillförlitlighets nivå som du väljer avgör det minsta antalet noder som den primära nodtypen måste ha. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Rekommendationer för Tillförlitlighets nivån

När du ökar eller minskar storleken på klustret (summan av virtuella dator instanser i alla nodtyper) måste du uppdatera tillförlitligheten för klustret från en nivå till en annan. Detta utlöser de kluster uppgraderingar som krävs för att ändra antalet replik uppsättningar för system tjänster. Vänta tills den pågående uppgraderingen har slutförts innan du gör några andra ändringar i klustret, t. ex. att lägga till noder.  Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller genom att köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Här är en rekommendation om att välja Tillförlitlighets nivå.  Antalet startnoder anges också till det minsta antalet noder för en Tillförlitlighets nivå.  För ett kluster med guld tillförlitlighet finns det till exempel sju startnoder.

| **Antal klusternoder** | **Tillförlitlighets nivå** |
| --- | --- |
| 1 |Ange inte parametern för Tillförlitlighets nivå, systemet beräknar den |
| 3 |Brons |
| 5 eller 6|Silver |
| 7 eller 8 |Guld |
| 9 och uppåt |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Primär nodtyp – kapacitets vägledning

Här följer vägledningen för att planera den primära nodtypen:

- **Antal virtuella dator instanser att köra produktions arbets belastningar i Azure:** Du måste ange en minsta primär nodtyp storlek på 5 och en Tillförlitlighets nivå för silver.  
- **Antal VM-instanser att köra test arbets belastningar i Azure** Du kan ange en minsta typ av primär nodtyp på 1 eller 3. Klustret med en nod körs med en särskild konfiguration och kan därför inte längre skala ut från klustret. Klustret med en nod har ingen tillförlitlighet och så i din Resource Manager-mall måste du ta bort/inte ange att konfigurationen (inte ange konfiguration svärdet är otillräcklig). Om du konfigurerar ett kluster för en nod som har kon figurer ATS via portalen tas konfigurationen automatiskt hand om. Ett och tre nod-kluster stöds inte för att köra produktions arbets belastningar. 
- **VM-SKU:** Den primära nodtypen är den plats där system tjänsterna körs, så den VM-SKU du väljer för den måste ta hänsyn till den övergripande belastningen som du planerar att placera i klustret. Här är en analog för att illustrera vad jag menar här – tänker på den primära nodtypen som din "lungor", det är vad som ger syre till din hjärna, och så om hjärna inte får tillräckligt med syre blir ditt innehåll. 

Eftersom kapacitets behoven hos ett kluster bestäms av arbets belastningen som du planerar att köra i klustret, kan vi inte ge dig kvalitativ vägledning för din speciella arbets belastning, men det här är en bred vägledning som hjälper dig att komma igång

För produktions arbets belastningar: 

- Vi rekommenderar att du dedikerar klustrets primära NodeType till system tjänster och använder placerings begränsningar för att distribuera ditt program till sekundära NodeTypes.
- Den rekommenderade VM-SKU: n är standard D2_V2 eller likvärdig med minst 50 GB lokal SSD.
- Den minsta virtuella datorns SKU som stöds är Standard_D2_V3 eller standard-D1_V2 eller motsvarande med minst 50 GB lokal SSD. 
- Vår rekommendation är minst 50 GB. För dina arbets belastningar, särskilt när du kör Windows-behållare, krävs större diskar. 
- Partiella kärn VM-SKU: er som standard a0 stöds inte för produktions arbets belastningar.
- En serie VM SKU: er stöds inte för produktions arbets belastningar av prestanda skäl.
- Virtuella datorer med låg prioritet stöds inte.

> [!WARNING]
> Ändra den primära nodens SKU-storlek i ett kluster som körs, är en skalnings åtgärd och dokumenterad i den virtuella datorns skalnings [uppsättning skala ut](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentation.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ av icke-primär nodtyp – kapacitets vägledning för tillstånds känsliga arbets belastningar

Den här vägledningen är för tillstånds känsliga arbets belastningar som använder sig av pålitliga samlingar för Service Fabric [eller pålitliga aktörer](service-fabric-choose-framework.md) som körs i den icke-primära nodtypen.

**Antal VM-instanser:** För produktions arbets belastningar som är tillstånds känsliga rekommenderar vi att du kör dem med ett minimi-och mål replik antal på 5. Det innebär att du i stabilt tillstånd slutar med en replik (från en replik uppsättning) i varje fel domän och uppgraderings domän. Hela Tillförlitlighets nivån för den primära nodtypen är ett sätt att ange den här inställningen för system tjänster. Samma överväganden gäller även för dina tillstånds känsliga tjänster.

För produktions arbets belastningar är den minsta rekommenderade icke-primära nodtypen 5, om du kör tillstånds känsliga arbets belastningar i den.

**VM-SKU:** Det här är nodtypen där dina program tjänster körs, så den VM-SKU du väljer för den måste ta hänsyn till den högsta belastning som du planerar att placera i varje nod. Vilken typ av kapacitet som krävs av nodtypen bestäms av arbets belastningen som du planerar att köra i klustret, så vi kan inte ge dig kvalitativ vägledning för din speciella arbets belastning, men det här är en bred vägledning som hjälper dig att komma igång

För produktions arbets belastningar 

- Den rekommenderade VM-SKU: n är standard D2_V2 eller likvärdig med minst 50 GB lokal SSD.
- Den minsta virtuella datorns SKU som stöds är Standard_D2_V3 eller standard-D1_V2 eller motsvarande med minst 50 GB lokal SSD. 
- Partiella kärn VM-SKU: er som standard a0 stöds inte för produktions arbets belastningar.
- En serie VM SKU: er stöds inte för produktions arbets belastningar av prestanda skäl.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ av icke-primär nodtyp – kapacitets vägledning för tillstånds lösa arbets belastningar

Den här vägledningen för tillstånds lösa arbets belastningar som du kör på den icke-primära nodtypen.

**Antal VM-instanser:** För produktions arbets belastningar som är tillstånds lösa är den minsta storlek som stöds av en icke-primär nodtyp 2. På så sätt kan du köra två tillstånds lösa instanser av ditt program och låta tjänsten överleva förlust av en VM-instans. 

**VM-SKU:** Det här är nodtypen där dina program tjänster körs, så den VM-SKU du väljer för den måste ta hänsyn till den högsta belastning som du planerar att placera i varje nod. Vilken typ av kapacitet som krävs av nodtypen bestäms av arbets belastningen som du planerar att köra i klustret. Vi kan inte ge dig kvalitativ vägledning för din speciella arbets belastning.  Här är dock de breda rikt linjerna som hjälper dig att komma igång.

För produktions arbets belastningar 

- Den rekommenderade VM-SKU: n är standard D2_V2 eller motsvarande. 
- Den minsta användningen av VM SKU: n som stöds är standard D1 eller standard D1_V2 eller motsvarande. 
- Partiella kärn VM-SKU: er som standard a0 stöds inte för produktions arbets belastningar.
- En serie VM SKU: er stöds inte för produktions arbets belastningar av prestanda skäl.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
När du är klar med din kapacitets planering och konfigurerat ett kluster kan du läsa följande:

* [Service Fabric kluster säkerhet](service-fabric-cluster-security.md)
* [Service Fabric kluster skalning](service-fabric-cluster-scaling.md)
* [Planering för haveri beredskap](service-fabric-disaster-recovery.md)
* [Relationen mellan Nodetypes och skalnings uppsättningen för den virtuella datorn](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

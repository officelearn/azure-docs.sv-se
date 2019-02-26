---
title: Planera kapacitet för Service Fabric-kluster | Microsoft Docs
description: Service Fabric-kluster överväganden vid kapacitetsplanering. Nivåerna Nodetypes, åtgärder, tillförlitlighet och tillförlitlighet
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: 5fb8f54f50d821e53ec260c67ad5cf56c7f5671b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816546"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric-kluster kapacitetsplanering
För alla Produktionsdistribution är kapacitetsplanering ett viktigt steg. Här är några av de objekt som du måste väga in som en del av den här processen.

* Antal nodtyper som behövs i klustret för att börja med
* Egenskaperna för respektive nodtyp (storlek, primär, internetuppkopplad, antalet virtuella datorer, osv.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

> [!NOTE]
> Du bör granska alla minimalt **otillåtna** uppgradera principvärden när du planerar. Det här är att se till att du har angett värden på rätt sätt och för att minimera bränna på klustret senare på grund av tilldelningstyper systemkonfigurationsinställningar. 
> 

Låt oss gå igenom objekten.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Antal nodtyper som behövs i klustret för att börja med
Du måste först ta reda på vad du skapar klustret som ska användas för.  Vilka typer av program som du planerar att distribuera i det här klustret? Om du inte rensa på syftet med klustret du sannolikt ännu inte är redo för att ange processen för kapacitetsplanering.

Fastställa det antal nodtyper som behövs i klustret för att börja med.  Varje nodtyp mappas till en VM-skalningsuppsättning. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Det handlar antal nodtyper beslut i stort sett om att följande överväganden:

* Har ditt program flera tjänster och behöver någon av dem vara offentligt eller mot internet? Vanliga program innehåller en klientdelsgateway som-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med frontend-tjänster. Så i detta fall använder få du med minst två nodtyper.
* Har dina tjänster (som utgör ditt program) för olika infrastrukturbehov, till exempel RAM-minne eller högre CPU-cykler? Låt oss anta att det program som du vill distribuera innehåller en frontend-tjänst och en backend tjänst. Frontend-tjänst kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna till internet.  Backend-tjänsten, men är beräkning intensiva och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är internet som riktas mot.
  
  Även om du kan välja att placera alla tjänster på en nodtyp i det här exemplet rekommenderar vi att du ska placera dem i ett kluster med två nodtyper.  På så sätt kan varje nodtyp har olika egenskaper, till exempel internet-anslutning eller VM-storlek. Hur många virtuella datorer kan skalas oberoende av varandra, samt.  
* Eftersom du inte kan förutspå framtiden, gå med fakta som du vet och välj antal nodtyper som dina program måste börja med. Du kan alltid lägga till eller ta bort nodtyper senare. Service Fabric-kluster måste ha minst en nodtyp.

## <a name="the-properties-of-each-node-type"></a>Egenskaperna för varje nodtyp
Den **nodtyp** kan ses som roller i molntjänster. Nodtyper definierar storlekarna, hur många virtuella datorer och deras egenskaper. Varje nodtyp som definieras i Service Fabric-kluster som mappar till en [virtual machine scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Varje nodtyp är en distinkt skala ange och kan skalas upp eller ned oberoende av varandra, ha olika portar öppna och ha olika kapacitet. För mer information om relationerna mellan nodtyper och VM-skalningsuppsättningar, hur till RDP till någon av instanserna, hur du öppnar nya portarna och så vidare se [nodtyper för Service Fabric-kluster](service-fabric-cluster-nodetypes.md).

Service Fabric-kluster kan bestå av fler än en nodtyp. I sådana fall består klustret av en primära nodtypen och en eller flera icke-primär nodtyper.

En enda nodtyp skala inte på ett tillförlitligt sätt utöver 100 noder per skalningsuppsättning för SF program; uppnå större än 100 noder på ett tillförlitligt sätt måste du lägga till ytterligare VM-skalningsuppsättningar.

### <a name="primary-node-type"></a>Primära nodtypen

Service Fabric-systemtjänster (till exempel Cluster Manager-tjänsten eller avbildning Store-tjänsten) placeras på den primära nodtypen. 

![Skärmbild av ett kluster med två nodtyper][SystemServices]

* Den **minsta storlek på virtuella datorer** för den primära noden typ bestäms av den **hållbarhetsnivå** du väljer. Hållbarhetsnivå standard är Brons. Se [hållbarhet egenskaper för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) för mer information.  
* Den **minsta antal virtuella datorer** för den primära noden typ bestäms av den **tillförlitlighetsnivån** du väljer. Standard tillförlitlighetsnivån är Silver. Se [tillförlitlighetsegenskaper för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) för mer information.  

Från Azure Resource Manager-mallen den primära nodtypen har konfigurerats med den `isPrimary` attributet den [typdefinition för nod](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Icke-primära nodtypen

Det finns en primära nodtypen i ett kluster med flera nodtyper och resten är icke-primär.

* Den **minsta storlek på virtuella datorer** för icke-primära noden typer bestäms av den **hållbarhetsnivå** du väljer. Hållbarhetsnivå standard är Brons. Mer information finns i [hållbarhet egenskaper för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Den **minsta antal virtuella datorer** för icke-primär nodtyper är en. Dock bör du välja det här talet baserat på antalet repliker för program/tjänster som du vill köra i den här nodtypen. Antalet virtuella datorer i en nodtyp kan utökas när du har distribuerat i klustret.

## <a name="the-durability-characteristics-of-the-cluster"></a>Hållbarhet egenskaper för klustret
Hållbarhetsnivån används för att ange de behörigheter som dina virtuella datorer som har med den underliggande Azure-infrastrukturen i systemet. I den primära nodtypen kan privilegiet Service Fabric för att pausa varje virtuell dator på infrastruktur-begäran (till exempel en omstart av virtuell dator, återställa avbildningen av de virtuella datorer eller VM-migrering) som påverkar kvorum kraven för systemtjänster och dina tillståndskänsliga tjänster. I icke-primär nodtyperna kan privilegiet Service Fabric för att pausa alla VM på infrastruktur förfrågningar (till exempel omstart av virtuell dator, återställa avbildningen av de virtuella datorer och VM-migrering) som påverkar kvorum kraven för din tillståndskänsliga tjänster.

| Hållbarhetsnivå  | Minsta antal virtuella datorer | Stöds VM SKU: er                                                                  | Uppdateringar som du gör i virtual machine scale Sets                               | Uppdateringar och underhåll som initierades av Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Guld             | 5                              | Fullständig noder SKU: er som är dedikerad till en enda kund (till exempel L32s, GS5, G5, DS15_v2, D15_v2) | Kan fördröjas tills godkänts av Service Fabric-kluster | Kan pausas i 2 timmar per UD att tillåta mer tid för repliker efter tidigare fel |
| Silver           | 5                              | Virtuella datorer med enkel kärna eller senare                                                        | Kan fördröjas tills godkänts av Service Fabric-kluster | Det går inte att fördröjas för varje betydande tidsperiod                                                    |
| Brons           | 1                              | Alla                                                                                | Inte kommer att fördröjas av Service Fabric-kluster           | Det går inte att fördröjas för varje betydande tidsperiod                                                    |

> [!WARNING]
> Nodtyper som körs med Brons hållbarhet hämta _saknad behörighet_. Det innebär att infrastruktur för jobb som påverkar din tillståndslösa arbetsbelastningar inte kommer stoppas eller fördröjd, vilket kan påverka dina arbetsbelastningar. Använd endast Brons för nodtyper som kör endast tillståndslösa arbetsbelastningar. Kör Silver eller ovan rekommenderas för produktionsarbetsbelastningar. 

> Oavsett eventuella hållbarhetsnivå [frigörs](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) åtgärden på den Virtuella Datorskalningsuppsättningen förstör klustret

**Fördelarna med att använda Silver eller Gold hållbarhet nivåer**
 
- Minskar antalet steg i en skala in-åtgärd (det vill säga noden inaktivering och ta bort ServiceFabricNodeState kallas automatiskt).
- Minskar risken för dataförlust på grund av en kundinitierade plats VM SKU ändring eller de Azure-infrastrukturen åtgärder.

**Nackdelarna med att använda Silver eller Gold hållbarhet nivåer**
 
- Distributioner till VM-skalningsuppsättning ställa in och andra relaterade Azure-resurser kan fördröjas, kan tar för lång tid eller blockeras helt och hållet av problem i klustret eller på infrastrukturnivå. 
- Ökar antalet [Livscykelhändelser för repliken](service-fabric-reliable-services-lifecycle.md) (till exempel primära växlingar) på grund av automatiserad noden avaktiveringar under Azure-infrastrukturen.
- Tar noder ur för tidsperioder när programuppdateringar för Azure-plattformen eller maskinvaruunderhåll aktiviteter som utförs. Du kan se noder med status inaktiveras/inaktiverad under dessa aktiviteter. Detta minskar kapaciteten för klustret tillfälligt, men bör inte påverka tillgängligheten för dina kluster eller ett program.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Rekommendationer för när du ska använda Silver eller Gold hållbarhet nivåer

Använda Silver eller Gold hållbarhet för alla typer av noden som är värdar för tillståndskänsliga tjänster som du förväntar dig att skala in (minska instansantalet för virtuella datorer), och du föredrar att distributionsåtgärder fördröjas att reduceras av förenkla dessa skala in och kapacitet åtgärder. Skala ut-scenarier (lägga till instanser av virtuella datorer) spelar inte i ditt val av hållbarhetsnivån, endast att skala in kräver.

### <a name="changing-durability-levels"></a>Föränderliga hållbarhet nivåer
- Nodtyper med hållbarhet kontrollnivåer Silver eller Gold kan inte nedgraderas till Brons.
- Uppgradera från Brons till Silver eller Gold kan ta några timmar.
- När du ändrar hållbarhetsnivå, måste du uppdatera den i både Service Fabric konfigurationen av tillägget i VM scale set resursen och i typdefinition för nod i ditt Service Fabric-klusterresursen. Dessa värden måste matcha.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Operativa rekommendationer för noden ange att du har angett till silver eller gold hållbarhet nivå.

- Ha-kluster och program felfria hela tiden och se till att program ska svara på alla [tjänsten Livscykelhändelser för repliken](service-fabric-reliable-services-lifecycle.md) (t.ex. repliken i bygger har fastnat) i tid.
- Anta säkrare sätt att göra en VM-SKU ändra (skala upp/ned): Ändra VM-SKU på en skalningsuppsättning för virtuell dator sin natur är en osäkra åtgärd och så bör inte användas om det är möjligt. Här är den process som du kan följa för att undvika vanliga problem.
    - **För icke-primär nodtyper:** Vi rekommenderar att du skapar nya virtuella datorns skalningsuppsättning, ändra villkoret för tjänsten placering för att inkludera den nya VM scale set/node-typen och minska gamla VM scale set instansantalet till noll, en nod i taget (detta är att göra Se till att ta bort noder inte påverkar tillförlitligheten för klustret).
    - **För den primära nodtypen:** Vår rekommendation är att du inte ändrar VM-SKU på den primära nodtypen. Ändringar av den primära nodtypen SKU inte stöds. Om orsaken till den nya SKU: N är kapacitet, rekommenderar vi att lägga till fler instanser. Om detta inte möjligt, skapa ett nytt kluster och [Återställ programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från ditt gamla kluster. Du behöver inte återställa alla systemtillstånd för tjänsten, de återskapas när du distribuerar ditt program till det nya klustret. Om du kör tillståndslösa program i klustret måste du distribuera dina program till det nya klustret.  Du har inget att återställa. Om du vill gå stöds inte vägen och vill ändra VM-SKU, ange sedan gör ändringar i VM-skalningsuppsättningen modell-definitionen för att återspegla den nya SKU: N. Om klustret har endast en nodtyp, kontrollerar du att alla dina tillståndskänsliga program att besvara alla [tjänsten Livscykelhändelser för repliken](service-fabric-reliable-services-lifecycle.md) (t.ex. repliken i bygger har fastnat) i god tid och att din tjänsterepliken återskapa varaktighet är mindre än fem minuter (för Silver hållbarhetsnivå). 
    
- Underhålla ett minsta antal fem noder för alla VM-skalningsuppsättning som har hållbarhetsnivå Gold och Silver aktiverat.
- Varje VM-skalningsuppsättning med hållbarhetsnivå Silver eller Gold måste mappas till en egen nodtyp i Service Fabric-klustret. Mappa flera skalningsuppsättningar för virtuella datorer till en enda nodtyp förhindrar samordning mellan Service Fabric-kluster och Azure-infrastrukturen fungerar korrekt.
- Inte ta bort slumpmässiga VM-instanser, alltid använda VM scale set skala ned funktionen. Borttagning av slumpmässiga VM-instanser har en potentiell för att skapa obalans i VM-instansen som är fördelade på UD och FD. Detta kan inverka menligt system-möjligheten att korrekt belastningsutjämna mellan tjänsten instanser/tjänstens repliker.
- Om du använder automatisk skalning, anger du reglerna så att skala in (ta bort instanser av virtuella datorer) görs bara en nod i taget. Det är inte säkert att skala ned fler än en instans i taget.
- Om du tar bort eller frigörs virtuella datorer på den primära nodtypen, ska du minska antalet allokerade virtuella datorer under tillförlitlighetsnivån kräver aldrig. De här åtgärderna kommer att blockeras på obestämd tid i en skalningsuppsättning med en hållbarhetsnivå av Silver eller Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Tillförlitlighetsegenskaper för klustret
Tillförlitlighetsnivån används för att ange antalet repliker av systemtjänster som du vill köra i det här klustret på den primära nodtypen. Det mer antalet repliker, mer tillförlitliga systemtjänsterna i klustret.  

Tillförlitlighetsnivån kan ha följande värden:

* Platina - kör systemtjänster med ett mål replikuppsättning antal sju
* Guld - kör systemtjänster med ett mål replikuppsättning antal sju
* Silver - kör systemtjänster med ett mål replikuppsättning antal fem 
* Brons - kör systemtjänster med ett mål replikuppsättning tre

> [!NOTE]
> Tillförlitlighetsnivån som du anger det minsta antalet noder som den primära nodtypen måste ha. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Rekommendationer för tillförlitlighetsnivån

När du ökar eller minskar storleken på ditt kluster (summan av VM-instanser i alla nodtyper), måste du uppdatera tillförlitligheten för ditt kluster från en nivå till en annan. Detta utlöser klusteruppgradering behövs för att ändra system services repliken ange antal. Vänta tills uppgraderingen pågår ska slutföras innan du gör några andra ändringar i klustret, som att lägga till noder.  Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller genom att köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Här är rekommendationen om att välja tillförlitlighetsnivån.  Antalet startvärdesnoder också anges det minsta antalet noder för en tillförlitlighetsnivå.  Till exempel för ett kluster med guld tillförlitlighet finns 7 startvärdesnoder.

| **Antalet klusternoder** | **Tillförlitlighetsnivå** |
| --- | --- |
| 1 |Ange inte parametern tillförlitlighetsnivån beräknar systemet |
| 3 |Brons |
| 5 eller 6|Silver |
| 7 eller 8 |Guld |
| 9 och senare |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Primära nodtypen - kapacitet vägledning

Här följer riktlinjer för planering av kapacitet för primära noden typ:

- **Antalet Virtuella datorinstanser att köra alla produktionsarbetsbelastningar i Azure:** Du måste ange en minsta primära noden typ storlek på 5 och en tillförlitlighet nivå av Silver.  
- **Antalet Virtuella datorinstanser att köra arbetsbelastningar för testning i Azure** du kan ange en minsta primära noden teckenstorlek på 1 eller 3. En nod-klustret körs med en särskild konfiguration och, skalning av klustret stöds inte. En nod-klustret har inga tillförlitlighet och i Resource Manager-mallen, du måste ta bort/inte ange denna konfiguration (inte ställa in Konfigurationsvärdet är inte tillräckligt). Om du har konfigurerat en nod klustret konfigureras via portalen är automatiskt konfigurationen tar hand om. Kluster med en och tre noder stöds inte för arbetsbelastningar under produktion. 
- **SKU FÖR VIRTUELL DATOR:** Primära nodtypen är där systemtjänster körs, så att VM-SKU du väljer för den, måste ta hänsyn den övergripande högsta laddar du planerar att placera i klustret. Så om hjärnan inte får tillräckligt med oxygen, meddelandetexten drabbas här är en också illustrerar vad menar jag att här – Tänk på den primära nodtypen som din ”lungor”, det är vad tillhandahåller oxygen till dig och ut. 

Eftersom kapacitetsbehoven i ett kluster bestäms av arbetsbelastning som du planerar att köra i klustret, inte kan vi ge du med kvalitativa vägledning för den specifika arbetsbelastningen, men här är bred riktlinjer som hjälper dig att komma igång

För produktionsarbetsbelastningar: 

- Vi rekommenderar att du anger dina kluster primära NodeType systemtjänster och Använd placeringsbegränsningar för att distribuera programmet till sekundära NodeTypes.
- Den rekommenderade VM-SKU är Standard D3 Standard D3_V2 eller motsvarande med minst 14 GB lokal SSD-lagring.
- Minsta stöds användning VM SKU är Standard D1 Standard D1_V2 eller motsvarande med minst 14 GB lokal SSD-lagring. 
- 14 GB lokal SSD-lagring är ett minimikrav. Vår rekommendation är minst 50 GB. Större diskar krävs för dina arbetsbelastningar, särskilt när du kör Windows-behållare. 
- Partiell grundläggande VM SKU: er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- Standard A1-SKU stöds inte för produktionsarbetsbelastningar av prestandaskäl.
- Lågprioriterade virtuella datorer stöds inte.

> [!WARNING]
> Ändra den primära noden VM SKU-storlek på ett aktivt kluster är en åtgärd för skalning och dokumenteras i [Virtual Machine Scale Sets skala ut](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentation.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Icke-primära nodtypen - kapacitet vägledning för tillståndskänsliga arbetsbelastningar

Den här vägledningen är avsedd för tillståndskänsliga arbetsbelastningar som använder Service fabric [tillförlitliga samlingar eller reliable Actors](service-fabric-choose-framework.md) som körs i den icke-primära nodtypen.

**Antal VM-instanser:** För produktionsarbetsbelastningar som är tillståndskänsliga, rekommenderas att du kör dem med en lägsta och mål replikantal på 5. Det innebär att i stabilt tillstånd att avslutas med en replik (från en replikuppsättning) i varje feldomän och uppgraderingsdomän. Hela tillförlitlighet nivån konceptet för den primära nodtypen är ett sätt att ange den här inställningen för systemtjänster. Detsamma gäller så att dina tillståndskänsliga tjänster.

Så för produktionsarbetsbelastningar minsta rekommenderade icke - primära noden standardstorleken är 5, om du kör tillståndskänsliga arbetsbelastningar i den.

**SKU FÖR VIRTUELL DATOR:** Detta är nodtyp där ditt program körs, så VM-SKU du väljer för den, måste ta hänsyn till toppbelastningen som du planerar att placera i varje nod. Kapacitetsbehoven av nodtyp, bestäms av arbetsbelastning som du planerar att köra i klustret, så att vi inte kan ge du med kvalitativa vägledning för den specifika arbetsbelastningen, men här är bred riktlinjer som hjälper dig att komma igång

För produktionsarbetsbelastningar 

- Den rekommenderade VM-SKU är Standard D3 Standard D3_V2 eller motsvarande med minst 14 GB lokal SSD-lagring.
- Minsta stöds användning VM SKU är Standard D1 Standard D1_V2 eller motsvarande med minst 14 GB lokal SSD-lagring. 
- Partiell grundläggande VM SKU: er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- Standard A1-SKU stöds inte för produktionsarbetsbelastningar av prestandaskäl.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Icke-primära nodtypen - kapacitet vägledning för tillståndslösa arbetsbelastningar

Den här vägledningen för tillståndslösa arbetsbelastningar som körs på den icke-primära nodtypen.

**Antal VM-instanser:** För produktionsarbetsbelastningar som är tillståndslösa, är minsta stöds icke - primära typ nodstorleken 2. På så sätt kan du köra du två tillståndslösa instanser av ditt program så att din tjänst att överleva förlusten av en VM-instans. 

**SKU FÖR VIRTUELL DATOR:** Detta är nodtyp där ditt program körs, så VM-SKU du väljer för den, måste ta hänsyn till toppbelastningen som du planerar att placera i varje nod. Kapacitetsbehoven av nodtyp bestäms av den arbetsbelastning som du planerar att köra i klustret. Vi kan inte tillhandahålla kvalitativ vägledning för den specifika arbetsbelastningen.  Här är dock bred riktlinjer hjälper dig att komma igång.

För produktionsarbetsbelastningar 

- Den rekommenderade VM-SKU är Standard D3 Standard D3_V2 eller motsvarande. 
- Minsta stöds användning VM SKU är Standard D1 Standard D1_V2 eller motsvarande. 
- Partiell grundläggande VM SKU: er som Standard A0 stöds inte för produktionsarbetsbelastningar.
- Standard A1-SKU stöds inte för produktionsarbetsbelastningar av prestandaskäl.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
När du slutför din kapacitetsplanering och konfigurera ett kluster kan du läsa följande:

* [Service Fabric-kluster-säkerhet](service-fabric-cluster-security.md)
* [Skalning av Service Fabric-klustret](service-fabric-cluster-scaling.md)
* [Planeringen för katastrofåterställning](service-fabric-disaster-recovery.md)
* [Ange relationen mellan Nodetypes och VM-skalningsuppsättning](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

---
title: Kapacitetsplanering för Service Fabric-appar
description: Beskriver hur du identifierar antalet beräkningsnoder som krävs för ett Service Fabric-program
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377216"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitetsplanering för Service Fabric-applikationer
Det här dokumentet lär dig hur du uppskattar mängden resurser (processorer, RAM- och disklagring) som du behöver för att köra dina Azure Service Fabric-program. Det är vanligt att resurskraven ändras med tiden. Du behöver vanligtvis få resurser när du utvecklar/ testar din tjänst och kräver sedan mer resurser när du går in i produktionen och ditt program växer i popularitet. När du utformar ditt program, tänka igenom de långsiktiga kraven och göra val som gör att din tjänst kan skalas för att möta hög kundefterfrågan.

 När du skapar ett Service Fabric-kluster bestämmer du vilka typer av virtuella datorer som utgör klustret. Varje virtuell dator levereras med en begränsad mängd resurser i form av processorer (kärnor och hastighet), nätverksbandbredd, RAM- och disklagring. När tjänsten växer med tiden kan du uppgradera till virtuella datorer som erbjuder större resurser och/eller lägger till fler virtuella datorer i klustret. För att göra det senare måste du skapa din tjänst från början så att den kan dra nytta av nya virtuella datorer som läggs till dynamiskt i klustret.

Vissa tjänster hanterar liten eller ingen data om de virtuella datorerna själva. Kapacitetsplanering för dessa tjänster bör därför i första hand inriktas på prestanda, vilket innebär att välja lämpliga processorer (kärnor och hastighet) för de virtuella datorerna. Dessutom bör du överväga nätverksbandbredd, inklusive hur ofta nätverksöverföringar sker och hur mycket data som överförs. Om tjänsten behöver fungera bra när tjänstanvändningen ökar kan du lägga till fler virtuella datorer i klustret och belastningsbalansen som nätverksbegäranden i alla virtuella datorer.

För tjänster som hanterar stora mängder data på de virtuella datorerna bör kapacitetsplanering i första hand inriktas på storlek. Därför bör du noga överväga kapaciteten för den virtuella datorns RAM- och disklagring. Systemet för hantering av virtuellt minne i Windows gör att diskutrymmet ser ut som RAM-minne till programkod. Dessutom ger Service Fabric-körningen smart växling som bara håller heta data i minnet och flyttar kalla data till disk. Program kan alltså använda mer minne än vad som är fysiskt tillgängligt på den virtuella datorn. Att ha mer RAM ökar helt enkelt prestanda, eftersom den virtuella datorn kan hålla mer disklagring i RAM. Den virtuella datorn som du väljer bör ha en disk som är tillräckligt stor för att lagra de data som du vill ha på den virtuella datorn. På samma sätt bör den virtuella datorn ha tillräckligt med RAM för att ge dig den prestanda du önskar. Om tjänstens data växer med tiden kan du lägga till fler virtuella datorer i klustret och partitionera data över alla virtuella datorer.

## <a name="determine-how-many-nodes-you-need"></a>Ta reda på hur många noder du behöver
Med partitionering av tjänsten kan du skala ut tjänstens data. Mer information om partitionering finns i [Partitionering Service Fabric](service-fabric-concepts-partitioning.md). Varje partition måste passa in i en enda virtuell dator, men flera (små) partitioner kan placeras på en enda virtuell dator. Så, med fler små partitioner ger dig större flexibilitet än att ha några större partitioner. Avvägningen är att om du har många partitioner ökar Service Fabric-omkostnaderna och du kan inte utföra utförda åtgärder över partitioner. Det finns också mer potentiell nätverkstrafik om din tjänstkod ofta behöver komma åt delar av data som finns i olika partitioner. När du utformar din tjänst bör du noga överväga dessa fördelar och nackdelar för att komma fram till en effektiv partitioneringsstrategi.

Anta att ditt program har en enda tillståndskänslig tjänst som har en butiksstorlek som du förväntar dig att växa till DB_Size GB om ett år. Du är villig att lägga till fler program (och partitioner) som du upplever tillväxt efter det året.  Replikeringsfaktorn (RF), som bestämmer antalet repliker för tjänsten påverkar det totala DB_Size. Den totala DB_Size för alla repliker är replikeringsfaktorn multiplicerad med DB_Size.  Node_Size representerar diskutrymmet/RAM-minnet per nod som du vill använda för tjänsten. För bästa prestanda bör DB_Size passa in i minnet i klustret, och en Node_Size som finns runt RAM-minnet för den virtuella datorn bör väljas. Genom att allokera en Node_Size som är större än RAM-kapaciteten förlitar du dig på växlingen som tillhandahålls av Service Fabric-körningen. Därför kanske din prestanda inte är optimal om hela dina data anses vara heta (sedan dess är data sökt in/ ut). Men för många tjänster där endast en bråkdel av data är heta, är det mer kostnadseffektivt.

Antalet noder som krävs för maximal prestanda kan beräknas på följande sätt:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Redogöra för tillväxt
Du kanske vill beräkna antalet noder baserat på DB_Size som du förväntar dig att tjänsten ska växa till, utöver de DB_Size som du påbörjade med. Öka sedan antalet noder när tjänsten växer så att du inte överetablerar antalet noder. Men antalet partitioner bör baseras på antalet noder som behövs när du kör tjänsten med maximal tillväxt.

Det är bra att ha några extra maskiner tillgängliga när som helst så att du kan hantera oväntade spikar eller fel (till exempel om några virtuella datorer går ner).  Medan den extra kapaciteten bör bestämmas med hjälp av dina förväntade spikar, är en utgångspunkt att reservera några extra virtuella datorer (5-10 procent extra).

Föregående förutsätter en enda tillståndskänslig tjänst. Om du har mer än en tillståndskänslig tjänst måste du lägga till DB_Size som är associerade med de andra tjänsterna i ekvationen. Alternativt kan du beräkna antalet noder separat för varje tillståndskänslig tjänst.  Tjänsten kan ha repliker eller partitioner som inte är balanserade. Tänk på att partitioner också kan ha mer data än andra. Mer information om partitionering finns i [partitioneringsartikel om metodtips](service-fabric-concepts-partitioning.md). Den föregående ekvationen är dock partitions- och replikoberoende, eftersom Service Fabric säkerställer att replikerna sprids ut mellan noderna på ett optimerat sätt.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Använda ett kalkylblad för kostnadsberäkning
Nu ska vi sätta några riktiga siffror i formeln. Ett [exempel kalkylblad](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) visar hur man planerar kapaciteten för ett program som innehåller tre typer av dataobjekt. För varje objekt approximerar vi dess storlek och hur många objekt vi förväntar oss att ha. Vi väljer också hur många repliker vi vill ha av varje objekttyp. Kalkylbladet beräknar den totala mängden minne som ska lagras i klustret.

Sedan anger vi en VM-storlek och månadskostnad. Baserat på storleken på den virtuella datorn anger kalkylbladet det minsta antalet partitioner som du måste använda för att dela upp dina data så att de passar fysiskt på noderna. Du kanske vill ha ett större antal partitioner för att tillgodose programmets specifika beräknings- och nätverkstrafikbehov. Kalkylbladet visar antalet partitioner som hanterar användarprofilobjekten har ökat från ett till sex.

Nu, baserat på all denna information, visar kalkylbladet att du fysiskt kan få alla data med önskade partitioner och repliker på ett 26-nodskluster. Det här klustret skulle dock vara tätt packat, så du kanske vill ha ytterligare noder för att hantera nodfel och uppgraderingar. Kalkylbladet visar också att fler än 57 noder inte ger något ytterligare värde eftersom du skulle ha tomma noder. Återigen kanske du vill gå över 57 noder ändå för att rymma nodfel och uppgraderingar. Du kan justera kalkylbladet så att det matchar programmets specifika behov.   

![Kalkylblad för kostnadsberäkning][Image1]

## <a name="next-steps"></a>Nästa steg
Kolla [in Partitioning Service Fabric-tjänster][10] om du vill veta mer om partitionering av tjänsten.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

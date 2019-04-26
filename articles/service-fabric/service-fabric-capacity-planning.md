---
title: Kapacitetsplanering för Service Fabric-appar | Microsoft Docs
description: Beskriver hur du identifierar antalet beräkningsnoder som krävs för en Service Fabric-program
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d7ca566b86ed79aa773d7af2553223c79ed9944a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342020"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitetsplanering för Service Fabric-program
Det här dokumentet lär du dig hur du kan beräkna mängden resurser (processorer, RAM-minne, disk storage) måste du köra Azure Service Fabric-program. Det är vanligt att resurskraven att ändras med tiden. Du kan normalt kräver några resurser som du utvecklar och testning i din tjänst och sedan kräver mer resurser när du övergår till produktion och ditt program ökar i popularitet. När du utformar ditt program ska tänka igenom de långsiktiga kraven och fatta beslut som kan skalas för att uppfylla hög kundernas efterfrågan tjänsten.

 När du skapar ett Service Fabric-kluster kan bestämma du vilka typer av virtuella datorer (VM) ingår i klustret. Varje virtuell dator levereras med en begränsad del av resurser i form av CPU: er (kärnor och hastighet), nätverkets bandbredd, RAM-minne och diskutrymme. När din tjänst växer med tiden kan uppgradera du till virtuella datorer som erbjuder större resurser och/eller lägga till fler virtuella datorer i klustret. Om du vill göra det senare måste du skapa din tjänst först så att den kan dra nytta av nya virtuella datorer dynamiskt läggs till i klustret.

Vissa tjänster hantera lite till några data på de virtuella datorerna själva. Kapacitetsplanering för dessa tjänster bör därför fokuserar främst på prestanda, vilket innebär att välja lämpliga processorer (kärnor och hastighet) för de virtuella datorerna. Dessutom bör du överväga nätverksbandbredd, inklusive hur ofta network-dataöverföringar inträffar och hur mycket data överförs. Om tjänsten behöver utföra samt service användning ökar, kan du lägga till fler virtuella datorer i klustret och belastningsutjämning nätverket begäranden för samtliga virtuella datorer.

För tjänster som hanterar stora mängder data på de virtuella datorerna, bör kapacitetsplanering Fokusera främst på storleken. Därför bör du noga överväga kapaciteten för den Virtuella datorns RAM-minne och diskutrymme. Hanteringssystem för virtuellt minne i Windows gör diskutrymme se ut RAM-minne för att programkod. Service Fabric-körningen tillhandahåller dessutom smart växling som endast frekventa data i minnet och flytta den kalla data till disk. Program kan därmed använda mer minne än vad som är fysiskt tillgängligt på den virtuella datorn. Med mer RAM-minne bara ökar prestanda, eftersom den virtuella datorn kan behålla mer disklagring i RAM-minne. Den virtuella datorn som du väljer ska ha en disk som är tillräckligt stor för att lagra de data som du vill på den virtuella datorn. På samma sätt kan ska den virtuella datorn ha tillräckligt med RAM-minne för att ge dig den prestanda du önskar. Om din tjänst data växer med tiden kan du lägga till fler virtuella datorer till klustret och partitionera data för samtliga virtuella datorer.

## <a name="determine-how-many-nodes-you-need"></a>Fastställa hur många noder du behöver
Partitionering din tjänst kan du skala ut din tjänsts data. Mer information om partitionering finns i [partitionering Service Fabric](service-fabric-concepts-partitioning.md). Varje partition måste rymmas inom en enskild virtuell dator, men flera (liten) partitioner kan placeras på en enskild virtuell dator. Därför får har flera små partitioner du större flexibilitet än med några få större partitioner. En kompromiss är att med många olika partitioner ökar Service Fabric-omkostnader och du kan inte utföra överförda åtgärder över partitioner. Det finns också flera potentiella nätverkstrafik om koden för tjänsten behöver ofta åtkomst till delar av data som inhyses i olika partitioner. När du utformar din tjänst, bör du noga överväga dessa- och nackdelar att komma till en effektiv strategi för partitionering.

Anta att ditt program har en tillståndskänslig tjänst som har en store-storlek som du förväntar dig att växa till DB_Size GB under ett år. Du är beredd att lägga till fler program (och partitioner) som du får tillväxt utöver det aktuella året.  Replikeringsfaktor (RF), som anger antalet repliker för din tjänst påverkar den totala DB_Size. Totalt antal DB_Size över alla repliker är Replikeringsfaktorn multiplicerat med DB_Size.  Node_Size representerar disk utrymme/RAM per nod som du vill använda för din tjänst. DB_Size ska rymmas i minnet i klustret för bästa prestanda och en Node_Size som gäller ram-MINNET på den virtuella datorn bör väljas. Genom att allokera en Node_Size som är större än RAM-kapacitet lita du på sidindelning som tillhandahålls av Service Fabric-körningen. Din prestanda kanske därför inte är optimalt om hela data anses vara frekvent (sedan dess data är paged in/ut). För många tjänster där bara en bråkdel av data frekvent, är det dock mer kostnadseffektiv.

Antalet noder som krävs för maximal prestanda kan beräknas enligt följande:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Konto för tillväxt
Du kanske vill beräkna antalet noder baserat på DB_Size som du förväntar dig att växa till, förutom DB_Size som du började med tjänsten. Sedan, utöka antalet noder när din tjänst växer så att du inte behöver etablera antalet noder. Men antalet partitioner ska baseras på antalet noder som behövs när du kör din tjänst vid maximal tillväxt.

Det är bra om du har några extra datorer som är tillgängliga när som helst så att du kan hantera alla oväntade toppar och fel (till exempel om några virtuella datorer går).  Extra kapacitet ska fastställas med hjälp av ditt förväntade toppar, en startpunkt är att reservera några extra virtuella datorer (5 – 10 procent extra).

Föregående förutsätter att en enda tillståndskänslig tjänst. Om du har mer än en tillståndskänslig tjänst, måste du lägga till DB_Size som är associerade med de andra tjänsterna till formeln. Du kan också beräkna antalet noder separat för varje tillståndskänslig tjänst.  Tjänsten kan ha repliker eller partitioner som inte är balanserade. Tänk på att partitioner kan också ha mer data än andra. Mer information om partitionering finns i [partitionering artikel om bästa praxis](service-fabric-concepts-partitioning.md). Föregående formeln är dock partition och repliken oberoende eftersom Service Fabric ser till att replikerna är fördelade mellan noderna i ett optimerat sätt.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Använd ett kalkylblad för beräkningen
Nu kan vi sätta några verkliga siffror i formeln. En [exempel kalkylblad](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) visar hur du planerar kapaciteten för ett program som innehåller tre typer av dataobjekt. För varje objekt vi göra en uppskattning av dess storlek och hur många objekt som vi tror att det finns. Vi kan också välja hur många kopior som vi vill för varje objekttyp. Beräknas den totala mängden minne som ska lagras i klustret.

Vi anger sedan ett VM-storlek och månadskostnad. Baserat på virtuella datorstorlek, anger kalkylbladet det minsta antalet partitioner måste du använda för att dela dina data fysiskt efter på noderna. Du kanske vill ha ett större antal partitioner för att hantera ditt programs specifika beräkning och nätverkstrafiken måste. Kalkylbladet visar antalet partitioner som hanterar användarobjekt för profilen har ökat från en till sex.

Baserat på den här informationen i kalkylbladet visas nu att du kan få alla data med önskad partitionerna och replikerna fysiskt på ett kluster med 26-noder. Men skulle det här klustret tätt intill packas, så du bör vissa ytterligare noder för hantering av nodfel och uppgraderingar. Kalkylbladet visar också att att mer än 57 noder ger inga ytterligare värde eftersom du ha tomma noder. Igen, kan du gå över 57 noder ändå för att hantera nodfel och uppgraderingar. Du kan justera kalkylbladet så att den matchar ditt programs specifika behov.   

![Kalkylblad för beräkningen][Image1]

## <a name="next-steps"></a>Nästa steg
Kolla in [partitionering Service Fabric-tjänster] [ 10] mer information om partitionering din tjänst.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

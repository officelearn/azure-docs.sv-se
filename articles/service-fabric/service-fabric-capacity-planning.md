---
title: "Kapacitetsplanering för Service Fabric-appar | Microsoft Docs"
description: "Beskriver hur du identifierar antalet compute-noder som krävs för ett Service Fabric-program"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: 
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: fc98bdd8b3597810b0c07563af507e93c611f769
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitetsplanering för Service Fabric-program
Det här dokumentet lär du dig hur du beräkna hur mycket resurser (processorer, minne, disk och lagring) måste du köra dina Azure Service Fabric-program. Det är vanligt att resurskraven att förändras över tid. Du kräver vanligtvis några resurser som du utvecklar och testning i tjänsten och sedan kräver mer resurser som du gå till produktion och programmet växer i större utsträckning. När du skapar ditt program kan tänka igenom de långsiktiga kraven och fatta beslut som gör att din tjänst ska skalas för att uppfylla hög kundernas behov.

 När du skapar ett Service Fabric-kluster kan bestämma du vilka typer av virtuella datorer (VM) utgör klustret. Varje virtuell dator har en begränsad mängd resurser i form av CPU (kärnor och hastighet), nätverkets bandbredd, RAM-minne och diskutrymme. När din tjänst växer med tiden kan uppgradera du till virtuella datorer som erbjuder större resurser och/eller lägga till fler virtuella datorer i klustret. Om du vill göra det senare, måste du skapa din tjänst först så att den kan dra nytta av nya virtuella datorer som hämta läggs till i klustret dynamiskt.

Vissa tjänster hantera lite eller inga data på virtuella datorerna. Kapacitetsplanering för dessa tjänster bör därför fokuserar huvudsakligen på prestanda, vilket innebär att välja lämplig CPU (kärnor och hastighet) på de virtuella datorerna. Dessutom bör du bandbredd i nätverket, inklusive hur ofta nätverket överföringar sker och hur mycket data överförs. Om din tjänst måste utföra såväl som tjänsten Minnesanvändningen ökar, du kan lägga till flera virtuella datorer till klustret och Läs in balansen nätverket begär över alla virtuella datorer.

För tjänster som hanterar stora mängder data på virtuella datorer, bör kapacitetsplanering fokuserar främst på storleken. Därför bör du noga överväga kapaciteten för den Virtuella datorns RAM-minne och diskutrymme. Hanteringssystemet virtuellt minne i Windows gör diskutrymme se ut RAM-minne för att programkod. Dessutom ger Service Fabric runtime smart växling som endast varm data i minnet och flytta cold data till disk. Program kan därmed använda mer minne än vad som är fysiskt tillgängligt på den virtuella datorn. Med mer RAM-minne bara ökar prestanda, eftersom den virtuella datorn kan ha flera disklagring i RAM-minne. Den virtuella datorn som du väljer måste ha en disk som är tillräckligt stor för att lagra de data som du vill använda på den virtuella datorn. På liknande sätt ska den virtuella datorn ha tillräckligt med RAM-minne för att ge dig den prestanda som du önskar. Din tjänst data växer över tid, kan du lägga till fler virtuella datorer till klustret och partition data över alla virtuella datorer.

## <a name="determine-how-many-nodes-you-need"></a>Fastställa hur många noder du behöver
Partitionering din tjänst kan du skala upp din tjänst data. Mer information om partitionering finns [partitionering Service Fabric](service-fabric-concepts-partitioning.md). Varje partition måste få plats i en enda virtuell dator, men flera (små) partitioner kan placeras på en enda virtuell dator. Därför ger har flera mindre partitioner dig större flexibilitet än att ha några större partitioner. En kompromiss är att ha många partitioner ökar Service Fabric kostnader och du kan inte utföra överförda åtgärder mellan partitioner. Det finns också flera potentiella nätverkstrafik om koden för tjänsten behöver ofta åtkomst till delar av data som finns i olika partitioner. När du utformar din tjänst kan bör du noggrant dessa- och nackdelar för att få fram en effektiv partitionering strategi.

Anta att programmet har en enda tillståndskänslig tjänst som har en store-storlek som du förväntar dig att växa till DB_Size GB i ett år. Du är beredd att lägga till fler program (och partitioner) som uppstår tillväxt utöver det aktuella året.  Replikering faktorn (RF) som anger antalet repliker för din tjänst påverkar den totala DB_Size. Totalt antal DB_Size över alla repliker är replikering faktorn multiplicerat med DB_Size.  Node_Size representerar disk space/RAM per nod som du vill använda för din tjänst. För bästa prestanda i DB_Size bör ryms i minnet i klustret och en Node_Size är runt ram-MINNET på den virtuella datorn bör väljas. Genom att allokera en Node_Size som är större än kapaciteten som RAM-minne, måste sidindelning som tillhandahålls av Service Fabric-körningen. Prestandan kanske därför inte är optimalt om din hela data ska anses vara varm (sedan dess data är växlingsbart systemminne in/ut). För många tjänster där endast en del av data är hot, är det dock mer kostnadseffektivt.

Antalet noder som krävs för maximala prestanda kan beräknas enligt följande:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Konto för tillväxt
Du kanske vill beräkna antalet noder baserat på DB_Size som du förväntar dig att växa till, förutom DB_Size som du började med tjänsten. Utöka sedan antalet noder i takt med din tjänst växer så att du inte överbelasta antalet noder. Men antalet partitioner ska baseras på antalet noder som behövs när du kör din tjänst på högsta tillväxt.

Det är bra att ha vissa extra datorer som är tillgängliga när som helst så att du kan hantera alla oväntat toppar eller fel (till exempel om några virtuella datorer gå).  Extra kapacitet ska fastställas med hjälp av din förväntade toppar, utgångspunkt är att reservera några extra VM: ar (5-10 procent extra).

Den föregående förutsätter en enda tillståndskänslig tjänst. Om du har mer än en tillståndskänslig service måste du lägga till DB_Size som är associerade med andra tjänster till formeln. Alternativt kan du beräkna antalet noder separat för varje tillståndskänslig service.  Tjänsten kan ha repliker eller partitioner som inte är Balanserat. Tänk på att partitioner kan också ha fler data än andra. Mer information om partitionering finns [partitionering artikel om bästa praxis](service-fabric-concepts-partitioning.md). Föregående formeln är dock partition och repliken storleksoberoende eftersom Service Fabric ser till att replikerna sprids bland noder på ett optimerat sätt.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Använda ett kalkylblad för beräkning
Nu ska vi lägga reellt tal i formeln. En [exempel kalkylblad](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) visar hur du planerar kapaciteten för ett program som innehåller tre typer av dataobjekt. Vi ungefär storleken och hur många objekt som vi räknar med att ha för varje objekt. Vi kan också välja hur många repliker som vi vill för varje objekttyp. Kalkylbladet beräknar den totala mängden minne som ska lagras i klustret.

Vi ange sedan ett VM-storlek och månadskostnaden. Baserat på VM-storlek, anger kalkylbladet det minsta antalet partitioner måste du använda för att dela dina data fysiskt plats på noderna. Du kanske vill ha ett större antal partitioner för att hantera ditt programs specifika beräkning och nätverkstrafiken måste. Kalkylbladet visar antalet partitioner som hanterar profil användarobjekt har ökat från en till sex.

Baserat på den här informationen i kalkylbladet innehåller nu att du fysiskt gick att hämta alla data med önskad partitioner och repliker på ett kluster med noder 26. Men skulle det här klustret tätt packas, så du kan vissa ytterligare noder för nodfel och uppgraderingar. Kalkylbladet visar också att med mer än 57 noder ger inga ytterligare värde eftersom du ha tomma noder. Igen, kanske du vill gå över 57 noder ändå för att passa nodfel och uppgraderingar. Du kan justera kalkylblad för att matcha ditt programs specifika behov.   

![Kalkylblad för beräkningen][Image1]

## <a name="next-steps"></a>Nästa steg
Checka ut [partitionering Service Fabric services] [ 10] för mer information om partitionering din tjänst.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

---
title: Frågelagringsscenarier – Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs några scenarier för Query Store i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768358"
---
# <a name="usage-scenarios-for-query-store"></a>Användningsscenarier för Query Store

**Gäller:** Azure-databas för PostgreSQL - Single Server version 9.6, 10, 11

Du kan använda Query Store i en mängd olika scenarier där det är viktigt att spåra och underhålla förutsägbara arbetsbelastningsprestanda. Överväg följande exempel: 
- Identifiera och justera dyraste frågor 
- A/B-testning 
- Hålla prestanda stabil under uppgraderingar 
- Identifiera och förbättra ad hoc-arbetsbelastningar 

## <a name="identify-and-tune-expensive-queries"></a>Identifiera och ställa in dyra frågor 

### <a name="identify-longest-running-queries"></a>Identifiera tidskrävande frågor 
Använd vyn [Frågeprestanda insikt](concepts-query-performance-insight.md) i Azure-portalen för att snabbt identifiera de längsta frågorna som körs. Dessa frågor tenderar vanligtvis att förbruka en betydande mängd resurser. Optimera dina längsta frågor kan förbättra prestanda genom att frigöra resurser för användning av andra frågor som körs på ditt system. 

### <a name="target-queries-with-performance-deltas"></a>Rikta frågor med prestandadelta 
Query Store segmenterar prestandadata i tidsfönster så att du kan spåra en frågas prestanda över tid. Detta hjälper dig att identifiera exakt vilka frågor som bidrar till en ökning av den totala tiden. Därför kan du göra riktad felsökning av din arbetsbelastning.

### <a name="tuning-expensive-queries"></a>Justera dyra frågor 
När du identifierar en fråga med underoptimal prestanda beror den åtgärd du vidtar på problemets natur: 
- Använd [Prestandarekommendationer](concepts-performance-recommendations.md) för att avgöra om det finns några föreslagna index. Om ja, skapa indexet och använd sedan Query Store för att utvärdera frågeprestanda när du har skapat indexet. 
- Kontrollera att statistiken är uppdaterad för de underliggande tabeller som används av frågan.
- Överväg att skriva om dyra frågor. Dra till exempel nytta av frågeparametrisering och minska användningen av dynamisk SQL. Implementera optimal logik när du läser data som att tillämpa datafiltrering på databassidan, inte på programsidan. 


## <a name="ab-testing"></a>A/B-testning 
Använd Query Store för att jämföra arbetsbelastningsprestanda före och efter en programändring som du planerar att introducera. Exempel på scenarier för att använda Query Store för att bedöma miljöpåverkan eller programändringen av arbetsbelastningsprestanda: 
- Distribuera en ny version av ett program. 
- Lägga till ytterligare resurser på servern. 
- Skapa saknade index i tabeller som refereras av dyra frågor. 
 
I något av dessa scenarier använder du följande arbetsflöde: 
1. Kör din arbetsbelastning med Query Store innan den planerade ändringen för att generera en prestandabaslinje. 
2. Använd programändringar vid den kontrollerade tidpunkten. 
3. Fortsätt köra arbetsbelastningen tillräckligt länge för att generera prestandaavbildning av systemet efter ändringen. 
4. Jämför resultat från före och efter ändringen. 
5. Bestäm om ändringen eller återställningen ska behållas. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifiera och förbättra ad hoc-arbetsbelastningar 
Vissa arbetsbelastningar har inte dominerande frågor som du kan ställa in för att förbättra den övergripande programprestandan. Dessa arbetsbelastningar kännetecknas vanligtvis med ett relativt stort antal unika frågor, var och en av dem förbrukar en del av systemresurser. Varje unik fråga körs sällan, så individuellt är deras körningsförbrukning inte kritisk. Å andra sidan, med tanke på att programmet genererar nya frågor hela tiden, spenderas en betydande del av systemresurserna på frågekompilering, vilket inte är optimalt. Vanligtvis inträffar den här situationen om ditt program genererar frågor (i stället för att använda lagrade procedurer eller parameteriserade frågor) eller om det är beroende av objekt-relationella mappningsramverk som genererar frågor som standard. 
 
Om du har kontroll över programkoden kan du överväga att skriva om dataåtkomstlagret för att använda lagrade procedurer eller parameteriserade frågor. Den här situationen kan dock också förbättras utan programändringar genom att tvinga fram frågeparametrarisering för hela databasen (alla frågor) eller för de enskilda frågemallarna med samma frågehh. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [metodtipsen för att använda Query Store](concepts-query-store-best-practices.md)
---
title: Användningsscenarier för Query Store i Azure Database for PostgreSQL
description: Den här artikeln beskrivs några scenarier för Query Store i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 873462354b70d13e56ca108c3257031ef34873f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563196"
---
# <a name="usage-scenarios-for-query-store"></a>Användningsscenarier för Query Store

**Gäller för:** Azure Database för PostgreSQL 9.6 och 10

Du kan använda Query Store i en mängd olika scenarier där spårnings- och bibehålla förutsägbara arbetsbelastningsprestandan är viktiga. Överväg följande exempel: 
- Identifiera och justera översta dyra frågor 
- A / B-testning 
- Att hålla prestanda stabil under uppgraderingar 
- Identifierar och förbättrar ad hoc-arbetsbelastningar 

## <a name="identify-and-tune-expensive-queries"></a>Identifiera och finjustera dyra frågor 

### <a name="identify-longest-running-queries"></a>Identifiera längsta körning av frågor 
Använd den [Query Performance Insight](concepts-query-performance-insight.md) vyn i Azure portal för att snabbt identifiera de längsta körning av frågorna. De här frågorna brukar normalt att använda en mycket resurser. Optimera dina körs längst frågor kan förbättra prestanda genom att frigöra resurser som ska användas i andra frågor som körs på datorn. 

### <a name="target-queries-with-performance-deltas"></a>Mål-frågor med prestanda deltan 
Query Store skär prestandadata i tidsfönster, så att du kan spåra frågeprestanda över tid. Detta hjälper dig att identifiera exakt vilka frågor som bidrar till ökad frågedata. Därmed kan du göra riktade felsökning av din arbetsbelastning.

### <a name="tuning-expensive-queries"></a>Justering dyra frågor 
När du har identifierat en fråga med icke-optimal prestanda kan beror den åtgärd du utför på typen av problemet: 
- Använd [Prestandarekommendationer](concepts-performance-recommendations.md) att avgöra om det finns några förslag på index. Om Ja, skapa indexet och sedan använda Query Store för att utvärdera prestanda för frågor när du har skapat indexet. 
- Se till att statistik är uppdaterad för de underliggande tabeller som används av frågan.
- Överväg att skriva om dyra frågor. Exempelvis kan dra nytta av frågeparameterisering och minska användningen av dynamisk SQL. Implementera logik som är optimala vid läsning av data som när du använder data som filtrering på databassidan visade inte på programsidan. 


## <a name="ab-testing"></a>A / B-testning 
Använd Query Store att jämföra arbetsbelastningsprestandan före och efter en ändring för program som du planerar att införa. Exempel på scenarier där Query Store för att utvärdera effekten av miljön eller program ändrar att arbetsbelastningens prestanda: 
- Börja med en ny version av ett program. 
- Lägga till ytterligare resurser på servern. 
- Skapa index som saknas på tabeller som refereras till av dyra frågor. 
 
I någon av dessa scenarier, gäller följande arbetsflöde: 
1. Kör din arbetsbelastning med Query Store före planerat att generera en baslinje för prestanda. 
2. Använd programmet ändring eller ändringar av den kontrollerade tidpunkten. 
3. Fortsätta att köra arbetsbelastningen tillräckligt länge för att generera prestanda bild av systemet efter ändringen. 
4. Jämför resultatet från innan och efter ändringen. 
5. Bestäm om du vill behålla ändring eller återställning. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifiera och förbättra ad hoc-arbetsbelastningar 
Vissa arbetsbelastningar har inte dominerande frågor som du kan finjustera för att förbättra programprestanda. Dessa arbetsbelastningar kännetecknas vanligtvis med en relativt stort antal unika frågor, var och en av dem använder en del av systemresurser. Varje unik fråga körs sällan, individuellt deras runtime-användning inte är viktigt. Å andra sidan, med hänsyn till att programmet ger dig nya frågor hela tiden kan kan en stor del av systemresurser hänföras frågekompilering som inte är optimala. Detta inträffar vanligtvis om ditt program genererar frågor (i stället för med lagrade procedurer eller parameterfrågor) eller om den är beroende av ramverk för objektrelationell mappning som genererar frågor som standard. 
 
Om du har kontroll över programkoden, kan du skriva om dataåtkomstlagret för att använda lagrade procedurer eller frågor med parametrar. Men kan den här situationen också förbättras utan ändringar i programmet genom att kräva frågeparameterisering för hela databasen (alla frågor) eller för enskild fråga mallar med samma fråga hash. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om de [bästa praxis för att använda Query Store](concepts-query-store-best-practices.md)
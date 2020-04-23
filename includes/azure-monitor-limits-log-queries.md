---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072645"
---
### <a name="general-query-limits"></a>Allmänna frågegränser

| Gräns | Beskrivning |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [Kusto-frågespråk](/azure/kusto/query/) som Azure Data Explorer. Se [Språkskillnader för Azure Monitor-loggfrågor](../articles/azure-monitor/log-query/data-explorer-difference.md) för KQL-språkelement som inte stöds i Azure Monitor. |
| Azure-regioner | Loggfrågor kan uppleva överdrivna omkostnader när data sträcker sig över Log Analytics-arbetsytor i flera Azure-regioner. Mer information [finns i Frågegränser.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Frågor om flera resurser | Maximalt antal Application Insights-resurser och Log Analytics-arbetsytor i en enda fråga som är begränsad till 100.<br>Frågan över flera resurser stöds inte i View Designer.<br>Fråga över flera resurser i loggaviseringar stöds i det nya schemalagdaQueryRules API:et.<br>Mer information finns i frågegränser för [flera resurser.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |

### <a name="user-query-throttling"></a>Begränsning av användarfrågor
Azure Monitor har flera begränsningsgränser för att skydda mot användare som skickar ett alltför stort antal frågor. Sådant beteende kan potentiellt överbelasta systemet serverda resurser och äventyra tjänstens lyhördhet. Följande gränser är utformade för att skydda kunder från avbrott och säkerställa en konsekvent servicenivå. Användaren strypning och gränser är utformade för att påverka endast extrem användning scenario och bör inte vara relevant för typisk användning.


| Mått | Gräns per användare | Beskrivning |
|:---|:---|:---|
| Samtidiga frågor | 5 | Om det redan finns 5 frågor som körs för användaren placeras alla nya frågor i en samtidig kö per användare. När en av de frågor som körs slutar hämtas nästa fråga från kön och startas. Detta inkluderar inte frågor från varningsregler.
| Tid i samtidig kö | 2,5 minuter | Om en fråga sitter i kön i mer än 2,5 minuter utan att startas, avslutas den med ett HTTP-felsvar med kod 429. |
| Totalt antal frågor i samtidighetskö | 40 | När antalet frågor i kön når 40 avvisas alla ytterligare frågor med en HTTP-felkod 429. Det här numret är ett tillägg till de 5 frågor som kan köras samtidigt. |
| Frågehastighet | 200 frågor per 30 sekunder | Det här är den totala hastighet som frågor kan skickas av en enskild användare till alla arbetsytor.  Den här gränsen gäller för programmatiska frågor eller frågor som initierats av visualiseringsdelar som Azure-instrumentpaneler och sammanfattningssidan för Logganalysarbetsyta. |

- Optimera dina frågor enligt beskrivningen i [Optimera loggfrågor i Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Instrumentpaneler och arbetsböcker kan innehålla flera frågor i en enda vy som genererar en explosion av frågor varje gång de läses in eller uppdateras. Överväg att dela upp dem i flera vyer som läses in på begäran. 
- I Power BI kan du överväga att extrahera endast aggregerade resultat i stället för råloggar.
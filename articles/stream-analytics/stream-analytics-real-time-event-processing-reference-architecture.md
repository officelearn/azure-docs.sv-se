---
title: Händelsebearbetning i realtid med Azure Stream Analytics
description: I den här artikeln beskrivs referensarkitekturen för att uppnå händelsebearbetning och analys i realtid med Hjälp av Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431488"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referensarkitektur: Händelsebearbetning i realtid med Microsoft Azure Stream Analytics
Referensarkitekturen för händelsebearbetning i realtid med Azure Stream Analytics är avsedd att tillhandahålla en allmän skiss för distribution av en realtidsplattform som en tjänst (PaaS) strömhanteringslösning med Microsoft Azure.

## <a name="summary"></a>Sammanfattning
Traditionellt har analyslösningar baserats på funktioner som ETL (utvinning, transformering, inläsning) och datalagring, där data lagras före analys. Ändra krav, inklusive snabbare ankommande data, driver denna befintliga modell till gränsen. Möjligheten att analysera data i rörliga strömmar före lagring är en lösning, och även om det inte är en ny funktion, har metoden inte använts i stor utsträckning över alla branschvertikarer. 

Microsoft Azure tillhandahåller en omfattande katalog med analystekniker som kan stödja en rad olika lösningsscenarier och krav. Att välja vilka Azure-tjänster som ska distribueras för en heltäckande lösning kan vara en utmaning med tanke på bredden på erbjudanden. Det här dokumentet är utformat för att beskriva funktionerna och driften av de olika Azure-tjänsterna som stöder en lösning för händelseströmning. Det förklarar också några av de scenarier där kunderna kan dra nytta av denna typ av metod.

## <a name="contents"></a>Innehåll
* Sammanfattning
* Introduktion till Realtidsanalys
* Värdeerbjudande för realtidsdata i Azure
* Vanliga scenarier för realtidsanalys
* Arkitektur och komponenter
  * Datakällor
  * Lager för dataintegrering
  * Analyslager i realtid
  * Lager för datalagring
  * Lager för presentation/förbrukning
* Slutsats

**Författare:** Charles Feddersen, lösningsarkitekt, Data Insights Center of Excellence, Microsoft Corporation

**Publicerad:** I januari 2015

**Revidering:** 1,0

**Ladda ned:** [Händelsebearbetning i realtid med Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Få hjälp
Om du vill ha mer hjälp kan du prova [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)


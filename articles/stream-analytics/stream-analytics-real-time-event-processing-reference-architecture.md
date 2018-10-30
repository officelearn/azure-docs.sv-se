---
title: Händelsebearbetning med i realtid med hjälp av Azure Stream Analytics händelsebearbetning
description: Den här artikeln beskriver Referensarkitektur för att uppnå händelsebearbetning i realtid och analytics med hjälp av Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 1195ab125749cd211a35d7ff00c4f418dbd59f62
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232434"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referensarkitektur: händelsebearbetning i realtid med Microsoft Azure Stream Analytics
Referensarkitektur för händelsebearbetning i realtid med Azure Stream Analytics är avsedd att ge en allmän skiss för att distribuera en i realtid plattform som en tjänst (PaaS) bearbetning av dataströmmen lösning med Microsoft Azure.

## <a name="summary"></a>Sammanfattning
Traditionellt har baseras Analyslösningar på funktioner som ETL (extract, transform, load) och datalagring, där data lagras före analys. Ändrade krav, inklusive mer snabbt inkommande data, skickar den här befintliga modellen med gränsen. Möjligheten att analysera data i flytta strömmar innan storage är en lösning och det är inte en ny funktion, metoden har inte tagits brett över alla vertikala branscher. 

Microsoft Azure tillhandahåller en omfattande katalog med analytics-tekniker som stöder en matris med olika lösningsscenarier och krav. Att välja vilka Azure-tjänster att distribuera för en slutpunkt till slutpunkt-lösning kan vara en utmaning som gett bredden av erbjudanden. Det här dokumentet är avsett att beskrivs de interoperation olika Azure-tjänster som har stöd för en lösning för direktuppspelning. Den förklarar också några scenarier där kunder kan dra nytta av den här typen av metoden.

## <a name="contents"></a>Innehåll
* Sammanfattning
* Introduktion till analys i realtid
* Förslagsvärde realtidsdata i Azure
* Vanliga scenarier för analys i realtid
* Arkitektur och komponenter
  * Datakällor
  * Dataintegrering lager
  * Analys i realtid lager
  * Datalager för lagring
  * Presentation / förbrukning Layer
* Sammanfattning

**Skapad av:** Charles Feddersen, Lösningsarkitekt, Insights Datacenter av expertcenter, Microsoft Corporation

**Publicerad:** januari 2015

**Revision:** 1.0

**Hämtning:** [händelsebearbetning i realtid med Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Få hjälp
För mer hjälp kan du prova den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


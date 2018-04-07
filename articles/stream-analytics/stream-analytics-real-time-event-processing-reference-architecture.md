---
title: Realtid händelsebearbetning med Azure Stream Analytics händelsebearbetning
description: Den här artikeln beskriver Referensarkitektur för att uppnå händelsebearbetning i realtid och analytics med hjälp av Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 8a5d426d67916e010c7fff048eebdc77b93c5c38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referera arkitektur: realtid händelsebearbetning med Microsoft Azure Stream Analytics
Referensarkitektur för realtid händelsebearbetning med Azure Stream Analytics är avsedd att ge en allmän plan för att distribuera en realtid plattform som en tjänst (PaaS) stream-bearbetning med Microsoft Azure.

## <a name="summary"></a>Sammanfattning
Traditionellt baseras Analyslösningar på funktioner som ETL (extract, transform, load) och datalagring, var data lagras före analys. Ändra krav, inklusive mer snabbt inkommande data push-installation befintliga modellen till gränsen. Möjligheten att analysera data i flytta dataströmmar innan lagring är en lösning och när den inte är en ny funktion i metoden har inte tagits brett över alla branschen lodräta annonser. 

Microsoft Azure tillhandahåller en omfattande katalog analytics tekniker som stöder ett flertal olika lösningsscenarier och krav. Att välja vilka tjänster som Azure ska distribueras för en slutpunkt till slutpunkt-lösning kan vara en utmaning beroende erbjudanden bredd. Det här dokumentet är avsett att beskrivs de interoperation för olika Azure-tjänster som stöder en lösning för direktuppspelning av händelsen. Det beskriver även några scenarier där kunder kan dra nytta av den här typen av metoden.

## <a name="contents"></a>Innehåll
* Sammanfattning
* Introduktion till analys i realtid
* Molntjänsters realtidsdata i Azure
* Vanliga scenarier för Realtidsanalys
* Arkitektur och komponenter
  * Datakällor
  * Dataintegrering lager
  * Realtidsanalys lager
  * Data Storage Layer
  * Presentation / förbrukning lager
* Sammanfattning

**Skapad av:** Charles Feddersen, Lösningsarkitekt, insikter Datacenter av utmärkt, Microsoft Corporation

**Publicerad:** januari 2015

**Revision:** 1.0

**Hämta:** [realtid händelsebearbetning med Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


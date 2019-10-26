---
title: Händelse bearbetning i real tid med hjälp av Azure Stream Analytics
description: Den här artikeln beskriver referens arkitekturen för att uppnå händelse bearbetning och analys i real tid med hjälp av Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 21a0e4e468b606ec7bb7e33bf1a616e68cd6cf50
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925100"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referens arkitektur: händelse bearbetning i real tid med Microsoft Azure Stream Analytics
Referens arkitekturen för händelse bearbetning i real tid med Azure Stream Analytics är avsedd att ge en allmän skiss för distribution av en PaaS-lösning (Platform as a Service) för real tids bearbetning med Microsoft Azure.

## <a name="summary"></a>Sammanfattning
Traditionellt har analys lösningar baserats på funktioner som ETL (extrahering, transformering, belastning) och data lager hantering, där data lagras före analys. Om du ändrar krav, inklusive snabbare data som kommer att skickas, skickas den befintliga modellen till gränsen. Möjligheten att analysera data i rörliga data strömmar före lagring är en lösning, och även om den inte är en ny funktion har metoden inte använts på ett brett sätt för alla branscher. 

Microsoft Azure tillhandahåller en omfattande katalog med analys tekniker som kan stödja en matris med olika lösnings scenarier och krav. Att välja vilka Azure-tjänster som ska distribueras för en slut punkt till slut punkt kan vara en utmaning med bredden av erbjudanden. Det här dokumentet är utformat för att beskriva funktionerna och funktionerna i de olika Azure-tjänsterna som har stöd för en händelse strömnings lösning. Här beskrivs också några av de scenarier där kunderna kan dra nytta av den här typen av metod.

## <a name="contents"></a>Innehåll
* Sammanfattning av chefer
* Introduktion till real tids analys
* Värde förslag på real tids data i Azure
* Vanliga scenarier för analys i real tid
* Arkitektur och komponenter
  * Datakällor
  * Data integrations lager
  * Analys lager i real tid
  * Data lagrings lager
  * Presentation/förbruknings lager
* Sammanfattning

**Författare:** Charles Feddersen, lösnings arkitekt, data insikter Center of expert, Microsoft Corporation

**Publicerad:** Januari 2015

**Revision:** 1,0

**Ladda ned:** [händelse bearbetning i Real tid med Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du prova [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


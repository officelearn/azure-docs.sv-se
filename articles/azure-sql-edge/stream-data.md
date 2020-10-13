---
title: Data strömning i Azure SQL Edge
description: Lär dig mer om data strömning i Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ca22b3d2c00bfef128455df4ad6b9bb6411f8a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900562"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Data strömning i Azure SQL Edge

Azure SQL Edge innehåller en inbyggd implementering av funktioner för strömning av data som kallas T-SQL streaming. Det ger data strömning, analys och händelse bearbetning i real tid för att analysera och bearbeta stora mängder data som snabbt strömmas från flera källor, samtidigt. T-SQL streaming har skapats med hjälp av samma högpresterande strömnings motor som har behörighet [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) i Microsoft Azure. Funktionen har stöd för en liknande uppsättning funktioner som erbjuds av Azure Stream Analytics som körs på gränsen.

Precis som med Stream Analytics känner T-SQL streaming igen mönster och relationer i information som har extraherats från ett antal IoT-ingångs källor, inklusive enheter, sensorer och program. Du kan använda dessa mönster för att utlösa åtgärder och initiera arbets flöden. Du kan till exempel skapa aviseringar, flödes information till en rapporterings-eller visualiserings lösning eller lagra data för senare användning. 

T-SQL streaming kan hjälpa dig att:

* Analysera telemetri strömmar från IoT-enheter i real tid.
* Använd real tids analys av data som genereras från autonoma och driv rutins lösta fordon.
* Använd fjärrövervakning och förutsägande underhåll av högpresterande industri-eller tillverknings till gångar.
* Använd avvikelse identifiering och mönster igenkänning av IoT-sensor avläsningar i ett jordbruk eller en energi grupp.

## <a name="how-does-t-sql-streaming-work"></a>Hur fungerar T-SQL streaming?

T-SQL streaming fungerar på exakt samma sätt som [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Till exempel används begreppet strömnings *jobb* för bearbetning av data strömning i real tid. 

Ett Stream Analytics-jobb består av:

- **Stream-indata**: Detta definierar de anslutningar till en data källa som data strömmen ska läsas från. Azure SQL Edge stöder för närvarande följande data ström typer:
    - Edge Hub
    - Kafka (stöd för Kafka-indata är för närvarande bara tillgängligt i Intel/AMD64-versioner av Azure SQL Edge.)

- **Strömma utdata**: Detta definierar de anslutningar till en data källa som data strömmen ska skrivas till. Azure SQL Edge har för närvarande stöd för följande strömmar av utdata
    - Edge Hub
    - SQL (SQL-utdata kan vara en lokal databas inom instansen av Azure SQL Edge, eller en fjärran sluten SQL Server eller Azure SQL Database.) 

- **Stream-fråga**: Detta definierar omvandlingen, sammansättningar, filtrering, sortering och kopplingar som ska tillämpas på indataströmmen innan de skrivs till Stream-utdata. Data ström frågan baseras på samma frågespråk som används av Stream Analytics. Mer information finns i [Stream Analytics frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> T-SQL streaming, till skillnad från Stream Analytics, stöder för närvarande inte [användning av referens data för sökningar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) eller [användning av UDF-och Uda i ett Stream-jobb](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL-direktuppspelning stöder bara en delmängd av språk området som stöds av Stream Analytics. Mer information finns i [Stream Analytics frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Begränsningar och begränsningar

Följande begränsningar och begränsningar gäller för T-SQL streaming. 

- Endast ett direkt uppspelnings jobb kan vara aktivt vid en angiven tidpunkt. Jobb som redan körs måste stoppas innan ett annat jobb startas.
- Varje körning av direkt uppspelnings jobb är en enkel tråd. Om streaming-jobbet innehåller flera frågor utvärderas varje fråga i serie ordning.
- När du har stoppat ett strömmande jobb i Azure SQL Edge kan det uppstå en fördröjning innan nästa strömmande jobb kan startas. Den här fördröjningen introduceras eftersom den underliggande strömnings processen måste stoppas som svar på begäran om att stoppa jobb och sedan startas om som svar på begäran om start jobb. 
- T-SQL strömma upp till 32 partitioner för en Kafka-dataström. Försök att konfigurera ett högre antal partitioner leder till ett fel. 

## <a name="next-steps"></a>Nästa steg

- [Skapa ett Stream Analytics jobb i Azure SQL Edge ](create-stream-analytics-job.md)
- [Visa metadata som är associerade med Stream-jobb i Azure SQL Edge ](streaming-catalog-views.md)
- [Skapa extern ström](create-external-stream-transact-sql.md)

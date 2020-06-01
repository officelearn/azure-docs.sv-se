---
title: Data strömning i Azure SQL Edge (för hands version)
description: Lär dig mer om data strömning i Azure SQL Edge (för hands version)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ea1bb05e111432a45fffa3b770e01c6da9a076b6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233128"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Data strömning i Azure SQL Edge (för hands version)

Azure SQL Edge (för hands version) innehåller två olika alternativ för att implementera data strömning. 

1. Distribuera Azure streaming Analytics Edge-jobb som skapats i Azure. Mer information om hur du distribuerar Azure streaming Analytics Edge-jobb i Azure SQL Edge hänvisa [Azure Stream Analytics-jobb](deploy-dacpac.md).
2. Med hjälp av den nya **strömnings funktionen T-SQL** för att skapa strömmande jobb i SQL Edge, utan att behöva konfigurera strömmande jobb i Azure. 

Även om det går att använda båda alternativen för att implementera data strömning i SQL Edge, rekommenderar vi att du bara använder ett. När båda används kan det finnas möjliga tävlings villkor som påverkar driften av data strömnings åtgärder.

Resten av det här dokumentet refererar till den nya funktionen, **T-SQL streaming**, som tillhandahåller data strömning, analys och händelse bearbetning i real tid för att analysera och bearbeta stora volymer med snabba strömmande data från flera källor samtidigt. *T-SQL streaming* skapas med samma högpresterande strömnings motor som ger [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) i Microsoft Azure och har stöd för en liknande uppsättning funktioner som erbjuds av Azure Stream Analytics som körs på gränsen.

Precis som med Azure Stream Analytics aktiverar T-SQL streaming igenkänning av mönster och relationer i information som har extraherats från ett antal IoT-ingångs källor, inklusive enheter, sensorer och program. Dessa mönster kan användas för att utlösa åtgärder och initiera arbets flöden, till exempel för att skapa aviseringar, matnings information till en rapporterings-eller visualiserings lösning eller lagra data för senare användning. 

Följande scenarier är exempel på när du kan använda T-SQL streaming:

* Analysera telemetri strömmar från IoT-enheter i real tid.
* Real tids analys av data som genereras från autonoma och driv rutins lösta fordon.
* Fjärrövervakning och förutsägande underhåll av hög värdes industriella eller tillverknings till gångar.
* Avvikelse identifiering och/eller mönster igenkänning av IoT-sensor avläsningar i ett jordbruk eller en energi grupp.

## <a name="how-does-t-sql-streaming-work"></a>Hur fungerar T-SQL streaming?

T-SQL streaming fungerar på exakt samma sätt som [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work), det använder till exempel begreppet strömnings jobb för bearbetning av data strömning i real tid. 

Stream Analytics-jobb består av:

- Stream-indata – en Stream-inmatning definierar de anslutningar till en data källa som data strömmen ska läsas från. Azure SQL Edge stöder för närvarande följande data ström typer:
    - Edge Hub
    - Kafka – stöd för Kafka-indata är för närvarande bara tillgängligt på Intel/AMD64-versioner av Azure SQL Edge.

- Strömma utdata – en Stream-utdata definierar de anslutningar till en data källa som data strömmen ska skrivas till. Azure SQL Edge har för närvarande stöd för följande strömmar av utdata
    - Edge Hub
    - SQL-SQL-utdata kan vara en lokal databas i SQL Edge-instansen eller en fjärran sluten SQL Server eller Azure SQL Database. 
    - Azure Blob Storage

- Stream-fråga – data Ströms frågan definierar omvandlingen, agg regeringar, filter, sortering och kopplingar som måste tillämpas på indataströmmen innan den skrivs till Stream-utdata. Data ström frågan baseras på samma frågespråk som används av Azure Stream Analytics. Mer information om Azure Stream Analytics frågespråk finns [Stream Analytics frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> T-SQL streaming, till skillnad från Azure Stream Analytics, stöder för närvarande inte [användning av referens data för sökningar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) eller [med UDF-och Uda i Stream-jobbet](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL-direktuppspelning stöder bara en delmängd av språk området som stöds av Azure Stream Analytics. Mer information om hur du Azure Stream Analytics frågespråket finns i [Stream Analytics frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Begränsningar och begränsningar

Följande begränsningar och begränsningar gäller för T-SQL streaming. 

- Endast ett direkt uppspelnings jobb kan vara aktivt när som helst. Jobb som redan körs måste stoppas innan ett annat jobb startas.
- Varje körning av direkt uppspelnings jobb är en enkel tråd. Om direkt uppspelnings jobbet innehåller flera frågor kommer varje fråga att utvärderas i serie ordning.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett Stream Analytics jobb i Azure SQL Edge (för hands version)](create-stream-analytics-job.md)
- [Visa metadata som är associerade med Stream-jobb i Azure SQL Edge (för hands version)](streaming-catalog-views.md)
- [Skapa extern ström](create-external-stream-transact-sql.md)
---
title: "Felsöka för felaktig inkommande händelser i Azure Stream Analytics | Microsoft Docs"
description: "Hur vet jag vilka händelser i min inkommande data som orsakar problem i ett Stream Analytics-jobb"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Vanliga problem i Stream Analytics och steg för att felsöka

## <a name="troubleshoot-for-malformed-input-events"></a>Felsöka för felaktig inkommande händelser

När Indataströmmen för Stream Analytics-jobbet innehåller felaktigt utformade meddelanden, gör serialisering problem. Felaktigt utformade meddelanden innehåller felaktig serialisering, till exempel parentes saknas i en JSON-objekt eller felaktig Tidsstämpelformat. När en Stream Analytics-jobbet tar emot ett felaktigt meddelande, släpper meddelandet och meddelar användare med en varning. Symbolen varning visas på den **indata** för Stream Analytics-jobbet (den här varningen logga finns så länge som jobbet är i körningstillstånd):

![Indata sida vid sida](media/stream-analytics-malformed-events/inputs_tile.png)

Du kan aktivera diagnostik loggfilerna för att visa information om varningen. Malformatted inkommande händelser, körningsloggar innehåller en post med meddelandet som ser ut som ”: meddelande: Det gick inte att deserialisera indata händelse(r) från resursen <blob URI> som json)”. 

### <a name="troubleshooting-steps"></a>Felsökningssteg

1. Gå till panelen inkommande och klicka om du vill visa varningar.
2. Panelen inkommande information visar en uppsättning varningar med information om problemet. Följande är ett exempel varning, varningsmeddelandet visas Partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Varningsmeddelande visas med förskjutning](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Om du vill hämta JSON-data som har fel format, kör CheckMalformedEvents.cs kod kan du skaffa it från den [GitHub exempel databasen](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läser partitions-Id, förskjutning och skriver ut de data som finns i förskjutningen. 

4. När du läser data kan du analysera och korrigera serialiseringsformat. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Hantera dubblerade poster när du använder Azure SQL Database som utdata för ett Stream Analytics-jobb

När du konfigurerar Azure SQL-databas som utdata till Stream Analytics-jobbet infogas masskapat poster i måltabellen. I allmänhet Azure stream analytics garanterar [minst en gång leverans]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) till utdatamottagaren, en fortfarande [uppnå exakt-en leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL utdata när SQL-tabellen har en unik begränsning har definierats. 

När unik nyckelvillkor ställs in på SQL-tabellen och det finns dubbla poster som infogas i SQL-tabellen, Azure Stream Analytics tar bort dubblettposten genom att dela data i batchar och rekursivt att infoga i batchar förrän en enda dubblettposten hittas. Om det finns många dubblettrader i din pipeline, är dela och rekursivt infogar data ignorerar dubbletter i taget en tidskrävande process. Om du ser flera felet varningsmeddelanden i din aktivitetsloggen inom den senaste timmen, är det troligt att SQL-utdata är långsammare hela jobbet. Lös problemet, bör du [konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar felet genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under bulkinfogningar och SQL Azure bara genererar ett varningsmeddelande i stället för ett fel. Azure Stream Analytics skapar inte primärnyckelfel fel längre.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX måste du ta bort och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med ALTER INDEX för ett unikt index som skiljer sig från primära nyckel/UNIQUE-begränsningen och skapas med CREATE INDEX eller INDEX definition.  
* IGNORE_DUP_KEY gäller inte för kolumnarkivindex eftersom du kan tillämpa unika sådana index.  

## <a name="next-steps"></a>Nästa steg

* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


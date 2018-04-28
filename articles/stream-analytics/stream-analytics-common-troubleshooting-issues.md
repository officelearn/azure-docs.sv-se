---
title: Vanliga problem med att felsöka i Azure Stream Analytics
description: Den här artikeln beskriver några vanliga problem i Azure Stream Analytics och steg för att felsöka dessa problem.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Vanliga problem i Stream Analytics och steg för att felsöka

## <a name="troubleshoot-malformed-input-events"></a>Felsöka felaktig inkommande händelser

 Serialisering problem orsakas när Indataströmmen för Stream Analytics-jobbet innehåller felaktigt utformade meddelanden. Ett felaktigt meddelande kan till exempel vara orsakats av en parentes som saknas eller en klammerparentes som saknas i JSON-objekt eller felaktigt tidsformat stämpel i ett tidsfält. 
 
 När en Stream Analytics-jobbet tar emot ett felaktigt meddelande från indata, släpper meddelandet och meddelar användare med en varning. Symbolen varning visas på den **indata** för Stream Analytics-jobbet (den här varningen logga finns så länge som jobbet är i körningstillstånd):

![Indata sida vid sida](media/stream-analytics-malformed-events/inputs_tile.png)

Aktivera diagnostik loggfilerna för att visa information om varningen om du vill se mer information. Felaktig inkommande händelser, körningsloggar innehåller en post med meddelandet som ser ut som ”: meddelande: gick inte att deserialisera indata händelse(r) från resursen <blob URI> som json”. 

### <a name="troubleshooting-steps"></a>Felsökningssteg

1. Gå till panelen inkommande och klicka om du vill visa varningar.

2. Panelen inkommande information visar en uppsättning varningar med information om problemet. Följande är ett exempel varning, varningsmeddelandet visas Partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Varningsmeddelande visas med förskjutning](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Kör CheckMalformedEvents.cs kod för att hämta JSON-data som har fel format. Det här exemplet är tillgängliga i den [GitHub exempel databasen](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läser partitions-ID, förskjutning och skriver ut de data som finns i förskjutningen. 

4. När du läser dessa data kan du sedan analysera och korrigera serialiseringsformatet. 

## <a name="delayed-output"></a>Fördröjd utdata

### <a name="first-output-is-delayed"></a>Första utdata är försenad
När ett Stream Analytics-jobb har startat inkommande händelser är skrivskyddade, men det kan finnas en fördröjning i utdata som skapas i vissa fall.

Stora tid-värdena i temporala frågan element kan bidra till fördröjning av utdata. Direktuppspelningsjobbet startar genom att läsa data från det senaste gången möjligt (upp till sju dagar sedan) om du vill fylla tidsramen för att skapa rätt utdata över stora tidsfönster. Under den tiden kan utdata inga förrän catch-up Läs utestående inkommande händelser är klar. Det här problemet kan ansluta när systemet uppgraderas strömmande jobb, därför att starta om jobbet. Sådana uppgraderingar inträffar vanligen när varje par månader. 

Därför försiktig när du utformar din Stream Analytics-fråga. Om du använder en stor tidsfönstret (flera timmar upp till sju dagar) för temporala element i jobbets frågesyntaxen, det kan leda till en fördröjning på första utdata när jobbet startas eller startas om.  

En lösning för den här typen av den första utdata fördröjningen är att använda parallellisering frågetekniker (partitionering data) eller Lägg till fler enheter för strömning för att förbättra systemets genomflöde tills jobbet fångar.  Mer information finns i [överväganden när du skapar Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)

Dessa faktorer som påverkar aktualitet första utdata som genereras:

1. Användning av fönsteraggregeringar (grupp av av rullande, Hopping och glidande windows)
   - För rullande eller hopping fönstret mängder genereras resultat i slutet av fönstret tidsram. 
   - För ett skjutfönster genereras resultaten när en händelse anländer till eller lämnar Hoppande fönster. 
   - Om du planerar att använda stora fönsterstorlek (> 1 timme), är det bäst att välja Hoppande eller glidande så att du kan se utdata oftare.

2. Användning av temporala kopplingar (koppla ihop med DATEDIFF)
   - Matchar genereras när när båda sidor av de matchade händelserna anländer.
   - Data som saknar en matchning (vänster yttre koppling) genereras i slutet av fönstret DATEDIFF med avseende på varje händelse på vänster sida.

3. Användning av temporala analysfunktioner (ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION)
   - För analys, utdata genereras för varje händelse finns det ingen fördröjning.

### <a name="output-falls-behind"></a>Resultatet ligger
Under normal drift för jobbet om du hittar den jobbutdata faller bakom (längre och längre svarstid), kan du hitta den de bakomliggande orsakerna genom att undersöka dessa faktorer:
- Om underordnade sink begränsas
- Om den överordnade källan begränsas
- Om standardbearbetningslogiken i frågan är beräkningsintensiva

Om du vill se detaljer, i Azure portal, väljer direktuppspelningsjobbet och väljer den **jobbet diagram**. För varje indata, finns en per partition eftersläpning händelse mått. Om eftersläpning händelse mått fortsätter att öka, är en indikator att systemresurserna är begränsad. Potentiellt som är på grund av att utdata sink-begränsning eller hög CPU. Mer information om hur du använder i jobbet diagram finns [datadrivna felsökning med hjälp av jobbet diagram](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Hantera dubblerade poster i Azure SQL Database-utdata

När du konfigurerar Azure SQL-databas som utdata till Stream Analytics-jobbet infogas masskapat poster i måltabellen. I allmänhet Azure stream analytics garanterar [minst en gång leverans]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) till utdatamottagaren, en fortfarande [uppnå exakt-en leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL utdata när SQL-tabellen har en unik begränsning har definierats. 

När unik nyckelvillkor ställs in på SQL-tabellen, och det finns dubbla poster som infogas i SQL-tabellen, Azure Stream Analytics tar du bort dubblettposten. Den delas data i batchar och rekursivt att infoga i batchar tills en enda dubblettpost hittas. Om direktuppspelningsjobbet har ett stort antal dubblettrader i den här delade och infoga processen har att ignorera dubbletter i taget, vilket är mindre effektivt och tidskrävande. Om du ser flera felet varningsmeddelanden i din aktivitetsloggen inom den senaste timmen, är det troligt att SQL-utdata är långsammare hela jobbet. 

Lös problemet, bör du [konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar felet genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under bulkinfogningar och SQL Azure bara genererar ett varningsmeddelande i stället för ett fel. Azure Stream Analytics skapar inte primärnyckelfel fel längre.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX måste du ta bort och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med ALTER INDEX för ett unikt index som skiljer sig från primära nyckel/UNIQUE-begränsningen och skapas med CREATE INDEX eller INDEX definition.  
* IGNORE_DUP_KEY gäller inte för kolumnarkivindex eftersom du kan tillämpa unika sådana index.  

## <a name="next-steps"></a>Nästa steg
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

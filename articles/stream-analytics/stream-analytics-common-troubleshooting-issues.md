---
title: Vanliga problem med att felsöka i Azure Stream Analytics
description: Den här artikeln beskriver flera vanliga problem i Azure Stream Analytics och steg för att felsöka problemen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521483"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Vanliga problem i Stream Analytics och steg för att felsöka

## <a name="troubleshoot-malformed-input-events"></a>Felsök felaktig indatahändelser

 Serialiseringsproblem orsakas när Indataströmmen för Stream Analytics-jobbet innehåller felaktig meddelanden. Till exempel ett felaktigt meddelande kan bero på en parentes saknas eller en saknas klammerparentes i JSON-objektet, eller felaktigt tidsformat stämpel i ett tidsfält. 
 
 När ett Stream Analytics-jobb tar emot ett felaktigt meddelande från indata, ignoreras meddelandet och meddelar användaren med en varning. En varningssymbol visas på den **indata** panelen för ditt Stream Analytics-jobb (den här varningen logga finns så länge som jobbet är i körningstillstånd):

![Indata-panelen på instrumentpanelen för Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivera diagnostikloggar att visa information om varningen om du vill se mer information. Felaktig inmatningshändelser innehålla loggarna för jobbkörning en post med meddelandet som ser ut som: ”Meddelande: Det gick inte att deserialisera indatahändelserna från resursen \<blob-URI > som json ”. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

1. Gå till panelen indata och klicka för att visa varningar.

2. Indatainformation panelen visar en uppsättning varningar med information om problemet. Följande är ett exempel varning, varningsmeddelandet visar Partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Indatameddelande varning med förskjutning](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Kör CheckMalformedEvents.cs kod för att få JSON-data som har fel format. Det här exemplet är tillgänglig i den [GitHub-lagringsplats med exempel](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läsningar partitions-ID, förskjutning och skriver ut de data som finns i förskjutningen. 

4. När du läser dessa data kan du sedan analysera och korrigera serialiseringsformatet.

5. Du kan också [läsa händelser från en IoT-hubb med Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Försenade utdata

### <a name="first-output-is-delayed"></a>Första utdata är försenad
När ett Stream Analytics-jobb startas, inkommande händelser läses, men det kan uppstå en fördröjning i utdata som genereras under vissa omständigheter.

Stora tid-värdena i den temporala fråga element kan bidra till utdata-fördröjning. Det direktuppspelade jobbet startar genom att läsa data från det senaste gången möjligt (upp till sju dagar sedan) så att den fyller tidsfönstret för att skapa rätt utdata över stora tidsfönster. Under denna tid har skapas inga utdata tills catch-up läsningen av utestående inkommande händelser har slutförts. Det här problemet kan ge när systemet uppgraderas direktuppspelningsjobb, så starta om jobbet. Sådana uppgraderingar inträffar vanligen en gång för varje några månader. 

Därför var försiktig när du utformar din Stream Analytics-fråga. Om du använder en lång tidsperiod (mer än timmarna, upp till sju dagar) för den temporala element i frågesyntax för jobbets kan leda till en fördröjning på första utdata när jobbet startas eller startas om.  

En lösning för den här typen av den första utdata fördröjningen är att använda parallellisering frågetekniker (partitionering av data) eller lägga till fler enheter för strömning för att förbättra dataflödet tills jobbet kommer det ikapp.  Mer information finns i [överväganden när du skapar Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)

Dessa faktorer som påverkar skyndsamhet på första utdata som genereras:

1. Användning av fönsteraggregeringar (grupp av av rullande, hoppar och glidande windows)
   - Rullande eller hoppar fönstret aggregeringar kan genereras resultat i slutet av fönstret tidsramen. 
   - För ett skjutfönster genereras resultaten när en händelse anländer till eller lämnar Hoppande fönster. 
   - Om du planerar att använda stora fönsterstorlek (> 1 timme), är det bäst att välja Hoppande eller glidande så att du kan se utdata oftare.

2. Användning av temporala kopplingar (koppla ihop med DATEDIFF)
   - Matchar genereras när när båda sidor av matchande händelser anländer.
   - Data som saknar en matchning (vänster yttre koppling) skapas i slutet av fönstret DATEDIFF med avseende på varje händelse till vänster.

3. Användning av temporala analysfunktioner (ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION)
   - För analysfunktioner, utdata genereras för varje händelse finns det ingen fördröjning.

### <a name="output-falls-behind"></a>Utdata som ligger
Under normal drift av jobbet om du hittar jobbets utdata faller bakom (längre och längre svarstid), kan du hitta den bakomliggande orsaken genom att undersöka dessa faktorer:
- Om underordnade mottagare är begränsad
- Om den överordnade källan är begränsad
- Om standardbearbetningslogiken i frågan är beräkningsintensiva

Välj det direktuppspelade jobbet om du vill se detaljer, Azure-portalen och välj den **jobbdiagram**. För varje indata, finns en per partition eftersläpning händelse mått. Om eftersläpning för händelsen mått fortsätter att öka, är det indikerar att systemresurserna är begränsade. Potentiellt som är på grund av att utdata mottagare begränsning eller hög CPU. Läs mer om hur du använder jobbdiagrammet [datadrivna felsökning med hjälp av jobbdiagrammet](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Hantera dubblerade poster i Azure SQL Database-utdata

När du konfigurerar Azure SQL-databas som utdata för ett Stream Analytics-jobb infogas har masskapat poster i måltabellen. I allmänhet Azure stream analytics garanterar [minst en leverans]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) till utdatamottagaren, kan en fortfarande [uppnå exakt – en leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL-utdata när SQL-tabellen har en unik begränsning som definierats. 

När unika nyckelvillkor ställs in på den SQL-tabellen och det finns dubbla poster som infogas i SQL-tabell, Azure Stream Analytics tar du bort dubblettposten. Den delas data i batchar och rekursivt infoga i batchar tills en enda kopia av posten hittas. Om det direktuppspelade jobbet har ett stort antal dubblettrader, den här dela och infoga processen har att ignorera dubbletter en i taget, vilket är mindre effektivt och tidskrävande. Om du ser flera felet varningsmeddelanden i din aktivitetslogg inom den senaste timmen, är det troligt att dina SQL-utdata är långsammare hela projektet. 

För att lösa problemet, bör du [konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar felet genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under bulkinfogningar och SQL Azure genererar bara ett varningsmeddelande i stället för ett fel. Azure Stream Analytics producerar inte primära felet fel längre.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX måste du ta bort och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med hjälp av ALTER INDEX för ett unikt index som skiljer sig från primär nyckel/UNIQUE-begränsning och skapas med CREATE INDEX eller INDEX.  
* Indexalternativet gäller inte för columnstore-index eftersom du inte kan framtvinga unikhet på sådana index.  

## <a name="next-steps"></a>Nästa steg
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

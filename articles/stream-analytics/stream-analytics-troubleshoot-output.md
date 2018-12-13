---
title: Felsöka Azure Stream Analytics-utdata
description: Den här artikeln beskrivs metoder för att felsöka utgående anslutningar i Azure Stream Analytics-jobb.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 92cb427149e6e6cbddfb96c6e4488017641e6482
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164916"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Felsöka Azure Stream Analytics-utdata

Den här sidan beskriver vanliga problem med utgående anslutningar och hur du felsöker och -versioner.

## <a name="output-not-produced-by-job"></a>Utdata som inte kommer från jobb 
1.  Kontrollera anslutningen till utdata genom att använda den **Testanslutningen** knappen för varje utdata.

2.  Titta på [ **Övervakningsmåtten** ](stream-analytics-monitoring.md) på den **övervakaren** fliken. Eftersom värdena aggregeras, fördröjs mått med några få minuter.
    - Om Indatahändelser > 0, jobbet ska kunna läsa indata. Om Indatahändelser inte är > 0:
      - Om du vill se om datakällan har giltiga data, kontrollera den med hjälp av [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Den här kontrollen utförs om jobbet använder Event Hub som indata.
      - Kontrollera om dataserialiseringsformatet och datakodning är som förväntat.
      - Om jobbet använder en Event Hub, kontrollerar du om meddelandets brödtext är *Null*.
      
    - Om datakonverteringsfel > 0 och stiger kan det bero på följande:
      - Händelsen utdata följer inte schemat för mål-mottagare. 
      - Händelseschemat kanske inte överensstämmer med den definierade eller förväntade händelseschemat i frågan.
      - Datatyper i vissa fält kanske i händelsen inte matchar förväntningar.
      
    - Om körningsfel > 0, innebär det att jobbet kan ta emot data men genererar fel när frågan bearbetas.
      - Du hittar felen genom att gå till den [granskningsloggar](../azure-resource-manager/resource-group-audit.md) och filtrerar på *misslyckades* status.
      
    - Om InputEvents > 0 och OutputEvents = 0, innebär det att något av följande är sant:
      - Frågebearbetningen resulterade i noll utdata-händelser.
      - Händelser eller dess fält kan vara felaktig, vilket resulterar i noll utdata efter frågebearbetning.
      - Jobbet kunde inte skicka data till utdatamottagaren av anslutnings- eller autentisering skäl.
      
    - I alla de tidigare nämnda fel fall loggmeddelandena förklara ytterligare information (inklusive vad som händer), utom i fall där frågans logik har filtrerat bort alla händelser. Om bearbetningen av flera händelser genererar fel loggar Stream Analytics de första tre felmeddelandena av samma typ inom 10 minuter till åtgärdsloggar. Den ignorerar sedan ytterligare identiska fel med meddelandet ”fel händer alltför snabbt dessa är dolt”.
    
## <a name="job-output-is-delayed"></a>Jobbutdata är försenad

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Felet varning med Azure SQL Database-utdata

När du konfigurerar Azure SQL-databas som utdata för ett Stream Analytics-jobb infogas har masskapat poster i måltabellen. I allmänhet Azure stream analytics garanterar [minst en leverans]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) till utdatamottagaren, kan en fortfarande [uppnå exakt – en leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL-utdata när SQL-tabellen har en unik begränsning som definierats. 

När unika nyckelvillkor ställs in på den SQL-tabellen och det finns dubbla poster som infogas i SQL-tabell, Azure Stream Analytics tar du bort dubblettposten. Den delas data i batchar och rekursivt infoga i batchar tills en enda kopia av posten hittas. Om det direktuppspelade jobbet har ett stort antal dubblettrader, den här dela och infoga processen har att ignorera dubbletter en i taget, vilket är mindre effektivt och tidskrävande. Om du ser flera felet varningsmeddelanden i din aktivitetslogg inom den senaste timmen, är det troligt att dina SQL-utdata är långsammare hela projektet. 

För att lösa problemet, bör du [konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar felet genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under bulkinfogningar och SQL Azure genererar bara ett varningsmeddelande i stället för ett fel. Azure Stream Analytics producerar inte primära felet fel längre.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX måste du ta bort och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med hjälp av ALTER INDEX för ett unikt index som skiljer sig från primär nyckel/UNIQUE-begränsning och skapas med CREATE INDEX eller INDEX.  
* Indexalternativet gäller inte för columnstore-index eftersom du inte kan framtvinga unikhet på sådana index.  

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

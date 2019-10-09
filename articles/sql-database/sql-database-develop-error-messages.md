---
title: SQL-felkoder-databas anslutnings fel | Microsoft Docs
description: 'Läs om SQL-felkoder för SQL Database-klient program, till exempel vanliga databas anslutnings fel, problem med databas kopiering och allmänna fel. '
keywords: SQL-felkod, åtkomst till SQL, databas anslutnings fel, SQL-felkoder
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 19febc5a0a6e4a72cfebfaecd917185538130152
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035032"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-felkoder för SQL Database klient program: Databas anslutnings fel och andra problem

Den här artikeln innehåller SQL-felkoder för SQL Database klient program, inklusive databas anslutnings fel, tillfälliga fel (kallas även tillfälliga fel), resurs styrnings fel, problem med databas kopiering, elastisk pool och andra fel. De flesta kategorier är specifika för Azure SQL Database och gäller inte för Microsoft SQL Server. Se även [system fel meddelanden](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Databas anslutnings fel, tillfälliga fel och andra tillfälliga fel

I följande tabell beskrivs SQL-felkoderna för anslutnings förlust fel och andra tillfälliga fel som du kan stöta på när programmet försöker komma åt SQL Database. För att komma igång-självstudier om hur du ansluter till Azure SQL Database, se [ansluta till Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Vanligaste databas anslutnings fel och tillfälliga fel

Azure-infrastrukturen har kapacitet att dynamiskt omkonfigurera servrar vid ökad arbetsbelastning i SQL Database-tjänsten.  Den här dynamiska funktionen kan göra ditt klientprogram tappar kontakten med SQL Database. Den här typen av fel tillstånd kallas för ett *tillfälligt fel*.

Vi rekommenderar starkt att ditt klient program har omprövnings logik så att det kan återupprätta en anslutning när du har angett den tillfälliga fel tiden för att korrigera sig själv.  Vi rekommenderar att du väntar i 5 sekunder innan ditt första försök. Försöker igen efter en fördröjning som är kortare än 5 sekunders risker med moln tjänsten. För varje efterföljande försök bör fördröjningen växa exponentiellt, upp till högst 60 sekunder.

Tillfälliga fel uppstår vanligt vis i ett av följande fel meddelanden från klient programmen:

* Databas &lt;db_name @ no__t-1 på servern &lt;Azure_instance @ no__t-3 är för närvarande inte tillgänglig. Försök att ansluta igen senare. Om problemet kvarstår kan du kontakta kund support och tillhandahålla sessionens spårnings-ID för &lt;session_id @ no__t-1
* Databas &lt;db_name @ no__t-1 på servern &lt;Azure_instance @ no__t-3 är för närvarande inte tillgänglig. Försök att ansluta igen senare. Om problemet kvarstår kan du kontakta kund support och tillhandahålla sessionens spårnings-ID för &lt;session_id @ no__t-1. (Microsoft SQL Server, Fel: 40613)
* En befintlig anslutning tvingades stänga av den fjärranslutna värden.
* System.Data.Entity.Core.EntityCommandExecutionException: Ett fel uppstod när kommando definitionen kördes. Mer information finns i meddelandet om internt undantag. ---> system. data. SqlClient. SqlException: Ett fel på transport nivå har uppstått när resultat togs emot från servern. CSP Session-Provider, fel: 19-fysisk anslutning kan inte användas)
* Ett anslutnings försök till en sekundär databas misslyckades eftersom databasen håller på att omkonfigureras och den är upptagen med att använda nya sidor i mitten av en aktiv transaktion på den primära databasen. 

Kod exempel på logik för omprövning finns i:

* [Anslutnings bibliotek för SQL Database och SQL Server](sql-database-libraries.md) 
* [Åtgärder för att åtgärda anslutnings fel och tillfälliga fel i SQL Database](sql-database-connectivity-issues.md)

En diskussion om *spärrnings perioden* för klienter som använder ADO.NET finns i [SQL Server anslutningspoolen (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Fel koder för tillfälliga fel

Följande fel är tillfälliga och bör göras om i program logiken: 

| Felkod | severity | Beskrivning |
| ---:| ---:|:--- |
| 4060 |16 |Det går inte att öppna databasen ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. Mer information finns i [fel 4000 till 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Tjänsten har påträffat ett fel när din begäran bearbetades. Försök igen. Felkod% d.<br/><br/>Du får det här felet när tjänsten är avstängd på grund av program varu-eller maskin varu uppgraderingar, maskin varu fel eller andra problem med redundansväxling. Felkoden (% d) [i meddelandet om fel 40197](sql-database-develop-error-messages.md#embedded-error-codes) ger ytterligare information om vilken typ av fel eller redundans som har inträffat. Några exempel på fel koderna är inbäddade i meddelandet om fel 40197 är 40020, 40143, 40166 och 40540.<br/><br/>Om du återansluter till SQL Database servern ansluts du automatiskt till en felfri kopia av databasen. Ditt program måste fånga fel 40197, logga den inbäddade felkoden (% d) i meddelandet för fel sökning och försöka ansluta till SQL Database tills resurserna är tillgängliga och anslutningen upprättas igen. Mer information finns i [tillfälliga fel](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Tjänsten är upptagen för tillfället. Gör om begäran efter 10 sekunder. Incident-ID:% ls. Kod:% d. Mer information finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Databasen '%.&#x2a;ls' på servern '%.&#x2a;ls' är inte tillgänglig. Försök att ansluta igen senare. Om problemet kvarstår kontaktar du kundsupport och ger dem session spårnings-ID (%.&#x2a;ls).<br/><br/> Det här felet kan inträffa om det redan finns en befintlig DAC-anslutning (Dedicated Administrator Connection) som är etablerad i databasen. Mer information finns i [tillfälliga fel](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Det går inte att behandla begäran. Det finns inte tillräckligt med resurser för att bearbeta begäran.<br/><br/>Tjänsten är upptagen för tillfället. Försök att utföra begäran senare. Mer information finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Det går inte att bearbeta Create eller Update-begäran. För många skapande-eller uppdaterings åtgärder pågår för prenumerationen% LD.<br/><br/>Tjänsten är upptagen med att bearbeta flera skapande-eller uppdaterings begär Anden för din prenumeration eller server. Förfrågningar är för närvarande blockerade för resurs optimering. Fråga [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för väntande åtgärder. Vänta tills väntande skapande-eller uppdaterings begär Anden är slutförda eller ta bort en väntande begäran och försök sedan igen senare. Mer information finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Det går inte att behandla begäran. För många åtgärder pågår för prenumerationen% LD.<br/><br/>Tjänsten är upptagen med att bearbeta flera begär Anden för den här prenumerationen. Förfrågningar är för närvarande blockerade för resurs optimering. Fråga [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för åtgärds status. Vänta tills väntande begär Anden är slutförda eller ta bort en väntande begäran och försök igen senare. Mer information finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Inloggningen till Read-Secondary misslyckades på grund av lång väntan på HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING. Repliken är inte tillgänglig för inloggning eftersom rad versioner saknas för transaktioner som var i flygning när replikeringen återvanns. Problemet kan lösas genom att återställa eller bekräfta de aktiva transaktionerna på den primära repliken. Förekomster av det här tillståndet kan minimeras genom att undvika långa Skriv transaktioner på den primära. |

## <a name="embedded-error-codes"></a>Inbäddade felkoder

Följande fel är inbäddade i den mer allmänna fel koden 40197:

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Felkod | severity | Beskrivning | 
| ---:| ---:|:---|
|  1104 |17 |TEMPDB fick slut på utrymme vid spill. Skapa utrymme genom att släppa objekt och/eller Skriv om frågan så att den använder färre rader. Om problemet kvarstår bör du överväga att uppgradera till ett högre service nivå mål.|
| 40020 |16 |Databasen är i över gången och transaktionerna avslutas.|
| 40143 |16 |Den replik som datanoden innehåller för den begärda partitionen är inte primär.|
| 40166 |16 |En omkonfiguration av CloudDB pågår och alla nya användar transaktioner avbryts.|
| 40540 |16 |Transaktionen avbröts eftersom databasen har flyttats till skrivskyddat läge. Detta är en tillfällig situation och försök sedan igen.|

Information om andra inbäddade fel hittar du genom att fråga `sys.messages`:

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Databas kopierings fel

Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md).

| Felkod | severity | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen (%.&#x2a;ls) är tillfälligt inaktiverad. |
| 40637 |16 |Skapa databas kopiering har inaktiverats för tillfället. |
| 40561 |16 |Databas kopieringen misslyckades. Käll-eller mål databasen finns inte. |
| 40562 |16 |Databas kopieringen misslyckades. Käll databasen har släppts. |
| 40563 |16 |Databas kopieringen misslyckades. Mål databasen har släppts. |
| 40564 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40565 |16 |Databas kopieringen misslyckades. Högst 1 samtidiga databas kopiering från samma källa tillåts. Släpp mål databasen och försök igen senare. |
| 40566 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40567 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40568 |16 |Databas kopieringen misslyckades. Käll databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40569 |16 |Databas kopieringen misslyckades. Mål databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40570 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |
| 40571 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |

## <a name="resource-governance-errors"></a>Resurs styrnings fel

Följande fel orsakas av överdriven användning av resurser när du arbetar med Azure SQL Database. Exempel:

* En transaktion har varit öppen för lång.
* En transaktion håller för många lås.
* Ett program förbrukar för mycket minne.
* Ett program förbrukar för mycket `TempDb`-utrymme.

Närliggande ämnen:

* Mer information finns i:
  * [Begränsningar för databas server resurser](sql-database-resource-limits-database-server.md)
  * [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)
  * [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)
  * [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)
  * [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md). 

| Felkod | severity | Beskrivning |
| ---:| ---:|:--- |
| 10928 |20 |Resurs-ID:% d. % S-gränsen för databasen är% d och har nåtts. Mer information finns i [SQL Database resurs gränser för databaser med enkel och pool](sql-database-resource-limits-database-server.md).<br/><br/>Resurs-ID: t anger den resurs som har nått gränsen. Resurs-ID = 1 för arbets trådar. För sessioner är resurs-ID = 2.<br/><br/>Mer information om det här felet och hur du löser det finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Resurs-ID:% d. % S minsta garanti är% d, max gränsen är% d och den aktuella användningen för databasen är% d. Servern är dock för närvarande upptagen för att stödja begär Anden som är större än% d för den här databasen. Resurs-ID: t anger den resurs som har nått gränsen. Resurs-ID = 1 för arbets trådar. För sessioner är resurs-ID = 2. Mer information finns i: <br/>&bull; &nbsp;[resurs gränser för databas server](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md). <br/>Annars kan du försöka igen senare. |
| 40544 |20 |Databasen har nått sin storleks kvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. För databas skalning, se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Sessionen avslutas eftersom du har en tids krävande transaktion. Försök att förkorta din transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Sessionen har avslut ATS eftersom den har fått för många lås. Försök att läsa eller ändra färre rader i en enskild transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Sessionen har avslut ATS på grund av överdriven `TEMPDB`-användning. Försök att ändra frågan för att minska den temporära tabell utrymmes användningen.<br/><br/>Om du använder tillfälliga objekt sparar du utrymme i `TEMPDB`-databasen genom att släppa tillfälliga objekt efter att de inte längre behövs i sessionen. Mer information om tempdb-användning i SQL Database finns i [tempdb-databasen i SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sessionen har avslut ATS på grund av överdriven användning av transaktions logg utrymme. Försök att ändra färre rader i en enskild transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](sql-database-use-batching-to-improve-performance.md).<br/><br/>Om du utför Mass infogningar med verktyget `bcp.exe` eller klassen `System.Data.SqlClient.SqlBulkCopy` kan du prova att använda alternativen `-b batchsize` eller `BatchSize` för att begränsa antalet rader som kopieras till servern i varje transaktion. Om du återskapar ett index med `ALTER INDEX`-instruktionen kan du prova att använda alternativet `REBUILD WITH ONLINE = ON`. Information om transaktions logg storlekar för vCore-inköps modellen finns i: <br/>&bull; &nbsp;[vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Sessionen har avslut ATS på grund av överdriven minnes användning. Försök att ändra frågan så att den bearbetar färre rader.<br/><br/>Om du minskar antalet `ORDER BY`-och `GROUP BY`-åtgärder i din Transact-SQL-kod minskar frågans minnes krav. För databas skalning, se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Elastiska pool-fel

Följande fel är relaterade till att skapa och använda elastiska pooler:

| Felkod | severity | Beskrivning | Korrigerande åtgärd |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Den elastiska poolen har nått sin lagrings gräns. Lagrings användningen för den elastiska poolen får inte överskrida (% d) MB. Försök att skriva data till en databas när lagrings gränsen för den elastiska poolen har nåtts. Information om resurs gränser finns i: <br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Överväg att öka DTU: er och/eller lägga till lagring till den elastiska poolen om det är möjligt för att öka lagrings gränsen, minska lagrings utrymmet som används av enskilda databaser i den elastiska poolen eller ta bort databaser från den elastiska poolen. För skalning av elastiska pooler, se [skala elastiska pool resurser](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |% S minsta garanti är% d, max gränsen är% d och den aktuella användningen för databasen är% d. Servern är dock för närvarande upptagen för att stödja begär Anden som är större än% d för den här databasen. Information om resurs gränser finns i: <br/>&bull; &nbsp;[DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Annars kan du försöka igen senare. DTU/vCore min per databas; Max per databas för DTU/vCore. Det totala antalet samtidiga arbetare (begär Anden) över alla databaser i den elastiska poolen försökte överskrida poolens gräns. |Överväg att öka DTU: er-eller virtuella kärnor för den elastiska poolen om det är möjligt för att öka arbets gränsen, eller ta bort databaser från den elastiska poolen. |
| 40844 | 16 |Databasen% ls på servern% LS är en% LS Edition-databas i en elastisk pool och kan inte ha en kontinuerlig kopierings relation.  |Gäller inte |
| 40857 | 16 |Det gick inte att hitta någon elastisk pool för servern:% ls, namn på elastisk pool:% ls. Den angivna elastiska poolen finns inte på den angivna servern. | Ange ett giltigt namn på elastisk pool. |
| 40858 | 16 |Den elastiska poolen% LS finns redan på servern:% ls. Den angivna elastiska poolen finns redan på den angivna SQL Database servern. | Ange ett nytt namn på elastisk pool. |
| 40859 | 16 |Den elastiska poolen stöder inte tjänst nivån% ls. Den angivna tjänst nivån stöds inte för etablering av elastisk pool. |Ange rätt utgåva eller lämna tjänst nivån tom för att använda standard tjänst nivån. |
| 40860 | 16 |Kombinationen av elastisk pool% LS och tjänst målet% LS är ogiltig. Elastisk pool och tjänst nivå kan bara anges om resurs typ har angetts som ' ElasticPool '. |Ange rätt kombination av elastisk pool och tjänst nivå. |
| 40861 | 16 |Databas versionen%. *ls får inte vara samma som tjänst nivån för elastisk pool, som är%.* ls. Databas versionen skiljer sig från tjänst nivån för elastisk pool. |Ange inte någon annan databas version än tjänst nivån för elastisk pool.  Observera att databas versionen inte behöver anges. |
| 40862 | 16 |Namn på elastisk pool måste anges om det angivna målet för tjänsten elastisk pool har angetts. Ett tjänst mål för elastisk pool kan inte unikt identifiera en elastisk pool. |Ange namnet på den elastiska poolen om du använder det elastiska pool tjänst målet. |
| 40864 | 16 |DTU: er för den elastiska poolen måste vara minst (% d) DTU: er för tjänst nivån%. * ls. Försöker ställa in DTU: er för den elastiska poolen under minimigränsen. |Försök att ange DTU: er för den elastiska poolen till minst den minsta gränsen. |
| 40865 | 16 |DTU: er för den elastiska poolen får inte överskrida (% d) DTU: er för tjänst nivån%. * ls. Försöker ställa in DTU: er för den elastiska poolen ovanför Max gränsen. |Försök att ange DTU: er för den elastiska poolen så att den inte överskrider max gränsen. |
| 40867 | 16 |Max DTU per databas måste vara minst (% d) för tjänst nivån%. * ls. Försöker ställa in Max DTU per databas under den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40868 | 16 |Max DTU per databas får inte överskrida (% d) för tjänst nivån%. * ls. Försöker ställa in Max DTU per databas utöver den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40870 | 16 |Minsta DTU per databas får inte överskrida (% d) för tjänst nivån%. * ls. Försöker ställa in min DTU-min per databas utöver den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40873 | 16 |Antalet databaser (% d) och minsta DTU per databas (% d) får inte överskrida DTU: er för den elastiska poolen (% d). Försöker ange min DTU för databaser i den elastiska poolen som överskrider DTU: er för den elastiska poolen. | Överväg att öka DTU: er för den elastiska poolen, eller minska det minsta DTU-minimivärdet per databas, eller minska antalet databaser i den elastiska poolen. |
| 40877 | 16 |Det går inte att ta bort en elastisk pool om den inte innehåller några databaser. Den elastiska poolen innehåller en eller flera databaser och kan därför inte tas bort. |Ta bort databaser från den elastiska poolen för att ta bort den. |
| 40881 | 16 |Den elastiska poolen%. * ls har nått sin gräns för antal databaser.  Gränsen för antalet databaser för den elastiska poolen får inte överskrida (% d) för en elastisk pool med (% d) DTU: er. Försöker skapa eller lägga till en databas i en elastisk pool när gränsen för antal databaser för den elastiska poolen har nåtts. | Överväg att öka DTU: er för den elastiska poolen om det är möjligt för att öka databas gränsen, eller ta bort databaser från den elastiska poolen. |
| 40889 | 16 |DTU: er eller lagrings gränsen för den elastiska poolen%. * ls kan inte minskas eftersom det inte finns tillräckligt med lagrings utrymme för databaserna. Försöker minska lagrings gränsen för den elastiska poolen under lagrings användningen. | Överväg att minska lagrings användningen för enskilda databaser i den elastiska poolen eller ta bort databaser från poolen, för att minska dess DTU: er eller lagrings gräns. |
| 40891 | 16 |Minimivärdet för DTU per databas (% d) får inte överskrida Max värdet för DTU per databas (% d). Försöker ställa in min DTU-minimivärde per databas som är högre än värdet för DTU per databas. |Se till att det lägsta DTU-antalet per databas inte överskrider max värdet per databas. |
| TBD | 16 |Lagrings storleken för en enskild databas i en elastisk pool får inte överskrida den maximala storlek som tillåts av den elastiska poolen%. * ls. Databasens Max storlek överskrider max storleken som tillåts av tjänst nivån för elastisk pool. |Ange Max storleken för databasen inom gränserna för den maximala storlek som tillåts av tjänst nivån elastisk pool. |

Närliggande ämnen:

* [Skapa en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Hantera en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Skapa en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Övervaka och hantera en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Allmänna fel

Följande fel tillhör inte några tidigare kategorier.

| Felkod | severity | Beskrivning |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin) är inte ett giltigt namn eftersom det innehåller ogiltiga tecken.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Det gick inte att logga in. Inloggningen kommer från en icke betrodd domän och kan inte användas med Windows authentication.%.&#x2a;ls (Windows-inloggningar inte stöds i den här versionen av SQL Server.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Inloggningen misslyckades för användaren%. &#x2a;ls.%. &#x2a;ls%. &#x2a;LS (inloggningen misslyckades för användaren%.&#x2a; LS ".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Inloggningen misslyckades för användaren (%.&#x2a;ls). Pga Kontot är inaktiverat.%. &#x2a;ls |
| 40014 |16 |Det går inte att använda flera databaser i samma transaktion. |
| 40054 |16 |Tabeller utan ett grupperat index stöds inte i den här versionen av SQL Server. Skapa ett grupperat index och försök igen. |
| 40133 |15 |Den här åtgärden stöds inte i den här versionen av SQL Server. |
| 40506 |16 |Angivet SID är ogiltigt för den här versionen av SQL Server. |
| 40507 |16 |'%.&#x2a;ls' kan inte anropas med parametrar i den här versionen av SQL Server. |
| 40508 |16 |USE-instruktionen stöds inte för att växla mellan databaser. Använd en ny anslutning för att ansluta till en annan databas. |
| 40510 |16 |Instruktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server |
| 40511 |16 |Den inbyggda funktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40512 |16 |Den föråldrade funktionen% LS stöds inte i den här versionen av SQL Server. |
| 40513 |16 |Servern variabeln '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40514 |16 |% LS stöds inte i den här versionen av SQL Server. |
| 40515 |16 |Referens till databasen och/eller server namn i '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40516 |16 |Globala temporära objekt stöds inte i den här versionen av SQL Server. |
| 40517 |16 |Nyckelordet eller instruktionsalternativet '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40518 |16 |DBCC-kommandot '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40520 |16 |Den skydd bara klassen% S_MSG stöds inte i den här versionen av SQL Server. |
| 40521 |16 |Den skydd bara klassen% S_MSG stöds inte i Server omfånget i den här versionen av SQL Server. |
| 40522 |16 |Databastyp UPN '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40523 |16 |Skapa en implicit användarroll '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. Skapa användaren explicit innan du använder den. |
| 40524 |16 |Datatypen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40525 |16 |MED%. ls stöds inte i den här versionen av SQL Server. |
| 40526 |16 |'%.&#x2a;ls' raduppsättningsprovidern stöds inte i den här versionen av SQL Server. |
| 40527 |16 |Länkade servrar stöds inte i den här versionen av SQL Server. |
| 40528 |16 |Det går inte att mappa användare till certifikat, asymmetriska nycklar eller Windows-inloggningar i den här versionen av SQL Server. |
| 40529 |16 |Den inbyggda funktionen '%.&#x2a;ls' personifiering kontexten inte stöds i den här versionen av SQL Server. |
| 40532 |11 |Det går inte att öppna servern ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. |
| 40553 |16 |Sessionen har avslut ATS på grund av överdriven minnes användning. Försök att ändra frågan så att den bearbetar färre rader.<br/><br/> Genom att minska antalet `ORDER BY`-och `GROUP BY`-åtgärder i din Transact-SQL-kod kan du minska minnes kraven för din fråga. |
| 40604 |16 |Det gick inte att skapa/ändra databasen eftersom den skulle överskrida serverns kvot. |
| 40606 |16 |Det finns inte stöd för att ansluta databaser i den här versionen av SQL Server. |
| 40607 |16 |Windows-inloggningar stöds inte i den här versionen av SQL Server. |
| 40611 |16 |Servrar kan ha högst 128 brand Väggs regler definierade. |
| 40614 |16 |Start-IP-adressen för brand Väggs regeln får inte överskrida IP-adressen. |
| 40615 |16 |Det går inte att öppna servern {0} som begärdes vid inloggningen. Klienten med IP-adressen {1} får inte åtkomst till servern.<br /><br />Om du vill aktivera åtkomst använder du SQL Database portalen eller kör SP @ no__t-0set @ no__t-1firewall @ no__t-2rule på huvud databasen för att skapa en brand Väggs regel för den här IP-adressen eller adress intervallet. Det kan ta upp till fem minuter innan den här ändringen börjar gälla. |
| 40617 |16 |Namnet på brand Väggs regeln som börjar med (regel namnet) är för långt. Maximal längd är 128. |
| 40618 |16 |Brand Väggs regelns namn får inte vara tomt. |
| 40620 |16 |Inloggningen misslyckades för användaren ”%.&#x2a;ls”. Det gick inte att ändra lösen ordet. Lösen ords ändring under inloggning stöds inte i den här versionen av SQL Server. |
| 40627 |20 |Åtgärden på servern {0} och databasen {1} pågår. Vänta några minuter innan du försöker igen. |
| 40630 |16 |Lösen ords verifieringen misslyckades. Lösen ordet uppfyller inte princip kraven eftersom det är för kort. |
| 40631 |16 |Det angivna lösen ordet är för långt. Lösen ordet får innehålla högst 128 tecken. |
| 40632 |16 |Lösen ords verifieringen misslyckades. Lösen ordet uppfyller inte princip kraven eftersom det inte är tillräckligt komplext. |
| 40636 |16 |Det går inte att använda ett reserverat namn (%.&#x2a;ls) i den här åtgärden. |
| 40638 |16 |Ogiltigt prenumerations-ID (prenumerations-ID). Prenumerationen finns inte. |
| 40639 |16 |Begäran stämmer inte överens med schemat: (schema fel). |
| 40640 |20 |Ett oväntat undantag påträffades av servern. |
| 40641 |16 |Den angivna platsen är ogiltig. |
| 40642 |17 |Servern är för upptagen för tillfället. Försök igen senare. |
| 40643 |16 |Det angivna värdet för x-MS-version-huvudet är ogiltigt. |
| 40644 |14 |Det gick inte att bevilja åtkomst till den angivna prenumerationen. |
| 40645 |16 |Servername (servername) får inte vara tomt eller null. Det kan bara bestå av gemener "a-z", numren 0-9 och bindestrecket. Bindestrecket får inte leda eller leda till ett spår. |
| 40646 |16 |Prenumerations-ID får inte vara tomt. |
| 40647 |16 |Prenumerationen (prenumerations-ID) saknar Server (servername). |
| 40648 |17 |För många begär Anden har utförts. Försök igen senare. |
| 40649 |16 |Ogiltig innehålls typ har angetts. Endast Application/XML stöds. |
| 40650 |16 |Prenumerationen (prenumerations-ID) finns inte eller är inte klar för åtgärden. |
| 40651 |16 |Det gick inte att skapa servern eftersom prenumerationen (prenumerations-ID) är inaktive rad. |
| 40652 |16 |Det går inte att flytta eller skapa servern. Prenumerationen (prenumerations-ID) överskrider Server kvoten. |
| 40671 |17 |Kommunikations problem mellan gatewayen och hanterings tjänsten. Försök igen senare. |
| 40852 |16 |Det går inte att öppna databasen%. \*ls på servern%. \*ls som begärdes av inloggningen. Åtkomst till databasen tillåts endast med en säkerhets aktive rad anslutnings sträng. För att få åtkomst till den här databasen ändrar du anslutnings strängarna så att de innehåller "Secure" i Server-FQDN-' Server namnet '. Database. Windows. net ska ändras till ' Server namn '. Database. `secure`.windows.net. |
| 40914 | 16 | Det går inte att öppna servern *[Server Name]* som begärdes av inloggningen. Klienten har inte behörighet att komma åt servern.<br /><br />Överväg att lägga till en [regel för virtuella nätverk](sql-database-vnet-service-endpoint-rule-overview.md)för att åtgärda problemet. |
| 45168 |16 |SQL Azure systemet är under belastning och placerar en övre gräns för samtidiga DB CRUD-åtgärder för en enda SQL Database Server (t. ex. skapa databas). Servern som anges i fel meddelandet överskrider det högsta antalet samtidiga anslutningar. Försök igen senare. |
| 45169 |16 |SQL Azure-systemet är under belastning och placerar en övre gräns för antalet samtidiga Server CRUD åtgärder för en enskild prenumeration (t. ex. skapa server). Den prenumeration som anges i fel meddelandet har överskridit det högsta antalet samtidiga anslutningar och begäran nekades. Försök igen senare. |

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure SQL Database funktioner](sql-database-features.md).
* Läs om [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md).
* Läs om [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md).


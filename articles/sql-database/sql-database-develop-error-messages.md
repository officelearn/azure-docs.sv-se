---
title: Felkoder för SQL - Databasanslutningsfel | Microsoft Docs
description: 'Mer information om SQL-felkoder för SQL-databas klientprogram, till exempel vanliga anslutningsfel i databasen och kopiera databasproblem Allmänt fel. '
keywords: SQL-felkod, åtkomst till sql, Databasanslutningsfel, sql-felkoder
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: af0cb98d679125d20b3f4b7819012bee70e04cd1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645505"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-felkoder för SQL Database-klientprogram: anslutningsfel och andra problem

Den här artikeln innehåller SQL-felkoder för SQL Database-klientprogram, inklusive anslutningsfel i databasen, tillfälligt fel (kallas även tillfälliga problem), resurs styrning fel, kopiera databasproblem, elastisk pool och andra fel. De flesta kategorier är specifika för Azure SQL Database och gäller inte för Microsoft SQL Server. Se även [system felmeddelanden](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Fel vid anslutning till databasen, tillfälligt fel och andra tillfälliga fel
I följande tabell beskrivs felkoder för SQL för anslutningsfel går förlorade och andra tillfälliga fel som kan uppstå när programmet försöker få åtkomst till SQL-databas. Komma igång Självstudier om hur du ansluter till Azure SQL Database finns [ansluta till Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>De vanligaste databasen anslutningsfel och tillfälliga fel fel
Azure-infrastrukturen har möjlighet att dynamiskt om servrar när tunga arbetsbelastningar uppstår i tjänsten SQL-databas.  Det här dynamiska funktionssättet orsaka klientprogrammet förlorar sin anslutning till SQL-databas. Den här typen av fel kallas en *tillfälligt fel*.

Du rekommenderas att klientprogrammet har logik så att den kan återupprätta en anslutning med den tillfälliga fel tid att rätta sig själv.  Vi rekommenderar att du fördröjning för 5 sekunder innan din första försök. Försöker igen efter en kortare än 5 sekunder risker överbelasta Molntjänsten fördröjning. För varje efterföljande försök fördröjningen ska växa exponentiellt, upp till högst 60 sekunder.

Tillfälligt fel fel visas vanligtvis som en av följande felmeddelanden från ditt program för klienter:

* Databasen &lt;%{db_name/&gt; på servern &lt;Azure_instance&gt; är inte tillgänglig. Försök ansluta igen senare. Om problemet kvarstår kontaktar du kundsupport och uppger sessions-ID för spårning av &lt;session_id&gt;
* Databasen &lt;%{db_name/&gt; på servern &lt;Azure_instance&gt; är inte tillgänglig. Försök ansluta igen senare. Om problemet kvarstår kontaktar du kundsupport och uppger sessions-ID för spårning av &lt;session_id&gt;. (Microsoft SQL Server, fel: 40613)
* En befintlig anslutning tvingades att stänga av fjärrvärden.
* System.Data.Entity.Core.EntityCommandExecutionException: Ett fel uppstod när kommandodefinition. Se ursprungsundantag för detaljer. ---> System.Data.SqlClient.SqlException: ett fel uppstod när resultat skulle tas emot från servern. (providern: Session-providern, fel: 19 - fysiska anslutningen kan inte användas)
* Ett anslutningsförsök till en sekundär databas misslyckades eftersom databasen håller på att omkonfiguration och den är upptagen med att tillämpa nya sidor i mitten av en aktiv transaktion i den primära databasen. 

Kodexempel för logik finns:

* [Anslutningsbibliotek för SQL Database och SQLServer](sql-database-libraries.md) 
* [Åtgärder för att åtgärda anslutningsfel och tillfälliga fel i SQL-databas](sql-database-connectivity-issues.md)

En beskrivning av den *blockerande period* för klienter som använder ADO.NET finns i [SQL Server anslutning poolning (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Felkoder för tillfälligt fel
Följande fel är tillfälligt och bör göras i programlogiken: 

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 4060 |16 |Det går inte att öppna databasen ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. |
| 40197 |17 |Tjänsten har påträffat ett fel när din begäran bearbetades. Försök igen. Felkod: %d.<br/><br/>Du får detta felmeddelande när tjänsten är igång på grund av andra failover-problem, maskinvarufel eller uppgraderingar av maskinvara eller programvara. Felkoden (%d) som är inbäddad i meddelandet för fel 40197 tillhandahåller ytterligare information om vilken typ av fel eller växling vid fel som inträffat. Några exempel på koder är inbäddade i meddelandet för fel 40197 felet är 40020, 40143, 40166 och 40540.<br/><br/>Ansluta till SQL Database-server automatiskt ansluter till en felfri kopia av databasen. Programmet måste fånga 40197, felloggen inbäddade felkoden (%d) i meddelandet för att felsöka och försök ansluta till SQL-databas tills resurserna som är tillgängliga, och anslutningen har upprättats igen. |
| 40501 |20 |Tjänsten är upptagen. Gör en begäran om efter 10 sekunder. Incident-ID: %ls. Felkod: %d.<br/><br/>Mer information finns i:<br/>• [Gränserna för azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40613 |17 |Databasen '%.&#x2a;ls' på servern '%.&#x2a;ls' är inte tillgänglig. Försök ansluta igen senare. Om problemet kvarstår kontaktar du kundsupport och ger dem session spårnings-ID (%.&#x2a;ls). |
| 49918 |16 |Det går inte att bearbeta begäran. Det finns inte tillräckligt med resurser för att behandla begäran.<br/><br/>Tjänsten är upptagen. Försök begäran senare. |
| 49919 |16 |Bearbeta det går inte att skapa eller uppdatera begäran. För många skapande- eller uppdateringsåtgärder pågår för prenumerationen ”% ld”.<br/><br/>Tjänsten är upptagen bearbeta flera skapa eller uppdatera begäranden för din prenumeration eller server. Begäranden blockeras för resursoptimering. Frågan [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för väntande åtgärder. Vänta tills väntande skapa eller uppdatera begäranden har slutförts eller ta bort en av dina väntande begäranden och försök igen med din begäran senare. |
| 49920 |16 |Det går inte att bearbeta begäran. För många åtgärder pågår för prenumerationen ”% ld”.<br/><br/>Tjänsten är upptagen med flera förfrågningar för den här prenumerationen. Begäranden blockeras för resursoptimering. Frågan [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för Åtgärdsstatus. Vänta tills väntande begäranden har slutförts eller ta bort en av dina väntande begäranden och försök igen med din begäran senare. |
| 4221 |16 |Inloggningen till Läs-/ underordnad misslyckades på grund av lång väntan på 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. Repliken är inte tillgänglig för inloggning eftersom raden versioner saknas för transaktioner som var relä när repliken återvanns. Problemet kan lösas genom att återställa eller acceptera aktiva transaktioner på den primära repliken. Förekomster av det här tillståndet kan minimeras genom att man undviker långa skrivtransaktioner på primärt. |

## <a name="database-copy-errors"></a>Kopiera databasfel
Följande fel inträffade kan kopiera en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen (%.&#x2a;ls) är tillfälligt inaktiverad. |
| 40637 |16 |Skapa databaskopian är för närvarande inaktiverad. |
| 40561 |16 |Databaskopieringen misslyckades. Käll-eller måldatabasen finns inte. |
| 40562 |16 |Databaskopieringen misslyckades. Källdatabasen har släppts. |
| 40563 |16 |Databaskopieringen misslyckades. Måldatabasen har släppts. |
| 40564 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40565 |16 |Databaskopieringen misslyckades. Mer än 1 samtidiga databaskopian från samma källa tillåts. Släpp måldatabasen och försök igen senare. |
| 40566 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40567 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40568 |16 |Databaskopieringen misslyckades. Källdatabasen är inte tillgänglig. Släpp måldatabasen och försök igen. |
| 40569 |16 |Databaskopieringen misslyckades. Måldatabasen är inte tillgänglig. Släpp måldatabasen och försök igen. |
| 40570 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |
| 40571 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |

## <a name="resource-governance-errors"></a>Resursen styrning fel
Följande fel orsakas av överdriven användning av resurser när du arbetar med Azure SQL Database. Exempel:

* En transaktion har varit öppen för länge.
* En transaktion innehåller för många Lås.
* Ett program förbrukar för mycket minne.
* Ett program förbrukar för mycket `TempDb` utrymme.

Relaterade ämnen:

* Mer information finns här: [gränserna för Azure SQL Database](sql-database-service-tiers-dtu.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 10928 |20 |Resurs-ID: %d. %S-gränsen för databasen är %d och har nåtts. Mer information finns på [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Resurs-ID anger den resurs som har nått gränsen. För trådar, resurs-ID = 1. För sessioner, resurs-ID = 2.<br/><br/>Mer information om felet och hur man löser problemet finns:<br/>• [Gränserna för azure SQL Database](sql-database-service-tiers-dtu.md). |
| 10929 |20 |Resurs-ID: %d. Minsta garantin %s är %d, högsta gränsen är %d och användningen av databasen är %d. Servern är för tillfället för upptagen för att stödja större än %d begäranden för den här databasen. Mer information finns på [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Annars, försök igen senare.<br/><br/>Resurs-ID anger den resurs som har nått gränsen. För trådar, resurs-ID = 1. För sessioner, resurs-ID = 2.<br/><br/>Mer information om felet och hur man löser problemet finns:<br/>• [Gränserna för azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40544 |20 |Databasen har uppnått sin kvot i storlek. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. |
| 40549 |16 |Sessionen avslutas eftersom du har en tidskrävande transaktion. Försök att göra transaktionen kortare. |
| 40550 |16 |Sessionen har avslutats eftersom den genererade för många Lås. Försök att läsa eller ändra färre rader i en enda transaktion. |
| 40551 |16 |Sessionen har avslutats på grund av hög `TEMPDB` användning. Försök att ändra frågan för att minska temporärt tabellutrymme.<br/><br/>Om du använder temporära objekt spara utrymme i den `TEMPDB` databasen genom att släppa temporära objekt när de inte längre behövs av sessionen. |
| 40552 |16 |Sessionen har avslutats på grund av mycket transaktionsloggutrymme användning av diskutrymme för loggen. Försök att ändra färre rader i en enda transaktion.<br/><br/>Om du utför bulk infogar med hjälp av den `bcp.exe` verktyget eller `System.Data.SqlClient.SqlBulkCopy` klassen genom att använda den `-b batchsize` eller `BatchSize` alternativ för att begränsa antalet rader som ska kopieras till servern i varje transaktion. När du återskapar ett index med det `ALTER INDEX` instruktionen, försök med den `REBUILD WITH ONLINE = ON` alternativet. |
| 40553 |16 |Sessionen har avslutats på grund av omfattande minnesanvändning. Försök att ändra frågan så att färre rader bearbetas.<br/><br/>Minska antalet `ORDER BY` och `GROUP BY` åtgärder i Transact-SQL-kod minskar minneskrav av din fråga. |

## <a name="elastic-pool-errors"></a>Elastisk pool-fel
Följande fel relaterade till skapa och använda elastiska pooler:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |Den elastiska poolen har nått sin lagringsgräns. Lagringsanvändningen för den elastiska poolen får inte överskrida (%d) MB. |Elastisk pool gräns för diskutrymme i MB. |Försök att skriva data till en databas när lagringsgränsen för den elastiska poolen har uppnåtts. |Överväg att öka dtu: erna för och/eller att lägga till lagring till den elastiska poolen om möjligt för att öka sin lagringsgräns minska det lagringsutrymme som används av enskilda databaser i den elastiska poolen eller ta bort databaser från den elastiska poolen. |
| 10929 |EX_USER |Minsta garantin %s är %d, högsta gränsen är %d och användningen av databasen är %d. Servern är för tillfället för upptagen för att stödja större än %d begäranden för den här databasen. Se [ http://go.microsoft.com/fwlink/?LinkId=267637 ](http://go.microsoft.com/fwlink/?LinkId=267637) om du behöver hjälp. Annars, försök igen senare. |DTU / vCore min. per databas. DTU / vCore max per databas |Det totala antalet samtidiga arbetare (antal begäranden) över alla databaser i den elastiska poolen försök gjordes att överskrida gränsen på poolen. |Överväg att öka dtu: er eller vCores för den elastiska poolen om möjligt för att öka gränsen worker eller ta bort databaser från den elastiska poolen. |
| 40844 |EX_USER |Databasen %ls på %ls-servern är %ls edition-databas i en elastisk pool och kan inte ha en kontinuerlig kopiering relation. |databasens namn, databasversionen, servernamn |En StartDatabaseCopy-kommandot har utfärdats för en icke-premium db i en elastisk pool. |Kommer snart |
| 40857 |EX_USER |Elastisk pool saknas för servern: %ls, namn på elastisk pool: %ls. |namnet på servern. namn på elastisk pool |Angivna elastisk pool finns inte i den angivna servern. |Ange ett giltigt elastisk pool-namn. |
| 40858 |EX_USER |Elastisk pool %ls finns redan på servern: %ls |namn på elastisk pool, servernamn |Angivna elastisk pool finns redan i den angivna logiska servern. |Ange namn på ny elastisk pool. |
| 40859 |EX_USER |Elastiska poolen stöder inte tjänstnivån %ls. |elastiska pooltjänstnivå |Angivna tjänstnivån stöds inte för etablering av elastisk pool. |Ange rätt version eller lämna det tomt om du vill använda standard-tjänstnivå tjänstnivån. |
| 40860 |EX_USER |Kombinationen av elastiska poolen %ls och tjänsten målet %ls är ogiltig. |namn på elastisk pool; namn på servicenivåmål |Elastisk pool och tjänsten målet kan anges samtidigt endast om tjänstmålet anges som 'ElasticPool'. |Ange rätt kombination av elastiska poolen och tjänstmålet. |
| 40861 |EX_USER |Databasversionen ' %. *ls' får inte vara annorlunda än den elastiska pooltjänstnivå som är ' %.* ls'. |Databasversionen elastiska pooltjänstnivå |Databasversionen är annorlunda än den elastiska pooltjänstnivå. |Ange inte en databasversionen som skiljer sig från den elastiska pooltjänstnivå.  Observera att databasversionen inte måste anges. |
| 40862 |EX_USER |Namn på elastisk pool måste vara anges om tjänstmålet elastisk pool har angetts. |Ingen |Elastisk pool tjänstmålet identifiera inte unikt en elastisk pool. |Ange namn på elastisk pool om du använder tjänstmålet elastisk pool. |
| 40864 |EX_USER |Dtu: erna för den elastiska poolen måste vara minst (%d) dtu: er för tjänstnivån ' %. * ls'. |Dtu: er för elastiska poolen; elastiska pooltjänstnivå. |Försöker ange dtu: erna för den elastiska poolen under minimigränsen. |Gör om inställningen dtu: erna för elastiska pool till minst den lägsta tillåtna värdet. |
| 40865 |EX_USER |Dtu: erna för den elastiska poolen får inte överskrida (%d) dtu: er för tjänstnivån ' %. * ls'. |Dtu: er för elastiska poolen; elastiska pooltjänstnivå. |Försöker ange dtu: erna för den elastiska poolen ovan maxgränsen. |Försök ange dtu: erna för den elastiska poolen till inte är större än den maximala gränsen. |
| 40867 |EX_USER |Max DTU per databas måste vara minst (%d) för tjänstnivån ' %. * ls'. |Max DTU per databas. elastiska pooltjänstnivå |Försöker ange max DTU per databas under gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställningen. |
| 40868 |EX_USER |Max DTU per databas får inte överskrida (%d) för tjänstnivån ' %. * ls'. |Max DTU per databas. elastiska pooltjänstnivå. |Försöker ange max DTU per databas, utöver gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställningen. |
| 40870 |EX_USER |Minsta DTU per databas får inte överskrida (%d) för tjänstnivån ' %. * ls'. |Minsta DTU per databas. elastiska pooltjänstnivå. |Försöker ange minsta DTU per databas, utöver gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställningen. |
| 40873 |EX_USER |Antalet databaser (%d) och minsta DTU per databas (%d) får inte överskrida dtu: erna för den elastiska poolen (%d). |Antalet databaser i elastiska poolen; Minsta DTU per databas. Dtu: er för elastisk pool. |Försöker ange minsta DTU för databaser i den elastiska poolen som överskrider dtu: erna för den elastiska poolen. | Överväg att öka dtu: erna för den elastiska poolen, eller minska minsta DTU per databas, eller minska antalet databaser i den elastiska poolen. |
| 40877 |EX_USER |En elastisk pool kan inte tas bort om den inte innehåller några databaser. |Ingen |Den elastiska poolen innehåller en eller flera databaser och därför kan inte tas bort. |Ta bort databaser från den elastiska poolen för att ta bort den. |
| 40881 |EX_USER |Den elastiska poolen ' %. * ls' har nått sin gräns för antalet av databasen.  Gränsvärde för databasen för den elastiska poolen får inte överskrida (%d) för en elastisk pool med (%d) dtu: er. |Namn på elastisk pool; Antal begränsning på elastisk pool; edtu: er för resurspoolen. |Försök att skapa eller lägga till databasen i den elastiska poolen när databasen gränsvärde för den elastiska poolen har uppnåtts. | Överväg att öka dtu: erna för den elastiska poolen om möjligt för att öka gränsen för databasen eller ta bort databaser från den elastiska poolen. |
| 40889 |EX_USER |Dtu: erna eller lagringsgränsen för den elastiska poolen ' %. * ls' kan inte minskas eftersom som inte skulle ger tillräckligt med lagringsutrymme för dess databaser. |Namn på elastisk pool. |Försöker lagringsgränsen för den elastiska poolen under dess lagringskvoten. | Överväg att minska lagringsanvändningen för enskilda databaser i den elastiska poolen och ta bort databaser från poolen för att minska dess dtu: erna eller lagringsgränsen. |
| 40891 |EX_USER |Minsta DTU per databas (%d) får inte överskrida max DTU per databas (%d). |Minsta DTU per databas. Max DTU per databas. |Försöker ange minsta DTU per databas som är högre än max DTU per databas. |Kontrollera minsta DTU per databaser inte överskrida max DTU per databas. |
| TBD |EX_USER |Lagringsstorleken för en individuell databas i en elastisk pool får inte överskrida den maximala storleken som tillåts av ' %. * ls' service tier elastisk pool. |elastiska pooltjänstnivå |Maxstorlek för databasen överskrider den maximala storlek som tillåts av elastiska pooltjänstnivå. |Ange maximala storleken på databasen inom gränserna för den maximala storlek som tillåts av elastiska pooltjänstnivå. |

Relaterade ämnen:

* [Skapa en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Hantera en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Skapa en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Övervaka och hantera en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Allmänt fel
Följande fel omfattas inte i alla tidigare kategorier.

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) är inte ett giltigt namn eftersom det innehåller ogiltiga tecken. |
| 18452 |14 |Inloggningen misslyckades. Inloggningen kommer från en icke betrodd domän och kan inte användas med Windows authentication.%.&#x2a;ls (Windows-inloggningar inte stöds i den här versionen av SQL Server.) |
| 18456 |14 |Inloggningen misslyckades för användaren '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(inloggningen misslyckades för användaren ”%.&#x2a;ls”. Det gick inte att ändra lösenordet. Lösenordsändring under inloggning stöds inte i den här versionen av SQL Server.) |
| 18470 |14 |Inloggningen misslyckades för användaren (%.&#x2a;ls). Orsak: Kontot är disabled.%.&#x2a;ls |
| 40014 |16 |Flera databaser kan inte användas i samma transaktion. |
| 40054 |16 |Tabeller utan grupperat index stöds inte i den här versionen av SQL Server. Skapa ett grupperat index och försök igen. |
| 40133 |15 |Den här åtgärden stöds inte i den här versionen av SQL Server. |
| 40506 |16 |Angiven SID är ogiltigt för den här versionen av SQL Server. |
| 40507 |16 |'%.&#x2a;ls' kan inte anropas med parametrar i den här versionen av SQL Server. |
| 40508 |16 |USE-instruktionen stöds inte för växling mellan databaser. Använd en ny anslutning för att ansluta till en annan databas. |
| 40510 |16 |Instruktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server |
| 40511 |16 |Den inbyggda funktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40512 |16 |Föråldrad funktion %ls stöds inte i den här versionen av SQL Server. |
| 40513 |16 |Servern variabeln '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40514 |16 |%ls stöds inte i den här versionen av SQL Server. |
| 40515 |16 |Referens till databasen och/eller server namn i '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40516 |16 |Globala temporära objekt stöds inte i den här versionen av SQL Server. |
| 40517 |16 |Nyckelordet eller instruktionsalternativet '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40518 |16 |DBCC-kommandot '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40520 |16 |Den skyddbara klassen '% S_MSG' stöds inte i den här versionen av SQL Server. |
| 40521 |16 |Den skyddbara klassen '% S_MSG' stöds inte i serverdefinitionsområdet i den här versionen av SQL Server. |
| 40522 |16 |Databastyp UPN '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40523 |16 |Skapa en implicit användarroll '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. Skapa användaren explicit innan du använder den. |
| 40524 |16 |Datatypen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40525 |16 |MED '%.ls' stöds inte i den här versionen av SQL Server. |
| 40526 |16 |'%.&#x2a;ls' raduppsättningsprovidern stöds inte i den här versionen av SQL Server. |
| 40527 |16 |Länkade servrar stöds inte i den här versionen av SQL Server. |
| 40528 |16 |Att det går inte mappa användare till certifikat, asymmetriska nycklar eller Windows-inloggningsnamn i den här versionen av SQL Server. |
| 40529 |16 |Den inbyggda funktionen '%.&#x2a;ls' personifiering kontexten inte stöds i den här versionen av SQL Server. |
| 40532 |11 |Det går inte att öppna servern ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. |
| 40553 |16 |Sessionen har avslutats på grund av omfattande minnesanvändning. Försök att ändra frågan så att färre rader bearbetas.<br/><br/> Minska antalet `ORDER BY` och `GROUP BY` åtgärder i Transact-SQL-kod bidrar till att minska minneskrav av din fråga. |
| 40604 |16 |Det gick inte CREATE/ALTER DATABASE eftersom den skulle överskrida kvoten för servern. |
| 40606 |16 |Koppla databaser stöds inte i den här versionen av SQL Server. |
| 40607 |16 |Windows-inloggningar stöds inte i den här versionen av SQL Server. |
| 40611 |16 |Servrar kan ha högst 128 brandväggsregler definierats. |
| 40614 |16 |Första IP-adress för brandväggsregeln får inte överstiga sista IP-adress. |
| 40615 |16 |Det går inte att öppna servern '{0}som begärdes vid inloggningen. Klienten med IP-adressen{1}' är inte tillåtet att ansluta till servern.<br /><br />Använda portalen för SQL-databasen för att möjliggöra åtkomst eller köra sp\_ange\_brandväggen\_regeln på huvuddatabasen för att skapa en brandväggsregel för IP-adressen eller adressintervallet. Det kan ta upp till fem minuter för att ändringen ska börja gälla. |
| 40617 |16 |Namnet på brandväggsregeln som inleds med (Regelnamnet) är för långt. Maxlängden är 128. |
| 40618 |16 |Namnet på brandväggsregeln får inte vara tomt. |
| 40620 |16 |Inloggningen misslyckades för användaren ”%.&#x2a;ls”. Det gick inte att ändra lösenordet. Lösenordsändring under inloggning stöds inte i den här versionen av SQL Server. |
| 40627 |20 |Åtgärden på servern '{0}'och'{1}' pågår. Vänta några minuter innan du försöker igen. |
| 40630 |16 |Lösenordsverifieringen misslyckades. Lösenordet uppfyller inte principkraven eftersom det är för kort. |
| 40631 |16 |Lösenordet som du angav är för långt. Lösenordet ska ha mer än 128 tecken. |
| 40632 |16 |Lösenordsverifieringen misslyckades. Lösenordet uppfyller inte principkraven eftersom det inte är tillräckligt komplicerat. |
| 40636 |16 |Det går inte att använda ett reserverat namn (%.&#x2a;ls) i den här åtgärden. |
| 40638 |16 |Ogiltigt prenumerations-id (prenumerations-id). Prenumerationen finns inte. |
| 40639 |16 |Begäran stämmer inte överens med schemat: (schemafel). |
| 40640 |20 |Servern påträffade ett oväntat undantag. |
| 40641 |16 |Den angivna platsen är ogiltig. |
| 40642 |17 |Servern är upptagen. Försök igen senare. |
| 40643 |16 |Det angivna huvud för x-ms-version-värdet är ogiltigt. |
| 40644 |14 |Det gick inte att bevilja åtkomst till den angivna prenumerationen. |
| 40645 |16 |ServerName (servername) får inte vara tomt eller null. Det kan endast bestå av gemener ”a”-”z”, siffrorna 0-9 och bindestreck. Bindestreck kan inte leda eller slutet i namnet. |
| 40646 |16 |Prenumerations-ID kan inte vara tomt. |
| 40647 |16 |Prenumeration (prenumerations-id) har inte servern (servername). |
| 40648 |17 |För många begäranden har utförts. Försök igen senare. |
| 40649 |16 |Ogiltig innehållstyp anges. Endast application/xml stöds. |
| 40650 |16 |Prenumerationen (prenumerations-id) finns inte eller är inte redo för åtgärden. |
| 40651 |16 |Det gick inte att skapa servern eftersom prenumerationen (prenumerations-id) har inaktiverats. |
| 40652 |16 |Det går inte att flytta eller skapa server. Prenumeration (prenumerations-id) kommer att överskrida serverkvoten. |
| 40671 |17 |Kommunikationsfel mellan gateway och management-tjänsten. Försök igen senare. |
| 40852 |16 |Det går inte att öppna databasen ' %. \*ls' på servern ' %. \*ls' begärdes vid inloggningen. Åtkomst till databasen tillåts endast med hjälp av en säkerhetsaktiverad anslutningssträng. Om du vill få åtkomst till den här databasen måste ändra din anslutningssträngar för att innehålla säker i serverns FQDN - 'servernamn'.database.windows .net ska ändras till 'servernamn'.database. `secure`. windows.net. |
| 40914 | 16 | Det går inte att öppna servern '*[servernamn]* som begärdes vid inloggningen. Klienten är inte tillåtet att ansluta till servern.<br /><br />Om du vill åtgärda, Överväg att lägga till en [virtuellt nätverk regeln](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |SQL Azure-systemet är under belastning och placerar en övre gräns för samtidiga DB CRUD-åtgärder för en enskild server (t.ex. Skapa databas). Den server som anges i felmeddelandet har överskridit det maximala antalet samtidiga anslutningar. Försök igen senare. |
| 45169 |16 |SQL azure-systemet är under belastning och placerar en övre gräns för antalet samtidiga server CRUD-åtgärder för en enda prenumeration (t.ex. Skapa server). Det abonnemang som angavs i felmeddelandet överskred det maximala antalet samtidiga anslutningar och begäran nekades. Försök igen senare. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure SQL Database-funktioner](sql-database-features.md).
* Läs mer om [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md).
* Läs mer om [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).


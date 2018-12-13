---
title: SQL-felkoder - Databasanslutningsfel | Microsoft Docs
description: 'Läs mer om SQL-felkoder för SQL Database-klientprogram, till exempel vanliga fel med databasen och kopiera databasproblem Allmänt fel. '
keywords: SQL-felkod, åtkomst till sql, Databasanslutningsfel, sql-felkoder
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/31/2018
ms.openlocfilehash: 00fe4e109df2ac8954e657a1a567842ec5eb7d37
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317465"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-felkoder för SQL Database-klientprogram: Anslutningsfel för databasen och andra problem

Den här artikeln innehåller SQL-felkoder för SQL Database-klientprogram, däribland anslutningsfel för databasen, tillfälligt fel (kallas även för tillfälliga fel), resource styrning fel, kopiera databasproblem, elastisk pool och andra fel. De flesta kategorierna är specifika för Azure SQL Database och gäller inte för Microsoft SQL Server. Se även [system felmeddelanden](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Anslutningsfel för databasen, tillfälligt fel och andra tillfälliga fel
I följande tabell beskrivs SQL-felkoder för förlust-anslutningsfel och andra tillfälliga fel kan uppstå när programmet försöker få åtkomst till SQL-databas. Komma igång-Självstudier om hur du ansluter till Azure SQL Database finns i [ansluter till Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>De vanligaste databas-anslutningsfel och tillfälliga fel fel
Azure-infrastrukturen har kapacitet att dynamiskt omkonfigurera servrar vid ökad arbetsbelastning i SQL Database-tjänsten.  Den här omkonfigurationen kan göra ditt klientprogram tappar kontakten med SQL-databas. Den här typen av fel kallas en *tillfälliga fel*.

Vi rekommenderar starkt att klientprogrammet har logik för omprövning så att den kan återupprätta en anslutning ge tillfälliga fel tid att åtgärdas.  Vi rekommenderar att du inte distribuerar i fem sekunder innan din första återförsöket. Försöker igen efter en fördröjning som är kortare än 5 sekunder risker överväldigande Molntjänsten. För varje efterföljande återförsök som fördröjningen ska ökar exponentiellt, upp till högst 60 sekunder.

Tillfälliga fel fel visas vanligtvis som en av följande felmeddelanden från ditt klientprogram:

* Databasen &lt;%{db_name/&gt; på servern &lt;Azure_instance&gt; är inte tillgänglig. Försök med anslutningen senare. Om problemet kvarstår kontaktar du kundsupport och uppger sessions-ID för spårning av &lt;session_id&gt;
* Databasen &lt;%{db_name/&gt; på servern &lt;Azure_instance&gt; är inte tillgänglig. Försök med anslutningen senare. Om problemet kvarstår kontaktar du kundsupport och uppger sessions-ID för spårning av &lt;session_id&gt;. (Microsoft SQL Server, fel: 40613)
* En befintlig anslutning avslutades av fjärrvärden.
* System.Data.Entity.Core.EntityCommandExecutionException: Ett fel uppstod vid körning av kommandot definitionen. Se ursprungsundantag för detaljer. ---> System.Data.SqlClient.SqlException: Ett fel uppstod när du får resultat från servern. (providern: Session-providern, fel: 19 - fysiska anslutningen kan inte användas)
* Ett anslutningsförsök till en sekundär databas misslyckades eftersom databasen håller på att omkonfiguration och den är upptagen med att tillämpa nya sidor i mitten av en aktiv transaktion på den primära databasen. 

Kodexempel för omprövning, se:

* [Anslutningsbibliotek för SQL Database och SQLServer](sql-database-libraries.md) 
* [Åtgärder för att åtgärda anslutningsfel och tillfälliga fel i SQL-databas](sql-database-connectivity-issues.md)

En beskrivning av den *blockerar period* för klienter som använder ADO.NET finns i [SQL Server anslutning poolning (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Felkoder för tillfälliga fel
Följande fel är tillfälligt och ska göras i programlogiken: 

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 4060 |16 |Det går inte att öppna databasen ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. |
| 40197 |17 |Tjänsten har påträffat ett fel när begäran bearbetades. Försök igen. Felkod: %d.<br/><br/>Du får detta felmeddelande när tjänsten är igång på grund av programvara eller uppgraderingar av maskinvara, maskinvarufel eller några andra problem med redundans. Felkod: (%d) som är inbäddad i meddelandet av fel 40197 tillhandahåller ytterligare information om vilken typ av fel eller växling vid fel som inträffat. Några exempel på felet koder är inbäddade i meddelandet felets 40197 är 40020, 40143, 40166 och 40540.<br/><br/>Återansluter till SQL Database-servern automatiskt ansluter till en felfri kopia av databasen. Programmet måste fånga upp 40197, felloggen embedded felkoden (%d) i meddelandet för att felsöka och försöka ansluta till SQL Database tills resurserna som är tillgängliga och din anslutning har upprättats igen. |
| 40501 |20 |Tjänsten är upptagen. Försök begäran efter 10 sekunder. Incident-ID: %ls. Felkod: %d.<br/><br/>Mer information finns i:<br/>• [Azure SQL Database-resursgränser](sql-database-service-tiers-dtu.md). |
| 40613 |17 |Databasen '%.&#x2a;ls' på servern '%.&#x2a;ls' är inte tillgänglig. Försök med anslutningen senare. Om problemet kvarstår kontaktar du kundsupport och ger dem session spårnings-ID (%.&#x2a;ls). |
| 49918 |16 |Det går inte att bearbeta begäran. Det finns inte tillräckligt med resurser för att bearbeta begäran.<br/><br/>Tjänsten är upptagen. Försök begäran igen senare. |
| 49919 |16 |Bearbeta det går inte att skapa eller uppdatera begäran. För många skapande- eller uppdateringsåtgärder pågår för prenumerationen ”% ld”.<br/><br/>Tjänsten är upptagen bearbetning av flera skapa eller uppdatera begäranden för din prenumeration eller en server. Begäranden är för närvarande blockerad för resursoptimering. Fråga [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för väntande åtgärder. Vänta tills väntande skapa eller uppdatera begäranden är klara eller ta bort en av dina väntande begäranden och försök igen med din begäran senare. |
| 49920 |16 |Det går inte att bearbeta begäran. För många åtgärder pågår för prenumerationen ”% ld”.<br/><br/>Tjänsten är upptagen med flera begäranden för den här prenumerationen. Begäranden är för närvarande blockerad för resursoptimering. Fråga [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) efter Åtgärdsstatus. Vänta tills väntande begäranden är klara eller ta bort en av dina väntande begäranden och försök igen med din begäran senare. |
| 4221 |16 |Inloggning till Läs-sekundär misslyckades på grund av lång väntetid på 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. Repliken är inte tillgänglig för inloggning eftersom radversioner saknas för transaktioner som pågick när repliken återvanns. Problemet kan lösas genom att återställa eller implementera de aktiva transaktionerna på den primära repliken. Förekomster av det här tillståndet kan minimeras genom att försöka undvika långa skrivtransaktioner på primärt. |

## <a name="database-copy-errors"></a>Kopiera databasfel
Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen (%.&#x2a;ls) är tillfälligt inaktiverad. |
| 40637 |16 |Skapa kopia är för närvarande inaktiverad. |
| 40561 |16 |Databaskopieringen misslyckades. Käll-eller måldatabasen finns inte. |
| 40562 |16 |Databaskopieringen misslyckades. Källdatabasen har släppts. |
| 40563 |16 |Databaskopieringen misslyckades. Måldatabasen har släppts. |
| 40564 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40565 |16 |Databaskopieringen misslyckades. Mer än 1 samtidiga databaskopian från samma källa tillåts. Släpp måldatabasen och försök igen senare. |
| 40566 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40567 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40568 |16 |Databaskopieringen misslyckades. Källdatabasen har blivit otillgänglig. Släpp måldatabasen och försök igen. |
| 40569 |16 |Databaskopieringen misslyckades. Måldatabasen har blivit otillgänglig. Släpp måldatabasen och försök igen. |
| 40570 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |
| 40571 |16 |Databaskopieringen misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |

## <a name="resource-governance-errors"></a>Resurs-styrning fel
Följande fel orsakas av överdriven användning av resurser när du arbetar med Azure SQL Database. Exempel:

* En transaktion har varit öppen för länge.
* En transaktion innehåller för många Lås.
* Ett program förbrukar för mycket minne.
* Ett program förbrukar för mycket `TempDb` utrymme.

Relaterade ämnen:

* Mer detaljerad information finns här: [Azure SQL Database-resursgränser](sql-database-service-tiers-dtu.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 10928 |20 |Resurs-ID: %d. %S gränsen för databasen är %d och har uppnåtts. Mer information finns i [SQL Database-resursgränser för enkel och delade databaser](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).<br/><br/>Resurs-ID anger den resurs som har uppnått gränsen. För arbetstrådar, resurs-ID = 1. För sessioner, resurs-ID = 2.<br/><br/>Mer information om felet och hur du löser den finns:<br/>• [Azure SQL Database-resursgränser](sql-database-service-tiers-dtu.md). |
| 10929 |20 |Resurs-ID: %d. %S minsta garantin är %d, övre gräns är %d och aktuell användning för databasen är %d. Men är servern upptagen för närvarande stöd för begäranden som är större än %d för den här databasen. Mer information finns i [SQL Database-resursgränser för enkel och delade databaser](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server). I annat fall. Försök igen senare.<br/><br/>Resurs-ID anger den resurs som har uppnått gränsen. För arbetstrådar, resurs-ID = 1. För sessioner, resurs-ID = 2.<br/><br/>Mer information om felet och hur du löser den finns:<br/>• [Azure SQL Database-resursgränser](sql-database-service-tiers-dtu.md). |
| 40544 |20 |Databasen har nått sin storlekskvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. |
| 40549 |16 |Sessionen avslutas eftersom du har en tidskrävande transaktion. Försök att göra transaktionen kortare. |
| 40550 |16 |Sessionen har avslutats eftersom det har fått för många Lås. Försök att läsa eller ändra färre rader i en enda transaktion. |
| 40551 |16 |Sessionen har avslutats på grund av hög `TEMPDB` användning. Försök att ändra din fråga för att minska utrymmesanvändningen temporär tabell.<br/><br/>Om du använder temporära objekt, spara utrymme i den `TEMPDB` databasen genom att släppa temporära objekt när de inte längre behövs i sessionen. |
| 40552 |16 |Sessionen har avslutats på grund av transaktionsloggutrymme användning av diskutrymme för loggen. Försök ändra färre rader i en enda transaktion.<br/><br/>Om du utför bulk infogar med hjälp av den `bcp.exe` verktyget eller `System.Data.SqlClient.SqlBulkCopy` klassen genom att använda den `-b batchsize` eller `BatchSize` alternativ för att begränsa antalet rader som ska kopieras till servern i varje transaktion. När du återskapar ett index med det `ALTER INDEX` -instruktionen, försök med den `REBUILD WITH ONLINE = ON` alternativet. |
| 40553 |16 |Sessionen har avslutats på grund av hög minnesanvändning. Försök att ändra en fråga så att färre rader bearbetas.<br/><br/>Minska antalet `ORDER BY` och `GROUP BY` åtgärder i Transact-SQL-kod minskar minneskrav av din fråga. |

## <a name="elastic-pool-errors"></a>Elastisk pool-fel
Följande fel är relaterade till skapar och använder elastiska pooler:

| Felkod | Severity | Beskrivning | Korrigerande åtgärd |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Den elastiska poolen har nått sin lagringsgräns. Lagringsanvändningen för den elastiska poolen får inte överskrida (%d) MB. Försök att skriva data till en databas när gränsen för lagring på den elastiska poolen har uppnåtts. |Överväg att öka dtu: er för och/eller att lägga till lagring till den elastiska poolen om möjligt för att öka sin lagringsgräns minska lagring som används av enskilda databaser på den elastiska poolen eller ta bort databaser från den elastiska poolen. |
| 10929 | 16 |%S minsta garantin är %d, övre gräns är %d och aktuell användning för databasen är %d. Men är servern upptagen för närvarande stöd för begäranden som är större än %d för den här databasen. Se [SQL Database-resursgränser för enkel och delade databaser](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) för att få hjälp. I annat fall. Försök igen senare. DTU / vCore-min per databas. DTU / vCore-max per databas. Det totala antalet samtidiga arbetare (begäranden) över alla databaser i den elastiska poolen försökte överskrider gränsen för poolen. |Överväg att öka dtu: er eller vCores för den elastiska poolen om möjligt för att öka gränsen worker eller ta bort databaser från den elastiska poolen. |
| 40844 | 16 |Databasen '%ls ”på servern '%ls” är en '%ls ”edition-databas i en elastisk pool och kan inte ha en relation för kontinuerlig kopiering.  |Gäller inte |
| 40857 | 16 |Elastisk pool hittades inte för servern: '%ls ”, namn på elastisk pool: '%ls”. Angivna elastisk pool finns inte i den angivna servern. | Ange ett giltigt elastisk pool-namn. |
| 40858 | 16 |Elastisk pool '%ls ”finns redan på servern: '%ls”. Angivna elastisk pool finns redan i den angivna logiska servern. | Ange namn på ny elastisk pool. |
| 40859 | 16 |Elastisk pool stöder inte tjänstnivå '%ls ”. Angivna tjänstnivå finns inte stöd för etablering av elastisk pool. |Ange rätt version eller lämna det tomt om du vill använda standard-tjänstnivå tjänstnivå. |
| 40860 | 16 |Kombinationen av elastisk pool '%ls ”och tjänsten objective '%ls” är ogiltig. Elastisk pool och service-nivån kan anges tillsammans endast om resurstyp har angetts som ”ElasticPool”. |Ange rätt kombination av elastisk pool och tjänstnivå. |
| 40861 | 16 |Databasversionen ' %. *ls' får inte vara densamma som den elastiska pooltjänstnivå som är ' %.* ls'. Databasversionen är annorlunda än den elastiska pooltjänstnivå. |Ange databasutgåva som skiljer sig från den elastiska pooltjänstnivå.  Observera att databasversionen inte måste anges. |
| 40862 | 16 |Namn på elastisk pool måste vara anges om den elastiska pooltjänsten har angetts. Elastiska pooltjänsten identifierar inte en elastisk pool. |Ange namn på elastisk pool om du använder den elastiska pooltjänsten. |
| 40864 | 16 |Dtu: er för den elastiska poolen måste vara minst (%d) dtu: er för tjänstnivån ' %. * ls'. Om du försöker ange dtu: er för den elastiska poolen under minimigränsen. |Gör om inställningen dtu: erna för elastiska pool för minst det lägsta tillåtna värdet. |
| 40865 | 16 |Dtu: er för den elastiska poolen får inte överskrida (%d) dtu: er för tjänstnivån ' %. * ls'. Om du försöker ange dtu: er för den elastiska poolen ovan maxgränsen. |Försök att ställa in dtu: er för den elastiska poolen är större än den maximala gränsen. |
| 40867 | 16 |Max DTU per databas måste vara minst (%d) för tjänstnivån ' %. * ls'. Om du försöker ange max DTU per databas under gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställning. |
| 40868 | 16 |Max DTU per databas får inte överskrida (%d) för tjänstnivån ' %. * ls'. Om du försöker ange max DTU per databas över gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställning. |
| 40870 | 16 |Minsta DTU per databas får inte överskrida (%d) för tjänstnivån ' %. * ls'. Om du försöker ange minsta DTU per databas över gränsen som stöds. | Överväg att använda den elastiska pooltjänstnivå som har stöd för önskad inställning. |
| 40873 | 16 |Antal databaser (%d) och minsta DTU per databas (%d) får inte överskrida dtu: er för den elastiska poolen (%d). Försöker ange minsta DTU för databaser i den elastiska poolen som överskrider dtu: er för den elastiska poolen. | Överväg att öka dtu: er för den elastiska poolen, eller minska minsta DTU per databas, eller minska antalet databaser i den elastiska poolen. |
| 40877 | 16 |En elastisk pool kan inte tas bort om den inte innehåller några databaser. Den elastiska poolen innehåller en eller flera databaser och därför inte tas bort. |Ta bort databaser från den elastiska poolen för att ta bort den. |
| 40881 | 16 |Den elastiska poolen ' %. * ls' har nått sin gräns för antal av databasen.  Databasen antal tillåtna för den elastiska poolen får inte överskrida (%d) för en elastisk pool med (%d) dtu: er. Försöker skapa eller lägga till databasen i elastisk pool när databasen antalsgränsen på den elastiska poolen har uppnåtts. | Överväg att öka dtu: er för den elastiska poolen om möjligt för att öka sin databasgräns för eller ta bort databaser från den elastiska poolen. |
| 40889 | 16 |Dtu: er eller lagringsgränsen för den elastiska poolen ' %. * ls' kan inte minskas eftersom då inte skulle tillhandahållas tillräckligt med lagringsutrymme för dess databaser. Försöker lagringsgränsen för den elastiska poolen under dess lagringsanvändning. | Överväg att minska lagringsanvändningen enskilda databaser i den elastiska poolen eller ta bort databaser från poolen för att minska dess dtu: erna eller lagringsgränsen. |
| 40891 | 16 |Minsta DTU per databas (%d) får inte överskrida max DTU per databas (%d). Om du försöker ange minsta DTU per databas som är högre än max DTU per databas. |Kontrollera minsta DTU per databaser inte överskrida max DTU per databas. |
| TBD | 16 |Lagringsstorleken för en enskild databas i en elastisk pool får inte överskrida den maximala storleken som tillåts av ' %. * ls' service tier elastisk pool. Max storlek för databasen överskrider den maximala storleken som tillåts av den elastiska pooltjänstnivå. |Ställ in maxstorleken på databasen inom ramen för den maximala storleken som tillåts av den elastiska pooltjänstnivå. |

Relaterade ämnen:

* [Skapa en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Hantera en elastisk pool (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Skapa en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Övervaka och hantera en elastisk pool (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Allmänt fel
Följande fel omfattas inte i alla tidigare kategorier.

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) är inte ett giltigt namn eftersom det innehåller ogiltiga tecken. |
| 18452 |14 |Inloggningen misslyckades. Inloggningen kommer från en icke betrodd domän och kan inte användas med Windows authentication.%.&#x2a;ls (Windows-inloggningar inte stöds i den här versionen av SQL Server.) |
| 18456 |14 |Inloggningen misslyckades för användaren ”%. &#x2a;ls'.%. &#x2a;ls %. &#x2a;ls (inloggningen misslyckades för användaren ”%.&#x2a; is ”.) |
| 18470 |14 |Inloggningen misslyckades för användaren (%.&#x2a;ls). Orsak: Kontot är disabled.%. &#x2a;ls |
| 40014 |16 |Flera databaser kan inte användas i samma transaktion. |
| 40054 |16 |Tabeller utan grupperat index stöds inte i den här versionen av SQL Server. Skapa ett grupperat index och försök igen. |
| 40133 |15 |Den här åtgärden stöds inte i den här versionen av SQL Server. |
| 40506 |16 |Angivna SID är ogiltigt för den här versionen av SQL Server. |
| 40507 |16 |'%.&#x2a;ls' kan inte anropas med parametrar i den här versionen av SQL Server. |
| 40508 |16 |USE-instruktionen stöds inte för växling mellan databaser. Använda en ny anslutning för att ansluta till en annan databas. |
| 40510 |16 |Instruktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server |
| 40511 |16 |Den inbyggda funktionen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40512 |16 |Föråldrad funktion ”%ls” stöds inte i den här versionen av SQL Server. |
| 40513 |16 |Servern variabeln '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40514 |16 |'%ls ”stöds inte i den här versionen av SQL Server. |
| 40515 |16 |Referens till databasen och/eller server namn i '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40516 |16 |Globala temporära objekt stöds inte i den här versionen av SQL Server. |
| 40517 |16 |Nyckelordet eller instruktionsalternativet '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40518 |16 |DBCC-kommandot '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40520 |16 |Den skyddbara klassen '% S_MSG' stöds inte i den här versionen av SQL Server. |
| 40521 |16 |Den skyddbara klassen '% S_MSG' stöds inte i serverdefinitionsområdet i den här versionen av SQL Server. |
| 40522 |16 |Databastyp UPN '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40523 |16 |Skapa en implicit användarroll '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. Skapa användaren explicit innan du använder den. |
| 40524 |16 |Datatypen '%.&#x2a;ls' stöds inte i den här versionen av SQL Server. |
| 40525 |16 |MED ”%.ls' stöds inte i den här versionen av SQL Server. |
| 40526 |16 |'%.&#x2a;ls' raduppsättningsprovidern stöds inte i den här versionen av SQL Server. |
| 40527 |16 |Länkade servrar stöds inte i den här versionen av SQL Server. |
| 40528 |16 |Det går inte att mappa användare till certifikat, asymmetriska nycklar eller Windows-inloggningsnamn i den här versionen av SQL Server. |
| 40529 |16 |Den inbyggda funktionen '%.&#x2a;ls' personifiering kontexten inte stöds i den här versionen av SQL Server. |
| 40532 |11 |Det går inte att öppna servern ”%.&#x2a;ls” begärdes vid inloggningen. Inloggningen misslyckades. |
| 40553 |16 |Sessionen har avslutats på grund av hög minnesanvändning. Försök att ändra en fråga så att färre rader bearbetas.<br/><br/> Minska antalet `ORDER BY` och `GROUP BY` åtgärder i Transact-SQL-kod bidrar till att minska minneskraven för din fråga. |
| 40604 |16 |Det gick inte CREATE/ALTER DATABASE eftersom det skulle innebära att kvoten på servern. |
| 40606 |16 |Koppla databaser stöds inte i den här versionen av SQL Server. |
| 40607 |16 |Windows-inloggningar stöds inte i den här versionen av SQL Server. |
| 40611 |16 |Servrar kan ha högst 128 brandväggsregler ska definieras. |
| 40614 |16 |Första IP-adress för brandväggsregeln får inte överstiga sista IP-adress. |
| 40615 |16 |Det går inte att öppna servern '{0}' begärdes vid inloggningen. Klienten med IP-adressen{1}' tillåts inte att ansluta till servern.<br /><br />Om du vill aktivera åtkomst måste använda SQL Database-portalen eller köra sp\_ange\_brandväggen\_regel i huvuddatabasen för att skapa en brandväggsregel för IP-adressen eller adressintervallet. Det kan ta upp till fem minuter för att ändringen ska börja gälla. |
| 40617 |16 |Brandväggsregelns namn som börjar med (namn) är för långt. Maximal längd är 128. |
| 40618 |16 |Brandväggsregelns namn får inte vara tom. |
| 40620 |16 |Inloggningen misslyckades för användaren ”%.&#x2a;ls”. Det gick inte att ändra lösenordet. Lösenordsändring under inloggning stöds inte i den här versionen av SQL Server. |
| 40627 |20 |Åtgärden på servern '{0}”och databas”{1}”pågår. Vänta några minuter innan du försöker igen. |
| 40630 |16 |Lösenordsverifieringen misslyckades. Lösenordet uppfyller inte principkraven eftersom det är för kort. |
| 40631 |16 |Det lösenord som du angav är för långt. Lösenordet ska innehålla högst 128 tecken. |
| 40632 |16 |Lösenordsverifieringen misslyckades. Lösenordet uppfyller inte principkraven eftersom det inte är tillräckligt komplicerat. |
| 40636 |16 |Det går inte att använda ett reserverat namn (%.&#x2a;ls) i den här åtgärden. |
| 40638 |16 |Ogiltigt prenumerations-id (prenumerations-id). Prenumerationen finns inte. |
| 40639 |16 |Begäran stämmer inte överens med schemat: (schemafel). |
| 40640 |20 |Ett oväntat undantag påträffades av servern. |
| 40641 |16 |Den angivna platsen är ogiltig. |
| 40642 |17 |Servern är upptagen för närvarande. Försök igen senare. |
| 40643 |16 |Den angivna x-ms-version huvudets värde är ogiltigt. |
| 40644 |14 |Det gick inte att bevilja åtkomst till den angivna prenumerationen. |
| 40645 |16 |Servernamn (servernamn) får inte vara tomt eller null. Det kan bara bestå av gemener ”a”-”z”, siffrorna 0-9 och bindestreck. Bindestreck kan inte leda eller sist i namnet. |
| 40646 |16 |Prenumerations-ID får inte vara tom. |
| 40647 |16 |Prenumerationen (prenumerations-id) har inte servern (servernamn). |
| 40648 |17 |För många begäranden har utförts. Försök igen senare. |
| 40649 |16 |Ogiltig innehållstyp anges. Endast application/xml stöds. |
| 40650 |16 |Prenumerationen (prenumerations-id) finns inte eller är inte redo för åtgärden. |
| 40651 |16 |Det gick inte att skapa servern eftersom prenumerationen (prenumerations-id) har inaktiverats. |
| 40652 |16 |Det går inte att flytta eller skapa server. Prenumerationen (prenumerations-id) kommer att överskrida serverkvoten. |
| 40671 |17 |Kommunikationsfel mellan gatewayen och management-tjänsten. Försök igen senare. |
| 40852 |16 |Det går inte att öppna databasen ' %. \*ls' på servern ' %. \*ls' som begärdes vid inloggningen. Åtkomst till databasen tillåts endast med hjälp av en säkerhetsaktiverad anslutningssträng. För att komma åt den här databasen, ändra anslutningssträngarna innehålla säker i serverns FQDN - 'server name'.database.windows .net ska ändras till ”server name” .database. `secure`. windows.net. |
| 40914 | 16 | Det går inte att öppna servern '*[servernamn]*' begärdes vid inloggningen. Klienten är inte tillåtet att ansluta till servern.<br /><br />Om du vill åtgärda, Överväg att lägga till en [virtuell nätverksregel](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |SQL Azure-systemet är under belastning och är att placera en övre gräns på samtidiga DB CRUD-åtgärder för en enskild server (t.ex. skapa databasen). Den server som anges i felmeddelandet har överskridit det maximala antalet samtidiga anslutningar. Försök igen senare. |
| 45169 |16 |SQL azure-systemet är under belastning och placerar en övre gräns för antalet samtidiga server CRUD-åtgärder för en enskild prenumeration (t.ex. Skapa server). Den prenumeration som anges i felmeddelandet har överskridit det maximala antalet samtidiga anslutningar och begäran nekades. Försök igen senare. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure SQL Database-funktioner](sql-database-features.md).
* Läs mer om [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md).
* Läs mer om [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).


---
title: Översikt över elastisk fråga i Azure SQL Database | Microsoft Docs
description: Elastisk fråga kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 01/19/2019
ms.openlocfilehash: fad9437a631254d6c60d6d97267ae111d195040f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585718"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database elastisk fråga översikt (förhandsversion)

Elastic query-funktionen (i förhandsversion) kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser i Azure SQL Database. Det kan du utföra frågor mellan databaser att ansluter till fjärrtabeller och för att ansluta Microsoft och verktyg från tredje part (Excel, Power BI, Tableau, osv.) att söka i datanivåer med flera databaser. Med den här funktionen kan du skala ut frågor till stora mängder datanivåer i SQL-databas och visualisera resultat i business intelligence (BI)-rapporter.

## <a name="why-use-elastic-queries"></a>Varför ska man använda elastiska frågor

### <a name="azure-sql-database"></a>Azure SQL Database

Frågor, Azure SQL-databaser helt i T-SQL. Detta möjliggör skrivskyddade frågor efter remote databaser och erbjuder ett alternativ för befintliga lokala SQL Server-kunder att migrera program som använder tre och fyra deltid namn eller länkad server till SQL DB.

### <a name="available-on-standard-tier"></a>Tillgängliga på standard-nivån

Elastisk fråga stöds för både Standard och Premium-nivåerna. Se avsnittet på begränsningar i förhandsversionen nedan på prestanda begränsningar för lägre tjänstnivåer.

### <a name="push-parameters-to-remote-databases"></a>Skicka parametrar till fjärranslutna databaser

Elastiska frågor kan nu skicka SQL-parametrar till de fjärranslutna databaserna för körning.

### <a name="stored-procedure-execution"></a>Lagrad procedurkörning

Köra lagrade RPC-anrop eller fjärranslutna funktioner med hjälp av [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilitet

Externa tabeller med elastisk fråga kan referera till fjärrtabeller med ett annat schema eller tabellnamn.

## <a name="elastic-query-scenarios"></a>Elastisk fråga scenarier

Målet är att underlätta förfrågningar till scenarier där rader bidra till ett enda övergripande resultat i flera databaser. Frågan kan antingen bestå av användaren eller programmet direkt eller indirekt via verktyg som är anslutna till databasen. Detta är särskilt användbart när du skapar rapporter med hjälp av kommersiella BI eller verktyg för integrering av data eller alla program som inte kan ändras. Du kan fråga över flera databaser med den välbekanta SQL Server-anslutningen miljö i verktyg som Excel, Power BI, Tableau eller Cognos med en elastisk förfrågan.
En elastisk förfrågan tillåter enkel åtkomst till en hel samling databaser via frågor som utfärdats av SQL Server Management Studio eller Visual Studio och underlättar databasöverskridande frågor från Entity Framework eller andra ORM-miljöer. Bild 1 visar ett scenario där en befintlig molnprogram (som använder den [klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)) bygger på en utskalad data nivå och en elastisk förfrågan används för rapportering mellan databaser.

**Bild 1** elastisk fråga som används på utskalad datanivå

![Elastisk fråga som används på utskalad datanivå][1]

För elastisk fråga kännetecknas av följande topologier:

* **Vertikal partitionering - databasöverskridande frågor** (topologi 1): Data partitioneras vertikalt mellan ett antal databaser i en datanivå. Normalt finnas olika uppsättningar med tabeller på olika databaser. Det innebär att schemat är annorlunda på olika databaser. Alla tabeller för inventering är exempel på en databas när alla redovisning-relaterade tabeller är på en andra databas. Vanliga användningsområden med den här topologin kräver ett till frågor, eller att kompilera rapporter över tabeller i flera databaser.
* **Horisontell partitionering – horisontell partitionering** (topologi 2): Data partitioneras vågrätt för att distribuera rader i en utskalad data nivå. Schemat är identiska på alla deltagande databaser med den här metoden. Den här metoden kallas även ”horisontell partitionering”. Horisontell partitionering kan utföras och hanterade med (1) elastic database-verktygen bibliotek eller (2) self-partitionering. En elastisk förfrågan används för att fråga efter eller kompilera rapporter över många skärvor.

> [!NOTE]
> Elastisk fråga fungerar bäst för scenarier där de flesta av bearbetningen (filtrering, aggregering) kan utföras på extern källa-sida-rapportering. Det är inte lämpligt för ETL-åtgärder där stora mängder data överförs från fjärranslutna databaser. För ökad reporting arbetsbelastning eller informationslager-scenarier med mer komplexa frågor kan även överväga att använda [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikal partitionering - databasöverskridande frågor

Om du vill börja koda, se [komma igång med databasöverskridande frågor (vertikala partitioner)](sql-database-elastic-query-getting-started-vertical.md).

En elastisk förfrågan kan användas för att se data i en SQL-databas som är tillgängliga för andra SQL-databaser. På så sätt kan frågor från en databas att referera till tabellerna i någon annan fjärranslutna SQL-databas. Det första steget är att definiera en extern datakälla för varje fjärrdatabasen. Den externa datakällan har definierats i den lokala databasen som du vill få åtkomst till tabeller som finns på fjärrdatabasen. Det krävs inga ändringar på fjärrdatabasen. För vanliga vertikal partitionering scenarier där olika databaser har olika scheman, kan elastiska frågor användas för att implementera vanliga användningsområden, till exempel åtkomst till referensdata och databasöverskridande frågor.

> [!IMPORTANT]
> Du måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten ingår behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.
>

**Referensdata**: Topologin används för hantering av referens. I figuren nedan visas hålls två tabeller (T1 och T2) med referensdata på en dedikerad databas. Med hjälp av en elastisk förfrågan, du kan nu komma åt tabeller T1 och T2 via en fjärranslutning från andra databaser, enligt bilden. Använd topologi 1 om referenstabeller är små eller fjärranslutna frågor till referenstabellen har selektiv predikat.

**Bild 2** vertikal partitionering – med hjälp av elastisk fråga fråga referensdata

![Vertikal partitionering – med hjälp av elastisk fråga fråga referensdata][3]

**Databasöverskridande frågor**: Elastiska frågor aktivera användningsfall som kräver fråga över flera SQL-databaser. Bild 3 visar fyra olika databaser: CRM, inventering, HR och produkter. Frågor som utförs i en av databaserna måste också åtkomst till en eller alla andra databaser. Med en elastisk förfrågan kan konfigurera du din databas för det här fallet genom att köra några enkla DDL-instruktionerna på var och en av de fyra databaserna. Efter den här engångskonfiguration är åtkomst till en fjärrtabell lika enkelt som refererar till en lokal tabell från T-SQL-frågor eller från din BI-verktyg. Den här metoden rekommenderas om fjärrfrågor inte returnerar stora resultat.

**Bild 3** vertikal partitionering – med hjälp av elastisk fråga att fråga över olika databaser

![Vertikal partitionering – med hjälp av elastisk fråga att fråga över olika databaser][4]

Med följande steg konfigurerar elastiska databasfrågor för vertikal partitionering scenarier som kräver åtkomst till en tabell som finns på fjärranslutna SQL-databaser med samma schema:

* [Skapa HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Skapa och släpp extern DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource av typen **RDBMS**
* [Skapa och släpp extern tabell](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Du kan komma åt fjärrtabellen ”mytable” som om det vore en lokal tabell när du har kört DDL-instruktionerna. Azure SQL Database automatiskt öppnar en anslutning till fjärrdatabasen bearbetar din begäran på fjärrdatabasen och returnerar resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horisontell partitionering – horisontell partitionering

Med hjälp av elastisk fråga för att utföra en shardad rapportuppgifter, det vill säga horisontellt, datanivå kräver en [fragmentkartan för elastisk databas](sql-database-elastic-scale-shard-map-management.md) att representera databaserna på datanivå. Normalt bara en enda fragmentkartan används i det här scenariot och en särskild databas med elastisk frågefunktioner (huvudnod) fungerar som ingångspunkt för rapportering frågor. Endast den här dedikerade databasen måste ha åtkomst till i fragmentkartan. Bild 4 illustrerar den här topologin och dess konfiguration med elastisk fråga i databasen och shard kartan. Databaser på datanivå kan vara av alla Azure SQL Database version eller utgåva. Mer information om klientbiblioteket för elastiska databaser och skapa fragmentkartor finns [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md).

**Bild 4** horisontell partitionering – med hjälp av elastisk fråga för rapportering över nivåerna shardade data

![Horisontell partitionering – med hjälp av elastisk fråga för rapportering över nivåerna shardade data][5]

> [!NOTE]
> Elastisk fråga databas (huvudnod) kan vara en separat databas eller det kan vara samma databas som är värd för fragmentkartan.
> Den konfiguration du väljer och kontrollera att den tjänstnivån beräkna storleken på den här databasen är tillräckligt högt för att hantera den förväntade mängden inloggning/frågebegäranden.

Med följande steg konfigurerar frågor för horisontell partitionering scenarier som kräver åtkomst till en uppsättning tabeller som finns på (normalt) flera fjärranslutna SQL-databaser i elastiska databaser:

* [Skapa HUVUDNYCKEL](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Skapa en [fragmentkartan](sql-database-elastic-scale-shard-map-management.md) som representerar din datanivå med hjälp av klientbiblioteket för elastiska databaser.
* [Skapa och släpp extern DATAKÄLLA](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource av typen **SHARD_MAP_MANAGER**
* [Skapa och släpp extern tabell](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

När du har utfört dessa steg kan du komma åt vågrätt partitionerad tabell ”mytable” som om det vore en lokal tabell. Azure SQL Database automatiskt öppnas flera parallella anslutningar till fjärr-databaser där tabellerna lagras fysiskt, bearbetar begäranden på fjärr-databaser och returnerar resultatet.
Mer information om de steg som krävs för horisontell partitionering scenariot finns i [elastisk fråga för horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md).

Om du vill börja koda, se [komma igång med elastisk fråga för horisontell partitionering (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-fråga

När du har definierat dina externa datakällor och dina externa tabeller kan använda du reguljära anslutningssträngar för SQL Server för att ansluta till databaser där du definierade dina externa tabeller. Du kan sedan köra T-SQL-uttryck över dina externa tabeller för anslutningen med begränsningar som beskrivs nedan. Du hittar mer information och exempel för T-SQL-frågor i dokumentationen-avsnitten om [horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md) och [vertikal partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server-anslutningssträngar för att ansluta dina program och verktyg för integrering av BI eller data till databaser som har externa tabeller. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. När du är ansluten, se elastisk fråga i databasen och de externa tabellerna i databasen som du skulle göra med någon annan SQL Server-databas som du ansluter till med verktyg.

> [!IMPORTANT]
> Autentisering med hjälp av Azure Active Directory med elastiska frågor stöds inte för närvarande.

## <a name="cost"></a>Kostnad

Elastisk fråga ingår i kostnaden för Azure SQL Database-databaser. Observera att topologier där remote databaserna är i ett annat Datacenter elastisk fråga slutpunkt stöds, men utgående data från fjärranslutna databaser debiteras regelbundet [Azure-avgifter](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Kör din första elastisk fråga kan ta upp till några minuter på Standard-tjänstnivå. Nu är nödvändigt att läsa in de elastiska frågefunktioner; läser in prestanda förbättras med högre tjänste- och storlekar.
* Kör skript på externa datakällor eller externa tabeller från SSMS eller SSDT stöds inte ännu.
* Import/Export för SQL DB stöder ännu inte externa datakällor och externa tabeller. Om du behöver använda Import/Export, ta bort dessa objekt innan du exporterar och sedan återskapa dem när du har importerat.
* Elastisk fråga stöder för närvarande endast skrivskyddad åtkomst till externa tabeller. Du kan dock använda alla T-SQL-funktioner på databasen där den externa tabellen definieras. Detta kan vara användbart att t.ex. Spara tillfällig resultat med till exempel, väljer < column_list > i < local_table > eller definiera lagrade procedurer för elastisk fråga i databasen som refererar till externa tabeller.
* Förutom nvarchar(max) stöds LOB-typerna inte i extern tabelldefinitioner. Som en lösning kan du skapa en vy på fjärrdatabasen som kastar LOB-typ till nvarchar(max), definiera din extern tabell över vyn i stället för bastabellen och skicka den tillbaka till den ursprungliga LOB-typen i dina frågor.
* Kolumner av datatypen nvarchar(max) i resultatet set inaktivera avancerade batchbearbetning technics som används i elastisk fråga implementering och kan påverka prestanda för fråga om en storleksordning eller två i storlek i icke-kanoniska användningsfall där det är stor mängd ej sammanräknade data överförs till följd av frågan.
* Kolumnstatistik via externa tabeller stöds inte för närvarande. Tabellstatistik stöds, men måste därför skapas manuellt.

## <a name="feedback"></a>Feedback

Dela feedback om din upplevelse med elastiska frågor med oss nedan, på MSDN-forum eller på Stack Overflow. Vi är intresserade av alla typer av feedback om tjänsten (defekter, grov kanter, funktionen luckor).

## <a name="next-steps"></a>Nästa steg

* Se en vertikal partitionering självstudie [komma igång med databasöverskridande frågor (vertikala partitioner)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs om horisontell partitionering (sharding) finns i [komma igång med elastisk fråga för horisontell partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade data, se [fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktionen på en enskild remote Azure SQL-databas eller en uppsättning databaser som fungerar som shards i en vågrät partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

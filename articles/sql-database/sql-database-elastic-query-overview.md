---
title: Översikt över elastisk fråga
description: Elastisk fråga gör det möjligt att köra en Transact-SQL-fråga som sträcker sig över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 07/01/2019
ms.openlocfilehash: 9566ac7169144d984f9200734c99eb10368b3142
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823744"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Översikt över Azure SQL Database elastisk fråga (för hands version)

Funktionen elastisk fråga (i för hands version) gör att du kan köra en Transact-SQL-fråga som sträcker sig över flera databaser i Azure SQL Database. Det gör att du kan utföra frågor över flera databaser för att få åtkomst till fjärrtabeller och ansluta Microsoft och verktyg från tredje part (Excel, Power BI, Tableau osv.) för att fråga över data nivåer med flera databaser. Med den här funktionen kan du skala ut frågor till stora data nivåer i SQL Database och visualisera resultaten i Business Intelligence (BI) rapporter.

## <a name="why-use-elastic-queries"></a>Varför ska man använda elastiska frågor

### <a name="azure-sql-database"></a>Azure SQL Database

Fråga över Azure SQL-databaser helt i T-SQL. Detta möjliggör skrivskyddad fråga i fjärrdatabaser och ger ett alternativ för aktuella lokala SQL Server-kunder att migrera program med tre-och fyr dels namn eller länkad server till SQL DB.

### <a name="available-on-standard-tier"></a>Tillgängligt på standard nivån

Elastiska frågor stöds på tjänst nivåerna standard och Premium. I avsnittet om förhands gransknings begränsningar nedan finns prestanda begränsningar för lägre tjänst nivåer.

### <a name="push-parameters-to-remote-databases"></a>Push-parametrar till fjärrdatabaser

Elastiska frågor kan nu skicka SQL-parametrar till fjärrdatabaserna för körning.

### <a name="stored-procedure-execution"></a>Körning av lagrad procedur

Kör fjärranslutna procedur anrop eller fjärrfunktioner med hjälp av [sp\_köra \_fjärr anslutning](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilitet

Externa tabeller med elastisk fråga kan referera till fjärrtabeller med ett annat schema-eller tabell namn.

## <a name="elastic-query-scenarios"></a>Elastiska fråge scenarier

Målet är att under lätta frågor i scenarier där flera databaser bidrar med rader till ett enda övergripande resultat. Frågan kan antingen bestå av användaren eller programmet direkt eller indirekt via verktyg som är anslutna till databasen. Detta är särskilt användbart när du skapar rapporter med hjälp av kommersiella BI-eller data integrerings verktyg eller program som inte kan ändras. Med en elastisk fråga kan du fråga över flera databaser med hjälp av den välbekanta SQL Server anslutnings upplevelsen i verktyg som Excel, Power BI, Tableau eller Cognos.
En elastisk fråga ger enkel åtkomst till en hel samling databaser via frågor som utfärdats av SQL Server Management Studio eller Visual Studio och underlättar frågor från flera databaser från Entity Framework eller andra ORM-miljöer. Bild 1 visar ett scenario där ett befintligt moln program (som använder [klient biblioteket för elastiska databaser](sql-database-elastic-database-client-library.md)) bygger på en skalad data nivå och en elastisk fråga används för rapportering mellan databaser.

**Bild 1** Elastisk fråga som används för utskalade data nivåer

![Elastisk fråga som används för utskalade data nivåer][1]

Kund scenarier för elastiska frågor kännetecknas av följande topologier:

* **Vertikal partitionering-frågor över flera databaser** (topologi 1): data partitioneras lodrätt mellan ett antal databaser i en data nivå. Vanligt vis finns olika uppsättningar med tabeller i olika databaser. Det innebär att schemat är olika i olika databaser. Till exempel är alla tabeller för inventering i en databas medan alla redovisnings tabeller finns på en andra databas. Vanliga användnings fall med den här topologin kräver att en fråga över eller kompilerar rapporter över tabeller i flera databaser.
* **Horisontell partitionering-horisontell partitionering** (topologi 2): data är partitionerade vågrätt för att distribuera rader över en utskalad data nivå. Med den här metoden är schemat identiskt på alla deltagande databaser. Den här metoden kallas även för "horisontell partitionering". Horisontell partitionering kan utföras och hanteras med hjälp av (1) de elastiska databas verktyg biblioteken eller (2) Self-horisontell partitionering. En elastisk fråga används för att fråga eller kompilera rapporter över flera Shards.

> [!NOTE]
> Elastisk fråga fungerar bäst för rapporterings scenarier där merparten av bearbetningen (filtrering, agg regering) kan utföras på den externa käll sidan. Det är inte lämpligt för ETL-åtgärder där stora mängder data överförs från fjärrdatabaser. För tung rapportering av arbets belastningar eller data lager scenarier med mer komplexa frågor kan du även överväga att använda [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikal partitionering-frågor mellan databaser

För att börja koda, se [komma igång med kors databas fråga (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).

En elastisk fråga kan användas för att göra data som finns i en SQL-databas tillgänglig för andra SQL-databaser. Detta gör att frågor från en databas kan referera till tabeller i andra fjärranslutna SQL-databaser. Det första steget är att definiera en extern data källa för varje fjärran sluten databas. Den externa data källan definieras i den lokala databasen från vilken du vill få åtkomst till tabeller som finns i fjärrdatabasen. Inga ändringar krävs för fjärrdatabasen. För vanliga lodräta partitionerings scenarier där olika databaser har olika scheman, kan elastiska frågor användas för att implementera vanliga användnings fall som till exempel åtkomst till referens data och kors databas frågor.

> [!IMPORTANT]
> Du måste ha behörighet för att ändra en extern DATA källa. Den här behörigheten ingår i ALTER DATABASE-behörigheten. ÄNDRA behörigheter för en extern DATA källa krävs för att referera till den underliggande data källan.
>

**Referens data**: topologin används för referens data hantering. I bilden nedan sparas två tabeller (T1 och T2) med referens data i en dedikerad databas. Med en elastisk fråga kan du nu komma åt tabeller T1 och T2 via fjärr anslutning från andra databaser, som du ser i bilden. Använd topologi 1 om referens tabeller är små eller fjärranslutna frågor till referens tabellen har selektiva predikat.

**Bild 2** Vertikal partitionering-använda elastisk fråga för att fråga referens data

![Vertikal partitionering-använda elastisk fråga för att fråga referens data][3]

**Fråga**i flera databaser: elastiska frågor möjliggör användnings fall som kräver frågor över flera SQL-databaser. Bild 3 visar fyra olika databaser: CRM, Inventory, HR och Products. Frågor som utförs i en av databaserna måste också ha åtkomst till en eller alla andra databaser. Med en elastisk fråga kan du konfigurera databasen för det här fallet genom att köra några enkla DDL-uttryck på var och en av de fyra databaserna. Efter den här konfigurationen är det lika enkelt att komma åt en fjärrtabell som hänvisar till en lokal tabell från T-SQL-frågorna eller från dina BI-verktyg. Den här metoden rekommenderas om fjärrfrågor inte returnerar stora resultat.

**Bild 3** Vertikal partitionering-använda elastisk fråga för att fråga mellan olika databaser

![Vertikal partitionering-använda elastisk fråga för att fråga mellan olika databaser][4]

Följande steg konfigurerar elastiska databas frågor för lodräta partitionerings scenarier som kräver åtkomst till en tabell som finns på fjärranslutna SQL-databaser med samma schema:

* [Skapa huvud nyckel](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [Skapa databasens begränsade](https://msdn.microsoft.com/library/mt270260.aspx) autentiseringsuppgifter för databasen
* [Skapa/släpp extern data källa](https://msdn.microsoft.com/library/dn935022.aspx) av typen **RDBMS**
* [Skapa/släpp extern tabell tabell](https://msdn.microsoft.com/library/dn935021.aspx)

När du har kört DDL-instruktionerna kan du komma åt fjärrtabellen "untable" som om den vore en lokal tabell. Azure SQL Database öppnar automatiskt en anslutning till fjärrdatabasen, bearbetar din begäran på fjärrdatabasen och returnerar resultatet.

## <a name="horizontal-partitioning---sharding"></a>Horisontell partitionering – horisontell partitionering

Om du använder elastiska frågor för att utföra rapporterings uppgifter över en shardade, det vill säga vågrätt partitionerat, kräver data nivån en [elastisk databas Shard-karta](sql-database-elastic-scale-shard-map-management.md) för att representera data skiktets databaser. Normalt används endast en enda Shard-karta i det här scenariot och en dedikerad databas med elastiska fråge funktioner (Head Node) fungerar som start punkt för rapporterings frågor. Endast den här dedikerade databasen behöver åtkomst till Shard-kartan. Bild 4 illustrerar den här topologin och dess konfiguration med den Elastic Query-databasen och Shard-kartan. Databaserna i data skiktet kan vara av valfri Azure SQL Database version eller utgåva. Mer information om klient biblioteket för Elastic Database och hur du skapar Shard Maps finns i [Shard Map Management](sql-database-elastic-scale-shard-map-management.md).

**Bild 4** Horisontell partitionering-använda elastisk fråga för rapportering över shardade data nivåer

![Horisontell partitionering-använda elastisk fråga för rapportering över shardade data nivåer][5]

> [!NOTE]
> Elastic Query-databas (Head Node) kan vara en separat databas, eller så kan den vara samma databas som är värd för Shard-kartan.
> Oavsett vilken konfiguration du väljer kontrollerar du att tjänst nivå och beräknings storlek för databasen är tillräckligt högt för att hantera den förväntade mängden inloggnings-/fråge förfrågningar.

Följande steg konfigurerar elastiska databas frågor för horisontella partitionerings scenarier som kräver åtkomst till en uppsättning tabeller som finns på (vanligt vis) flera fjärranslutna SQL-databaser:

* [Skapa huvud nyckel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Skapa databasens begränsade](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) autentiseringsuppgifter för databasen
* Skapa en [Shard-karta](sql-database-elastic-scale-shard-map-management.md) som representerar data nivån med klient biblioteket för Elastic Database.
* [Skapa/släpp extern data källa källa](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) av typen **SHARD_MAP_MANAGER**
* [Skapa/släpp extern tabell tabell](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

När du har utfört de här stegen kan du komma åt den vågrätt partitionerade tabellen "Tabell" som om den vore en lokal tabell. Azure SQL Database öppnar automatiskt flera parallella anslutningar till fjärrdatabaserna där tabellerna lagras fysiskt, bearbetar begär anden i fjärrdatabaserna och returnerar resultaten.
Mer information om de steg som krävs för det horisontella partitionerings scenariot finns i [elastisk fråga för horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md).

För att börja koda, se [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-fråga

När du har definierat dina externa data källor och dina externa tabeller kan du använda vanliga SQL Server anslutnings strängar för att ansluta till databaserna där du definierade dina externa tabeller. Du kan sedan köra T-SQL-uttryck över dina externa tabeller på den anslutningen med de begränsningar som beskrivs nedan. Du hittar mer information och exempel på T-SQL-frågor i dokumentations avsnitten för [horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md) och [vertikal partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server anslutnings strängar för att ansluta dina program och BI-eller data integrerings verktyg till databaser som har externa tabeller. Kontrol lera att SQL Server stöds som data källa för ditt verktyg. När du har anslutit kan du se den elastiska fråge databasen och de externa tabellerna i databasen på samma sätt som du gör med andra SQL Server-databaser som du ansluter till med ditt verktyg.

> [!IMPORTANT]
> Autentisering med Azure Active Directory med elastiska frågor stöds inte för närvarande.

## <a name="cost"></a>Kostnad

Elastisk fråga ingår i kostnaden för Azure SQL Database databaser. Observera att topologier där fjärrdatabaserna finns i ett annat data Center än den elastiska fråge slut punkten stöds, men utgående data från fjärrdatabaser debiteras regelbundet med [Azure-priser](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Det kan ta upp till några minuter att köra din första elastiska fråga på standard tjänst nivån. Den här tiden är nödvändig för att läsa in funktioner för elastiska frågor. inläsning av prestanda förbättras med högre tjänst nivåer och beräknings storlekar.
* Det finns ännu inte stöd för skript för externa data källor eller externa tabeller från SSMS eller SSDT.
* Import/export för SQL DB stöder ännu inte externa data källor och externa tabeller. Om du behöver använda import/export ska du ta bort dessa objekt innan du exporterar och sedan återskapa dem efter att du har importerat.
* Elastisk fråga stöder för närvarande endast skrivskyddad åtkomst till externa tabeller. Du kan dock använda fullständiga T-SQL-funktioner i databasen där den externa tabellen definieras. Detta kan vara användbart för, t. ex. Spara temporära resultat med, till exempel välja < column_list > i < local_table > eller definiera lagrade procedurer på den elastiska Frågeparametern som refererar till externa tabeller.
* Med undantag för nvarchar (max) stöds inte LOB-typer (inklusive spatiala typer) i externa tabell definitioner. Som en lösning kan du skapa en vy på den fjärranslutna databasen som överför LOB-typen till nvarchar (max), definiera den externa tabellen över vyn i stället för bas tabellen och sedan omvandla den till den ursprungliga LOB-typen i dina frågor.
* Kolumner av typen nvarchar (max) i resultat uppsättning inaktivera avancerad batching-Technics som används i elastiska fråge implementeringar och kan påverka prestanda för frågor i en storleksordning, eller till och med två storleksordningar i icke-kanoniska användnings fall där stor del av icke-aggregerade data överförs till följd av fråga.
* Kolumn statistik över externa tabeller stöds inte för närvarande. Tabell statistik stöds, men du måste skapa den manuellt.
* Elastisk fråga fungerar endast med Azure SQL Database. Du kan inte använda den för att skicka frågor till lokala SQL Server eller SQL Server i en virtuell dator.

## <a name="feedback"></a>Feedback

Dela feedback om din upplevelse med elastiska frågor med oss nedan, i MSDN-forumen eller på Stack Overflow. Vi är intresserade av alla typer av feedback om tjänsten (fel, grova kanter, funktions luckor).

## <a name="next-steps"></a>Nästa steg

* En lodrät partitionerings guide finns i [komma igång med kors databas fråga (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* För syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudie för horisontell partitionering (horisontell partitionering) finns i [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](sql-database-elastic-query-getting-started.md).
* För syntax och exempel frågor för vågrätt partitionerade data, se [fråga efter vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_-fjärråtkomst](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr-Azure SQL Database eller uppsättning databaser som är som Shards i ett schema med vågrät partitionering.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

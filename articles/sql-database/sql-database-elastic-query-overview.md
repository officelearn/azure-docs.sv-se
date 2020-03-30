---
title: Översikt över elastiska frågor
description: Med elastisk fråga kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: 827fab0661a58bfa7d28452960ea6df64d18bf84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873751"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Översikt över elastiska frågor i Azure SQL Database (förhandsversion)

Med den elastiska frågefunktionen (i förhandsversion) kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser i Azure SQL Database. Det gör att du kan utföra flerdatabasfrågor för att komma åt fjärrtabeller och ansluta Microsoft och verktyg från tredje part (Excel, Power BI, Tableau, etc.) för att fråga över datanivåer med flera databaser. Med den här funktionen kan du skala ut frågor till stora datanivåer i SQL Database och visualisera resultaten i BI-rapporter (Business Intelligence).

## <a name="why-use-elastic-queries"></a>Varför använda elastiska frågor

### <a name="azure-sql-database"></a>Azure SQL Database

Fråga över Azure SQL-databaser helt i T-SQL. Detta gör det möjligt för skrivskyddade frågor om fjärrdatabaser och ger ett alternativ för aktuella lokala SQL Server-kunder att migrera program med tre- och fyradelade namn eller länkad server till SQL DB.

### <a name="available-on-standard-tier"></a>Tillgänglig på standardnivå

Elastisk fråga stöds på både standard- och Premium-tjänstnivåerna. Se avsnittet om förhandsbegränsningar nedan om prestandabegränsningar för lägre tjänstnivåer.

### <a name="push-parameters-to-remote-databases"></a>Skicka parametrar till fjärrdatabaser

Elastiska frågor kan nu skicka SQL-parametrar till fjärrdatabaser för körning.

### <a name="stored-procedure-execution"></a>Körning av lagrad procedur

Kör fjärrlagrade proceduranrop eller fjärrfunktioner med [sp\_kör \_fjärrkontroll](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilitet

Externa tabeller med elastisk fråga kan referera till fjärrtabeller med ett annat schema eller tabellnamn.

## <a name="elastic-query-scenarios"></a>Elastiska frågescenarier

Målet är att underlätta frågescenarier där flera databaser bidrar med rader till ett enda övergripande resultat. Frågan kan antingen bestå av användaren eller programmet direkt, eller indirekt genom verktyg som är anslutna till databasen. Detta är särskilt användbart när du skapar rapporter, använder kommersiella BI- eller dataintegrationsverktyg eller program som inte kan ändras. Med en elastisk fråga kan du fråga över flera databaser med hjälp av den välbekanta SQL Server-anslutningsupplevelsen i verktyg som Excel, Power BI, Tableau eller Cognos.
En elastisk fråga ger enkel åtkomst till en hel samling databaser via frågor som utfärdats av SQL Server Management Studio eller Visual Studio, och underlättar frågar över flera databaser från Entity Framework eller andra ORM-miljöer. Bild 1 visar ett scenario där ett befintligt molnprogram (som använder [klientbiblioteket](sql-database-elastic-database-client-library.md)för elastisk databas) bygger på en utskalad datanivå och en elastisk fråga används för rapportering mellan databaser.

**Bild 1** Elastisk fråga som används på skalad datanivå

![Elastisk fråga som används på skalad datanivå][1]

Kundscenarier för elastisk fråga kännetecknas av följande topologier:

* **Vertikal partitionering - Flerdatabasfrågor** (Topologi 1): Data partitioneras lodrätt mellan ett antal databaser på en datanivå. Vanligtvis finns olika uppsättningar tabeller på olika databaser. Det innebär att schemat är olika på olika databaser. Alla tabeller för lager finns till exempel i en databas medan alla redovisningsrelaterade tabeller finns i en andra databas. Vanliga användningsfall med den här topologin kräver att en fråga över eller för att sammanställa rapporter över tabeller i flera databaser.
* **Vågrät partitionering – Sharding** (Topologi 2): Data partitioneras vågrätt för att distribuera rader över en skalad datanivå. Med den här metoden är schemat identiskt på alla deltagande databaser. Detta tillvägagångssätt kallas också "sharding". Sharding kan utföras och hanteras med hjälp av (1) bibliotek för elastiska databasverktyg eller (2) självskärpning. En elastisk fråga används för att fråga eller kompilera rapporter över många shards. Shards är vanligtvis databaser i en elastisk pool. Du kan tänka på elastisk fråga som ett effektivt sätt att fråga alla databaser med elastisk pool på en gång, så länge databaser delar det gemensamma schemat.

> [!NOTE]
> Elastisk fråga fungerar bäst för rapporteringsscenarier där de flesta bearbetningen (filtrering, aggregering) kan utföras på den externa källsidan. Den är inte lämplig för ETL-operationer där stora mängder data överförs från fjärrdatabaser. För tunga rapporteringsarbetsbelastningar eller scenarier för datalagring med mer komplexa frågor bör du också använda [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Lodrät partitionering - frågor över flera databaser

Information om hur du börjar koda finns i [Komma igång med fråga över flera databaser (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).

En elastisk fråga kan användas för att göra data som finns i en SQL-databas tillgängliga för andra SQL-databaser. På så sätt kan frågor från en databas referera till tabeller i någon annan fjärr-SQL-databas. Det första steget är att definiera en extern datakälla för varje fjärrdatabas. Den externa datakällan definieras i den lokala databas som du vill få åtkomst till tabeller som finns i fjärrdatabasen från. Inga ändringar behövs i fjärrdatabasen. För typiska vertikala partitioneringsscenarier där olika databaser har olika scheman kan elastiska frågor användas för att implementera vanliga användningsfall, till exempel åtkomst till referensdata och frågar över flera databaser.

> [!IMPORTANT]
> Du måste ha behörigheten ÄNDRA extern datakälla. Den här behörigheten ingår i behörigheten ALTER DATABASE. ÄNDRA EVENTUELLA externa datakällbehörigheter behövs för att referera till den underliggande datakällan.
>

**Referensdata**: Topologin används för hantering av referensdata. I figuren nedan finns två tabeller (T1 och T2) med referensdata förda i en särskild databas. Med hjälp av en elastisk fråga kan du nu fjärrse gå åt tabellerna T1 och T2 från andra databaser, som visas i figuren. Använd topologi 1 om referenstabeller är små eller fjärrfrågor i referenstabellen har selektiva predikat.

**Bild 2** Lodrät partitionering - Använda elastisk fråga för att fråga referensdata

![Lodrät partitionering - Använda elastisk fråga för att fråga referensdata][3]

**Frågar över flera databaser**: Elastiska frågor möjliggör användningsfall som kräver frågor över flera SQL-databaser. Figur 3 visar fyra olika databaser: CRM, Inventory, HR och Products. Frågor som utförs i en av databaserna behöver också åtkomst till en eller alla andra databaser. Med hjälp av en elastisk fråga kan du konfigurera databasen för det här fallet genom att köra några enkla DDL-satser på var och en av de fyra databaserna. Efter den här engångskonfigurationen är åtkomsten till en fjärrtabell lika enkel som att referera till en lokal tabell från dina T-SQL-frågor eller från bi-verktygen. Den här metoden rekommenderas om fjärrfrågorna inte returnerar stora resultat.

**Bild 3** Lodrät partitionering - Använda elastisk fråga för att fråga över olika databaser

![Lodrät partitionering - Använda elastisk fråga för att fråga över olika databaser][4]

I följande steg konfigureras elastiska databasfrågor för vertikala partitioneringsscenarier som kräver åtkomst till en tabell som finns i fjärr-SQL-databaser med samma schema:

* [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx) minmasterkey
* [SKAPA DATABASSCOPED AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx) Mycredential
* [SKAPA/SLÄPPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx) mindatakälla av typen **RDBMS**
* [SKAPA/SLÄPP EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) MYTABLE

När du har kört DDL-satser kan du komma åt fjärrtabellen "mytable" som om det vore en lokal tabell. Azure SQL Database öppnar automatiskt en anslutning till fjärrdatabasen, bearbetar din begäran i fjärrdatabasen och returnerar resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horisontell partitionering - fragmentering

Med hjälp av elastisk fråga för att utföra rapporteringsuppgifter över en fragmenterad, det vill än, vågrätt partitionerad, kräver datanivån en [elastisk databasshardmapp](sql-database-elastic-scale-shard-map-management.md) för att representera databaserna på datanivån. Vanligtvis används endast en enskild fragmentkarta i det här scenariot och en dedikerad databas med elastiska frågefunktioner (huvudnod) fungerar som startpunkt för rapporteringsfrågor. Endast den här dedikerade databasen behöver åtkomst till fragmentkartan. Figur 4 illustrerar den här topologin och dess konfiguration med den elastiska frågedatabasen och fragmentkartan. Databaserna på datanivån kan vara av alla Azure SQL Database-versioner eller versioner. Mer information om klientbiblioteket för elastisk databas och skapa fragmentkartor finns i [Shard map management](sql-database-elastic-scale-shard-map-management.md).

**Bild 4** Vågrät partitionering – Använda elastisk fråga för rapportering över fragmenterade datanivåer

![Vågrät partitionering – Använda elastisk fråga för rapportering över fragmenterade datanivåer][5]

> [!NOTE]
> Elastisk frågedatabas (huvudnod) kan vara separat databas, eller så kan det vara samma databas som är värd för fragmentmappningen.
> Oavsett vilken konfiguration du väljer, se till att tjänstnivån och beräkningsstorleken för databasen är tillräckligt hög för att hantera den förväntade mängden inloggnings-/frågebegäranden.

I följande steg konfigureras elastiska databasfrågor för vågräta partitioneringsscenarier som kräver åtkomst till en uppsättning tabeller som finns på (vanligtvis) flera fjärr-SQL-databaser:

* [SKAPA HUVUDNYCKEL](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) minmasterkey
* [SKAPA DATABASSCOPED AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) Mycredential
* Skapa en [fragmentkarta](sql-database-elastic-scale-shard-map-management.md) som representerar din datanivå med hjälp av klientbiblioteket för elastiska databaser.
* [SKAPA/SLÄPPA EXTERN DATAKÄLLA](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mindatakälla av typen **SHARD_MAP_MANAGER**
* [SKAPA/SLÄPP EXTERN TABELL](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) MYTABLE

När du har utfört dessa steg kan du komma åt den horisontellt partitionerade tabellen "mytable" som om den vore en lokal tabell. Azure SQL Database öppnar automatiskt flera parallella anslutningar till fjärrdatabaserna där tabellerna lagras fysiskt, bearbetar begäranden på fjärrdatabaserna och returnerar resultaten.
Mer information om de steg som krävs för det horisontella partitioneringsscenariot finns i [elastisk fråga för vågrät partitionering](sql-database-elastic-query-horizontal-partitioning.md).

Information om hur du börjar koda finns i [Komma igång med elastisk fråga för vågrät partitionering (sharding)](sql-database-elastic-query-getting-started.md).

> [!IMPORTANT]
> En lyckad körning av elastisk fråga över en stor uppsättning databaser är starkt beroende av tillgängligheten för var och en av databaserna under frågekörningen. Om en av databaserna inte är tillgänglig misslyckas hela frågan. Om du planerar att fråga hundratals eller tusentals databaser samtidigt kontrollerar du att klientprogrammet har återskapat logik inbäddade eller överväger att använda [elastiska databasjobb](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (förhandsgranskning) och frågar mindre delmängder av databaser, konsoliderar resultaten av varje fråga till ett enda mål.

## <a name="t-sql-querying"></a>T-SQL-frågor

När du har definierat dina externa datakällor och dina externa tabeller kan du använda vanliga SQL Server-anslutningssträngar för att ansluta till de databaser där du definierade dina externa tabeller. Du kan sedan köra T-SQL-uttryck över dina externa tabeller på den anslutningen med de begränsningar som beskrivs nedan. Du hittar mer information och exempel på T-SQL-frågor i dokumentationavsnitten för [vågrät partitionering](sql-database-elastic-query-horizontal-partitioning.md) och [vertikal partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server-anslutningssträngar för att ansluta dina program och BI- eller dataintegrationsverktyg till databaser som har externa tabeller. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. När du har anslutit kan du läsa den elastiska frågedatabasen och de externa tabellerna i databasen på samma sätt som med alla andra SQL Server-databaser som du ansluter till med verktyget.

> [!IMPORTANT]
> Autentisering med Azure Active Directory med elastiska frågor stöds för närvarande inte.

## <a name="cost"></a>Kostnad

Elastisk fråga ingår i kostnaden för Azure SQL Database-databaser. Observera att topologier där fjärrdatabaserna finns i ett annat datacenter än slutpunkten för elastiska frågor stöds, men data som går ut från fjärrdatabaser debiteras regelbundet [Azure-priser](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Det kan ta upp till några minuter att köra den första elastiska frågan på standardtjänstnivån. Den här tiden är nödvändig för att läsa in den elastiska frågefunktionen. inläsningsprestanda förbättras med högre tjänstnivåer och beräkningsstorlekar.
* Skriptning av externa datakällor eller externa tabeller från SSMS eller SSDT stöds ännu inte.
* Import/export för SQL DB stöder ännu inte externa datakällor och externa tabeller. Om du behöver använda Importera/exportera släpper du dessa objekt innan du exporterar och återskapar dem igen efter importen.
* Elastisk fråga stöder för närvarande endast skrivskyddad åtkomst till externa tabeller. Du kan dock använda fullständiga T-SQL-funktioner i databasen där den externa tabellen definieras. Detta kan vara användbart> local_table <> column_list <för att t.ex.
* Med undantag för nvarchar(max) stöds inte LOB-typer (inklusive rumsliga typer) i externa tabelldefinitioner. Som en lösning kan du skapa en vy på fjärrdatabasen som castar LOB-typen till nvarchar(max), definiera din externa tabell över vyn i stället för bastabellen och sedan casta tillbaka den till den ursprungliga LOB-typen i dina frågor.
* Kolumner med nvarchar(max) datatyp i resultatuppsättning inaktiverar avancerad batchteknik som används i Elastic Query-implementeringen och kan påverka frågans prestanda för en storleksordning, eller till och med två storleksordningar i icke-kanoniska användningsfall där en stor mängd icke-aggregerade data överförs som ett resultat av frågan.
* Kolumnstatistik över externa tabeller stöds för närvarande inte. Tabellstatistik stöds, men måste skapas manuellt.
* Elastisk fråga fungerar endast med Azure SQL Database. Du kan inte använda den för att fråga lokalt SQL Server eller SQL Server i en virtuell dator.

## <a name="feedback"></a>Feedback

Dela feedback på dina erfarenheter med elastiska frågor med oss nedan, på MSDN-forumen eller på Stack Overflow. Vi är intresserade av alla typer av feedback om tjänsten (defekter, ojämna kanter, funktionsluckor).

## <a name="next-steps"></a>Nästa steg

* En lodrät partitioneringsdjälva finns i [Komma igång med fråga över flera databaser (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax- och exempelfrågor för lodrätt partitionerade data finns i [Fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs för vågrät partitionering (sharding) finns i [Komma igång med elastisk fråga för vågrät partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax- och exempelfrågor för vågrätt partitionerade data finns i [Fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [\_sp \_kör fjärrkontroll](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-uttryck på en enda fjärr Azure SQL-databas eller uppsättning databaser som fungerar som shards i ett vågrätt partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

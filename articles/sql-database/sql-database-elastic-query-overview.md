---
title: "Översikt av Azure SQL Database-elastisk fråga | Microsoft Docs"
description: "Elastisk frågan kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser."
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: a8bf0e2c-bc74-44d0-9b1e-bcc9a6aa2e33
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: mlandzic
ms.openlocfilehash: 6389702b1be5e52c7191e6e57d17b48289e800b2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>SQL-databas elastisk frågan översikt över Azure (förhandsversion)
Funktionen elastisk fråga (under förhandsgranskning) kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser i Azure SQL Database. Det gör att du kan utföra flera databaser frågor till fjärrtabeller och ansluta Microsoft och tredje parts verktyg (Excel, PowerBI, Tableau, etc.) att fråga på datanivåer med flera databaser. Med den här funktionen kan du skala upp frågor till stora mängder datanivåer i SQL-databas och visualisera resultat i business intelligence (BI) rapporter.


## <a name="why-use-elastic-queries"></a>Varför använda elastisk frågor?

**Azure SQL Database**

Fråga på Azure SQL-databaser helt i T-SQL. Detta ger skrivskyddad frågor till fjärranslutna databaser. Detta ger ett alternativ för aktuell lokal SQL Server-kunder att migrera program som använder tre och fyra deltid namn eller länkad server till SQL DB.

**Tillgängligt på standardnivån**

Elastisk frågan stöds på Standard prestandanivån förutom prestanda premiumnivån. Se avsnittet på begränsningar i förhandsversionen nedan på prestanda begränsningar för lägre prestandanivåer.

**Push till fjärranslutna databaser**

Elastisk frågor kan nu skicka SQL-parametrar till fjärranslutna databaser för körning.

**Lagrade proceduren körning**

Köra lagrade RPC-anrop eller remote funktioner med hjälp av [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714).

**Flexibilitet**

Externa tabeller med elastisk fråga kan nu hänvisa till fjärrtabeller med ett annat schema eller tabellnamn.

## <a name="elastic-query-scenarios"></a>Elastisk frågan scenarier

Målet är att underlätta frågar scenarier där rader bidrar till ett övergripande resultat i flera databaser. Frågan kan antingen bestå av användaren eller programmet direkt eller indirekt via verktyg som är anslutna till databasen. Detta är särskilt användbar när du skapar rapporter med hjälp av kommersiella verktyg för integrering av BI eller data, eller alla program som inte kan ändras. Du kan fråga över flera databaser med hjälp av välbekanta SQL Server connectivity upplevelsen i verktyg som Excel, PowerBI, Tableau eller Cognos med en elastisk fråga.
En elastisk fråga ger enkel åtkomst till en hel samling databaser via frågor som utfärdats av SQL Server Management Studio eller Visual Studio och underlättar flera databaser frågar från Entity Framework eller andra ORM-miljöer. Bild 1 visar ett scenario där en befintlig molnet program (som använder den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md)) nivån bygger på en uppskalad data och en elastisk fråga används för rapportering av flera databaser.

**Bild 1** elastisk fråga som används på utskalat datanivå

![Elastisk fråga som används på utskalat datanivå][1]

Kundscenarier för elastiska frågan kännetecknas av följande topologier:

* **Vertikal partitionering - mellan databasfrågor** (topologi 1): data är partitionerad lodrätt mellan ett antal databaser i en datanivå. Vanligtvis finns olika uppsättningar med tabeller på olika databaser. Det innebär att schemat är olika för olika databaser. Till exempel finns alla tabeller för lager på en databas när alla redovisning-relaterade tabeller är på en andra databas. Vanliga användningsområden med den här topologin kräver en fråga på eller kompilera rapporter över tabeller i flera databaser.
* **Horisontell partitionering - horisontell partitionering** (topologi 2): Data är partitionerad vågrätt om du vill distribuera rader i en skaländras ut data nivå. Schemat är identiska för alla deltagande databaser med den här metoden. Den här metoden kallas även ”delning”. Horisontell partitionering kan utföras och hanterade med (1) den elastiska databasen verktyg bibliotek eller (2) self-delning. En elastisk fråga används för att fråga eller kompilera rapporter över många delar.

> [!NOTE]
> Elastisk frågan fungerar bäst för tillfällig reporting scenarier där de flesta av bearbetning kan utföras på datanivå. För tunga reporting arbetsbelastningar eller informationslager-scenarier med mer komplexa frågor kan också överväga att använda [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikal partitionering - frågor med flera databaser

För att börja koda, se [komma igång med flera databaser fråga (vertikal partitionering)](sql-database-elastic-query-getting-started-vertical.md).

En elastisk fråga kan användas för att göra data som finns i en SQL-databas som är tillgängliga för andra SQL-databaser. På så sätt kan frågor från en databas att referera till tabeller i alla andra fjärranslutna SQL-databasen. Det första steget är att definiera en extern datakälla för varje fjärrdatabasen. Den externa datakällan har definierats i den lokala databasen som du vill använda tabeller som finns på fjärrdatabasen. Det krävs inga ändringar på fjärrdatabasen. Vanliga vertikal partitionering scenarier där olika databaser har olika scheman, kan elastisk frågor användas för att implementera vanliga användningsområden för till exempel åtkomst till referensdata och frågor till flera databaser.

> [!IMPORTANT]
> Du måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten har behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.
>

**Referensdata**: topologin används för hantering av referens. I figuren nedan visas hålls två tabeller (T1 och T2) med referensdata på en dedikerad databas. Med en elastisk fråga, du kan nu komma åt tabeller T1 och T2 via fjärranslutning från andra databaser som visas i bilden. Använd topologi 1 om referenstabellerna är liten eller remote frågor i referenstabellen har selektiv predikat.

**Bild 2** vertikal partitionering – med elastisk att fråga referensdata

![Vertikal partitionering – med elastisk att fråga referensdata][3]

**Flera databaser frågar**: elastiska frågar aktivera användningsområden som kräver fråga över flera SQL-databaser. Bild 3 visar fyra olika databaser: CRM, inventering, HR och produkter. Frågor som utförs i en av databaserna måste också tillgång till en eller alla andra databaser. Med en elastisk fråga kan konfigurera du databasen för det här fallet genom att köra några enkla DDL-instruktionerna på var och en av de fyra databaserna. Efter den här engångskonfiguration är åtkomst till en fjärrtabell så enkelt som refererar till en lokal tabell från T-SQL-frågor eller BI-verktyg. Den här metoden rekommenderas om remote frågorna inte returnerar stora resultat.

**Bild 3** vertikal partitionering – med hjälp av elastiska att fråga mellan olika databaser

![Vertikal partitionering – med hjälp av elastiska att fråga mellan olika databaser][4]

Elastisk databasfrågor för vertikal partitionering scenarier som kräver åtkomst till en tabell som finns på fjärr-SQL-databaser med samma schema konfigurerar du följande steg:

* [Skapa HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Skapa och släpp extern DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource av typen **RDBMS**
* [Skapa och släpp extern tabell](https://msdn.microsoft.com/library/dn935021.aspx) mytable prefix

Du kan komma åt fjärrtabell ”mytable” som prefix som om det vore en lokal tabell när du har kört DDL-instruktioner. Azure SQL Database automatiskt öppnar en anslutning till fjärrdatabasen, bearbetar din förfrågan på fjärrdatabasen och returnerar resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horisontell partitionering - delning
Använda elastisk fråga för att utföra rapportuppgifter över ett delat vågrätt, d.v.s. partitionerad datanivå kräver en [elastisk databas Fragmentera kartan](sql-database-elastic-scale-shard-map-management.md) att representera databaserna på datanivå. Normalt bara en enda Fragmentera karta används i det här scenariot och en särskild databas med elastisk frågefunktioner (huvudnod) fungerar som startpunkt för rapportering frågor. Endast den här databasen behöver åtkomst till Fragmentera kartan. Bild 4 illustrerar den här topologin och dess konfiguration med elastisk fråga databasen och Fragmentera kartan. Databaserna på datanivå kan vara av en Azure SQL Database version eller utgåva. Läs mer om klientbibliotek för elastisk databas och skapa Fragmentera maps [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md).

**Bild 4** vågräta partitionering – med elastisk fråga för rapportering över shardade data kärnnivåerna

![Vågrät partitionering – med elastisk fråga för rapportering över shardade data kärnnivåerna][5]

> [!NOTE]
> Elastisk fråga databas (huvudnod) kan vara separat databas eller det kan vara samma databas som är värd för Fragmentera kartan. Den konfiguration som du väljer, kontrollera att tjänsten och prestanda nivån databasen är tillräckligt högt för att hantera den förväntade mängden inloggning/frågebegäranden.

Följande steg kan du konfigurera elastisk databasfrågor för horisontell partitionering scenarier som kräver åtkomst till en tabell som finns på (vanligtvis) flera fjärranslutna SQL-databaser:

* [Skapa HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Skapa en [Fragmentera kartan](sql-database-elastic-scale-shard-map-management.md) som representerar din datanivån med klientbibliotek för elastisk databas.   
* [Skapa och släpp extern DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource av typen **SHARD_MAP_MANAGER**
* [Skapa och släpp extern tabell](https://msdn.microsoft.com/library/dn935021.aspx) mytable prefix

När du har utfört dessa steg kan du komma åt vågrätt partitionerad tabell ”mytable” som prefix som om det vore en lokal tabell. Azure SQL Database automatiskt öppnar flera parallella anslutningar till fjärr-databaser där tabellerna lagras fysiskt, behandlar begäranden på fjärr-databaser och returnerar resultaten.
Mer information om de steg som krävs för horisontell partitionering scenario kan hittas i [elastisk frågan för horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md).

För att börja koda, se [komma igång med elastisk frågan för horisontell partitionering (delning)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-fråga
När du har definierat din externa datakällor och externa tabeller kan använda du reguljära anslutningssträngar för SQL Server för att ansluta till databaser där du har definierat externa tabeller. Du kan sedan köra T-SQL-uttryck via externa tabeller för anslutningen med de begränsningar som beskrivs nedan. Du hittar mer information och exempel för T-SQL-frågor i avsnitt dokumentation för [horisontell partitionering](sql-database-elastic-query-horizontal-partitioning.md) och [vertikal partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Anslutning för verktyg
Du kan använda vanliga SQL Server-anslutningssträngar för att ansluta dina program och verktyg för BI eller data integration till databaser som har externa tabeller. Kontrollera att SQL Server stöds som en datakälla för verktyget du behöver. När du är ansluten, referera till elastisk fråga databas och externa tabeller i databasen som du skulle göra med andra SQL Server-databas som du ansluter till med din-verktyget.

> [!IMPORTANT]
> Autentisering med hjälp av Azure Active Directory med elastisk frågor stöds inte för närvarande.
> 
> 

## <a name="cost"></a>Kostnad
Elastisk frågan ingår i kostnaden för Azure SQL Database-databaser. Observera att där remote databaserna är i ett annat Datacenter elastisk frågan slutpunkt-topologier stöds, men datatrafik från fjärranslutna databaser debiteras regular [Azure priser](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Begränsningar i förhandsversionen
* Kör ditt första elastisk frågan kan ta upp till några minuter på nivå Standard prestanda. Nu är nödvändiga för att läsa in de elastiska frågefunktioner; läser in prestanda förbättras med högre prestandanivåer.
* Skript för externa datakällor eller externa tabeller från SSMS eller SSDT stöds inte ännu.
* Import/Export för SQL DB stöder ännu inte externa datakällor och externa tabeller. Om du behöver använda Import/Export släpp objekten innan du exporterar och sedan återskapa dem när du har importerat.
* Elastisk fråga stöder för närvarande endast skrivskyddad åtkomst till externa tabeller. Du kan dock använda alla T-SQL-funktioner på databasen där den externa tabellen har definierats. Detta kan vara användbart till, t.ex. spara tillfälliga resultat använder, t.ex., väljer < column_list > till < local_table > eller för att definiera lagrade procedurer på elastiska fråga-databas som refererar till externa tabeller.
* Förutom nvarchar(max) stöds inte LOB-typerna i extern tabelldefinitioner. Som en tillfällig lösning kan du skapa en vy på fjärrdatabasen som kastar LOB-typ i nvarchar(max), definiera din extern tabell över vyn i stället för bastabellen och konvertera den tillbaka till den ursprungliga LOB-typen i dina frågor.
* Kolumnstatistik via externa tabeller stöds inte för närvarande. Tabeller statistik stöds men måste skapas manuellt.

## <a name="feedback"></a>Feedback
Kontrollera dela feedback om din upplevelse med elastisk frågor med oss på Livefyre nedan MSDN-forum eller Stackoverflow. Vi är intresserade av alla typer av feedback om tjänsten (fel, grov kanter, funktionen luckor).

## <a name="next-steps"></a>Nästa steg

* Vertikal partitionering, finns [komma igång med flera databaser fråga (vertikal partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade finns [frågar lodrätt partitionerad data)](sql-database-elastic-query-vertical-partitioning.md)
* Horisontell partitionering (delning), finns [komma igång med elastisk frågan för horisontell partitionering (delning)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade finns [frågar vågrätt partitionerad data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktion på en enda remote Azure SQL Database eller en uppsättning databaser som fungerar som delar i en vågrät partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

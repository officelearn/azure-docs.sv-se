---
title: Rapport över utskalade molndatabaser (horisontella partitioner) | Microsoft Docs
description: Använd mellan databaser databasfrågor rapporten över flera databaser.
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
ms.date: 12/18/2018
ms.openlocfilehash: a73938c98ebaea310875f0db8b665d0f1aed55e8
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562002"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapportera över skalbara molndatabaser (förhandsversion)

Du kan skapa rapporter från flera Azure SQL-databaser från en enda anslutning återställningspunkt med ett [elastisk fråga](sql-database-elastic-query-overview.md). Databaserna måste partitioneras vågrätt (även känt som ”delade”).

Om du har en befintlig databas, se [migrera befintliga databaser till utskalade databaser](sql-database-elastic-convert-to-use-elastic-tools.md).

Information om SQL-objekt som behövs för att fråga finns i [fråga över en vågrätt partitionerad databaser](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Förutsättningar

Ladda ned och kör den [komma igång med Elastic Database-verktyg exempel](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en shard kartan manager med hjälp av exempelappen
Här skapar du en skärvkarta manager tillsammans med flera shards, följt av inmatningen av data i shards. Om du redan har shards installation med shardade data i dem, kan du hoppa över följande steg och flytta till nästa avsnitt.

1. Skapa och köra den **komma igång med elastiska Databasverktyg** exempelprogrammet. Följ stegen tills steg 7 i avsnittet [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). I slutet av steg 7 visas följande kommandotolk:

    ![kommandotolk][1]
2. Skriv ”1” i kommandofönstret och tryck på **RETUR**. Detta skapar fragmentet kartan manager och lägger till två fragment till servern. Skriv ”3” och tryck på sedan **RETUR**; och upprepa åtgärden fyra gånger. Detta infogar rader med exempel data i din fragment.
3. Den [Azure-portalen](https://portal.azure.com) ska visa tre nya databaser på din server:

   ![Visual Studio-bekräftelse][2]

   Frågor mellan databaser finns nu tillgänglig via Elastic Database-klientbibliotek. Till exempel använda alternativ 4 i kommandofönstret. Multi-shard-frågan är alltid en **UNION ALL** av resultaten från alla shards.

   I nästa avsnitt skapar vi en database-slutpunkten som har stöd för mer omfattande frågekörning av data över shards.

## <a name="create-an-elastic-query-database"></a>Skapa en elastisk fråga databas
1. Öppna den [Azure-portalen](https://portal.azure.com) och logga in.
2. Skapa en ny Azure SQL-databas på samma server som din shard-konfiguration. Namnge databasen ”ElasticDBQuery”.

    ![Azure-portalen och prisnivå][3]

    > [!NOTE]
    > Du kan använda en befintlig databas. Om du kan göra så det inte får vara en av shards som du vill köra dina frågor på. Den här databasen används för att skapa metadataobjekt för en elastisk databasfråga.
    >

## <a name="create-database-objects"></a>Skapa databasobjekt
### <a name="database-scoped-master-key-and-credentials"></a>Databasbegränsade huvudnyckel och autentiseringsuppgifter
De används för att ansluta till fragmentkartehanteraren och shards:

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Ansluta till ElasticDBQuery databas och kör följande T-SQL-kommandon:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    ”användarnamn” och ”password” bör vara samma som används i steg 6 i inloggningsinformation [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) i [komma igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Externa datakällor
Om du vill skapa en extern datakälla, kör du följande kommando på ElasticDBQuery databasen:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 ”CustomerIDShardMap” är namnet på fragmentkartan, om du har skapat fragmentkartan och karthanteraren använder Verktyg för elastisk databas-exemplet. Men om du har använt din anpassade konfiguration för det här exemplet, bör sedan den vara fragment kartans namn du valde i ditt program.

### <a name="external-tables"></a>Externa tabeller
Skapa en extern tabell som matchar tabellen kunder på shards genom att köra följande kommando på ElasticDBQuery databasen:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Kör en exempelfråga elastisk databas T-SQL
När du har definierat den externa datakällan och dina externa tabeller som du kan nu använda fullständig T-SQL över dina externa tabeller.

Kör frågan på ElasticDBQuery databasen:

    select count(CustomerId) from [dbo].[Customers]

Du kommer att märka att frågan aggregerar resultaten från alla shards och ger följande utdata:

![Utdatainformation][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importera elastisk databas frågeresultaten till Excel
 Du kan importera resultatet av en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till den **Data** menyfliksområdet.
3. Klicka på **från andra källor** och klicka på **från SQL Server**.

   ![Excel-import från andra källor][5]
4. I den **Dataanslutningsguiden** skriver du servernamnet och inloggningsuppgifter för servern. Klicka sedan på **Nästa**.
5. I dialogrutan **Markera databasen som innehåller de data du vill**väljer den **ElasticDBQuery** databas.
6. Välj den **kunder** tabellen i listvyn och klicka på **nästa**. Klicka sedan på **Slutför**.
7. I den **importdata** formuläret under **Välj hur du vill visa data i din arbetsbok**väljer **tabell** och klicka på **OK**.

Alla rader från **kunder** tabell, lagras i olika shards fylla i Excel-blad.

Du kan nu använda Excels kraftfulla visualiseringen. Du kan använda anslutningssträngen med servernamnet, databasnamnet och autentiseringsuppgifter för att ansluta din integreringsverktyg BI och data till elastisk fråga i databasen. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Du kan referera till elastisk fråga i databasen och externa tabeller precis som andra SQL Server-databas och SQL Server-tabeller som du vill ansluta till med verktyg.

### <a name="cost"></a>Kostnad
Det finns ingen extra kostnad för att använda funktionen Elastic Database-fråga.

Information om priser finns i [prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk fråga översikt](sql-database-elastic-query-overview.md).
* Se en vertikal partitionering självstudie [komma igång med databasöverskridande frågor (vertikala partitioner)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* Syntax och exempel frågor för vågrätt partitionerade data, se [fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktionen på en enskild remote Azure SQL-databas eller en uppsättning databaser som fungerar som shards i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

---
title: "Rapport över utskalat moln databaser (horisontell partitionering) | Microsoft Docs"
description: "Använd mellan databasen databasfrågor rapporten över flera databaser."
services: sql-database
manager: jhubbard
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/23/2016
ms.author: mlandzic
ms.openlocfilehash: 29f1291f5c5d6023ca7bcd47fc2c0d9b69fd39fa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapporter över databaser som skalats ut molnet (förhandsgranskning)
Du kan skapa rapporter från flera Azure SQL-databaser från en enda anslutning med en [elastisk frågan](sql-database-elastic-query-overview.md). Databaserna partitioneras vågrätt (även kallat ”delat”).

Om du har en befintlig databas, se [migrera befintliga databaser som skalats ut databaser](sql-database-elastic-convert-to-use-elastic-tools.md).

SQL-objekt som behövs för att fråga finns [fråga över vågrätt partitionerade databaser](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Förutsättningar
Hämta och kör den [komma igång med elastisk databas verktyg exempel](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en Fragmentera kartan manager med sample-appen
Här skapar du en karta Fragmentera manager tillsammans med flera delar, följt av infogning av data till shards. Om du redan har shards installation med shardade data i dem kan du hoppa över följande steg och flytta till nästa avsnitt.

1. Skapa och köra den **komma igång med elastiska Databasverktyg** exempelprogrammet. Följ stegen tills steg 7 i avsnittet [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). I slutet av steg 7 visas följande kommandotolk:

    ![kommandotolk][1]
2. I Kommandotolken, Skriv ”1” och tryck på **RETUR**. Detta skapar Fragmentera kartan manager och lägger till två delar i server. Sedan skriver ”3” och tryck på **RETUR**; Upprepa åtgärden fyra gånger. Detta infogar exempel datarader i din shards.
3. Den [Azure-portalen](https://portal.azure.com) ska visa tre nya databaser på servern:

   ![Visual Studio-bekräftelse][2]

   Nu stöds mellan databasfrågor via klientbibliotek för elastisk databas. Till exempel använda alternativ 4 i kommandofönstret. Resultaten av en fråga med flera Fragmentera är alltid en **UNION ALL** av resultat från alla delar.

   I nästa avsnitt skapa vi en exempel-databasen slutpunkt som har stöd för bättre fråga data över shards.

## <a name="create-an-elastic-query-database"></a>Skapa en fråga för elastisk databas
1. Öppna den [Azure-portalen](https://portal.azure.com) och logga in.
2. Skapa en ny Azure SQL-databas på samma server som Fragmentera-installationsprogrammet. Namn på databasen ”ElasticDBQuery”.

    ![Azure-portalen och prisnivå][3]

    > [!NOTE]
    > Du kan använda en befintlig databas. Om du gör det, får den inte vara en delar som du vill köra frågor på. Den här databasen används för att skapa metadataobjekt för en elastisk databas-fråga.
    >

## <a name="create-database-objects"></a>Skapa databasobjekt
### <a name="database-scoped-master-key-and-credentials"></a>Databas-omfattande huvudnyckel och autentiseringsuppgifter
De används för att ansluta till Fragmentera kartan manager och delar:

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till ElasticDBQuery databas och kör följande T-SQL-kommandon:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    ”användarnamn” och ”password” ska vara samma som de inloggningsuppgifter som används i steg 6 i [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) i [komma igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Externa datakällor
Om du vill skapa en extern datakälla, kör du följande kommando på databasen för ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 ”CustomerIDShardMap” är namnet på kartan Fragmentera om du har skapat Fragmentera kartan och Fragmentera kartan manager med elastisk databas verktyg exemplet. Om du har använt din anpassade konfiguration för det här exemplet bör sedan det dock Fragmentera mappningsnamn som du valde i ditt program.

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

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra en exempelfråga för elastisk databas T-SQL
När du har definierat den externa datakällan och externa tabeller kan du nu använda fullständig T-SQL via externa tabeller.

Kör frågan på ElasticDBQuery databasen:

    select count(CustomerId) from [dbo].[Customers]

Du kommer att märka att frågan aggregerar resultat från alla delar och ger följande utdata:

![Utdatainformation][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importera elastisk databas frågeresultaten till Excel
 Du kan importera resultatet av en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till den **Data** menyfliksområdet.
3. Klicka på **från andra källor** och på **från SQL Server**.

   ![Excel-import från andra källor][5]
4. I den **Dataanslutningsguiden** skriver server servernamn och inloggningsuppgifter. Klicka sedan på **Nästa**.
5. I dialogrutan **Markera databasen som innehåller de data som du vill**, Välj den **ElasticDBQuery** databas.
6. Välj den **kunder** tabell i listan och klickar på **nästa**. Klicka på **Slutför**.
7. I den **importera Data** formuläret under **Välj hur du vill visa data i arbetsboken**väljer **tabell** och på **OK**.

Alla rader från **kunder** tabell som sparas i olika delar fylla i Excel-blad.

Du kan nu använda Excel-kraftfulla visualiseringen. Du kan använda anslutningssträngen med servernamnet, databasnamnet och autentiseringsuppgifter för att ansluta din verktyg för BI och integrering till elastisk fråga databas. Kontrollera att SQL Server stöds som en datakälla för verktyget du behöver. Du kan referera till elastisk fråga databas och externa tabeller precis som andra SQL Server-databas och SQL Server-tabeller som du vill ansluta till med din-verktyget.

### <a name="cost"></a>Kostnad
Det finns utan extra kostnad för att använda funktionen elastisk databasfråga.

Mer information om priser finns [prisinformation för SQL-databasen](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk frågan översikt](sql-database-elastic-query-overview.md).
* Vertikal partitionering, finns [komma igång med flera databaser fråga (vertikal partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade finns [frågar lodrätt partitionerad data)](sql-database-elastic-query-vertical-partitioning.md)
* Syntax och exempel frågor för vågrätt partitionerade finns [frågar vågrätt partitionerad data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktion på en enda remote Azure SQL Database eller en uppsättning databaser som fungerar som delar i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

---
title: Rapportera över utskalade molndatabaser
description: Använd databasfrågor mellan databaser för att rapportera över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823780"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapportera över utskalade molndatabaser (förhandsgranskning)

Du kan skapa rapporter från flera Azure SQL-databaser från en enda anslutningspunkt med hjälp av en [elastisk fråga](sql-database-elastic-query-overview.md). Databaserna måste vara vågrätt partitionerade (kallas även "fragmenterade").

Om du har en befintlig databas läser du [Migrera befintliga databaser till utskalade databaser](sql-database-elastic-convert-to-use-elastic-tools.md).

Information om vilka SQL-objekt som behövs för att fråga finns i [Fråga över vågrätt partitionerade databaser](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Krav

Hämta och kör [exemplet Komma igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en fragmentkarthanterare med exempelappen
Här skapar du en fragmentkarthanterare tillsammans med flera shards, följt av införande av data i shards. Om du råkar redan ha shards setup med fragmenterade data i dem, kan du hoppa över följande steg och gå vidare till nästa avsnitt.

1. Skapa och kör exempelprogrammet **Komma igång med elastiska databasverktyg** genom att följa stegen i artikelavsnittet [Hämta och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1). När du är klar med alla steg visas följande kommandotolk:

    ![Kommandotolken][1]
2. Skriv "1" i kommandofönstret och tryck på **Retur**. Detta skapar fragmentkarthanteraren och lägger till två shards på servern. Skriv sedan "3" och tryck på **Retur**; upprepa åtgärden fyra gånger. Detta infogar exempeldatarader i dina shards.
3. [Azure-portalen](https://portal.azure.com) ska visa tre nya databaser på servern:

   ![Bekräftelse från Visual Studio][2]

   Nu stöds frågor över flera databaser via klientbiblioteken för elastisk databas. Använd till exempel alternativ 4 i kommandofönstret. Resultaten från en multi-shard fråga är alltid en **UNION alla** resultat från alla shards.

   I nästa avsnitt skapar vi en exempeldatabasslutpunkt som stöder rikare frågor av data över shards.

## <a name="create-an-elastic-query-database"></a>Skapa en elastisk frågedatabas
1. Öppna [Azure-portalen](https://portal.azure.com) och logga in.
2. Skapa en ny Azure SQL-databas på samma server som fragmentkonfigurationen. Namnge databasen "ElasticDBQuery".

    ![Azure-portal och prisnivå][3]

    > [!NOTE]
    > du kan använda en befintlig databas. Om du kan göra det får det inte vara en av de shards som du vill köra dina frågor på. Den här databasen används för att skapa metadataobjekt för en elastisk databasfråga.
    >

## <a name="create-database-objects"></a>Skapa databasobjekt
### <a name="database-scoped-master-key-and-credentials"></a>Huvudnyckel och autentiseringsuppgifter för databasomfattade
Dessa används för att ansluta till fragmentkarthanteraren och shards:

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till ElasticDBQuery-databasen och kör följande T-SQL-kommandon:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "användarnamn" och "lösenord" bör vara samma som inloggningsinformation som används i steg 3 i avsnitt [Ladda ner och köra exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) i artikeln Komma igång med **elastiska databasverktyg.**

### <a name="external-data-sources"></a>Externa datakällor
Om du vill skapa en extern datakälla kör du följande kommando i ElasticDBQuery-databasen:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" är namnet på fragmentkartan, om du skapade fragmentkartan och fragmentkarthanteraren med hjälp av exemplet med elastiska databasverktyg. Men om du använde din anpassade inställning för det här exemplet, bör det vara fragmentmappningsnamnet som du valde i ditt program.

### <a name="external-tables"></a>Externa tabeller
Skapa en extern tabell som matchar tabellen Kunder i shards genom att köra följande kommando i ElasticDBQuery-databasen:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra en exempel elastisk databas T-SQL-fråga
När du har definierat din externa datakälla och dina externa tabeller kan du nu använda fullständig T-SQL över dina externa tabeller.

Kör den här frågan i ElasticDBQuery-databasen:

    select count(CustomerId) from [dbo].[Customers]

Du kommer att märka att frågan sammanställer resultat från alla shards och ger följande utdata:

![Information om utdata][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importera elastiska databasfrågeresultat till Excel
 Du kan importera resultaten från en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till **menyfliksområdet Data.**
3. Klicka **på Från andra källor** och klicka på Från SQL **Server**.

   ![Excel-import från andra källor][5]
4. I **guiden Dataanslutning** skriver du servernamnet och inloggningsuppgifterna. Klicka sedan på **Nästa**.
5. Markera databasen **som innehåller de data du vill använda**i dialogrutan Välj databasen **ElasticDBQuery.**
6. Markera tabellen **Kunder** i listvyn och klicka på **Nästa**. Klicka sedan på **Slutför**.
7. Välj **Tabell** under **Välj hur du vill visa dessa data i arbetsboken i**formuläret **Importera data** och klicka på **OK**.

Alla rader från tabellen **Kunder,** som lagras i olika shards, fyller i Excel-bladet.

Du kan nu använda Excels kraftfulla datavisualiseringsfunktioner. Du kan använda anslutningssträngen med servernamn, databasnamn och autentiseringsuppgifter för att ansluta bi- och dataintegrationsverktygen till den elastiska frågedatabasen. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Du kan referera till den elastiska frågedatabasen och externa tabeller precis som alla andra SQL Server-databas- och SQL Server-tabeller som du ansluter till med verktyget.

### <a name="cost"></a>Kostnad
Det finns ingen extra kostnad för att använda funktionen Elastisk databasfråga.

Prisinformation finns i [information om SQL-databasprissättning](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md).
* En lodrät partitioneringsdjälva finns i [Komma igång med fråga över flera databaser (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax- och exempelfrågor för lodrätt partitionerade data finns i [Fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* Syntax- och exempelfrågor för vågrätt partitionerade data finns i [Fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [\_sp \_kör fjärrkontroll](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-uttryck på en enda fjärr Azure SQL-databas eller uppsättning databaser som fungerar som shards i ett vågrätt partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

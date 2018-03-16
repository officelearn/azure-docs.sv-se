---
title: Migrera befintliga databaser att skala ut | Microsoft Docs
description: "Konvertera delat databaser för att använda elastisk Databasverktyg genom att skapa en Fragmentera kartan manager"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: d82994f3ab925fa3ace0d0dbe1631a01dd1df586
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrera befintliga databaser ska skalas ut
Enkelt hantera dina befintliga delat utskalat-databaser som använder Azure SQL Database databaser (som den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md)). Först konvertera en befintlig uppsättning databaser som ska användas i [Fragmentera kartan manager](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Översikt
För att migrera en befintlig delat databas. 

1. Förbereda den [Fragmentera kartan manager-databasen](sql-database-elastic-scale-shard-map-management.md).
2. Skapa Fragmentera kartan.
3. Förbered enskilda delar.  
4. Lägga till avbildningar Fragmentera kartan.

Dessa tekniker kan implementeras med hjälp av antingen den [klientbiblioteket för .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), eller PowerShell-skript som finns på [Azure SQL DB - skript för elastisk databas verktyg](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). De här exemplen använder PowerShell-skript.

Läs mer om ShardMapManager [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md). En översikt över elastisk Databasverktyg finns [elastisk databas funktionsöversikt](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Förbereda Fragmentera kartan manager-databasen
Fragmentera kartan manager är en särskild databas som innehåller data för att hantera databaser som skalats ut. Du kan använda en befintlig databas eller skapa en ny databas. En databas som fungerar som Fragmentera kartan manager får inte vara samma databas som en Fragmentera. PowerShell-skriptet skapar inte databasen för dig. 

## <a name="step-1-create-a-shard-map-manager"></a>Steg 1: skapa en Fragmentera kartan manager
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Att hämta Fragmentera kartan manager
När den har skapats, kan du hämta hanteraren Fragmentera karta med denna cmdlet. Det här steget krävs varje gång du behöver använda ShardMapManager-objektet.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Steg 2: skapa Fragmentera kartan
Välj typ av Fragmentera kartan för att skapa. Valet är beroende av databasens arkitektur: 

1. En organisation per databas (villkor, finns det [ordlista](sql-database-elastic-scale-glossary.md).) 
2. Flera klienter per databas (två typer):
   1. Lista över-mappning
   2. Mappning av intervallet

En enskild klient-modell, skapa en **lista mappning** Fragmentera kartan. Stöd för en innehavare modellen tilldelar en databas per klient. Detta är en effektiv modell för SaaS-utvecklare som det förenklar hanteringen.

![Lista över-mappning][1]

Modell för flera klienter tilldelas en enskild databas flera innehavare (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig varje innehavare har liten databehov. I den här modellen, tilldela ett antal klienter till en databas med hjälp av **intervallet mappning**. 

![Mappning av intervallet][2]

Eller du kan implementera en databas för flera innehavare modellen med hjälp av en *lista mappning* tilldela flera klienter till en enskild databas. Till exempel DB1 används för att lagra information om klient-ID 1 och 5 och DB2 lagrar data för klienten 7 och 10-klient. 

![Flera klienter på enskild DB][3] 

**Baserat på ditt val, välja ett av följande alternativ:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Alternativ 1: skapa en Fragmentera karta för en lista över-mappning
Skapa en Fragmentera karta med ShardMapManager-objektet. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Alternativ 2: skapa en Fragmentera karta för mappning av intervallet
Om du vill använda det här mönstret för mappning av klient-ID-värden måste vara sammanhängande intervall och det är acceptabelt att ha lucka i intervall genom att hoppa över intervallet när du skapar databaserna.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Alternativ 3: Visa en lista över mappningar på en enskild databas
Ställa in det här mönstret kräver också att skapa en karta i listan som visas i steg 2, alternativ 1.

## <a name="step-3-prepare-individual-shards"></a>Steg 3: Förbered enskilda delar
Lägg till varje Fragmentera (databasen) hanteraren Fragmentera kartan. Detta förbereder enskilda databaser för att lagra mappningsinformation. Köra den här metoden på varje Fragmentera.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Steg 4: Lägga till mappningar
För att lägga till mappningar beror på vilken typ av Fragmentera karta som du skapade. Om du har skapat en karta i listan kan du lägga till listan mappningar. Om du har skapat en karta intervallet du lägga till mappningar för intervallet.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Alternativ 1: mappa data för en lista över-mappning
Mappa dina data genom att lägga till en lista över-mappning för varje klient.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Alternativ 2: mappa data för mappning av intervallet
Lägg till intervallet mappningar för alla klient-ID intervallet - kopplingarna till databasen:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Steg 4 alternativ 3: mappa data för flera klienter på en enskild databas
Kör Add-ListMapping (alternativ 1) för varje klient. 

## <a name="checking-the-mappings"></a>Kontrollera mappningar
Information om befintliga delar och mappningar som är associerade med dem kan efterfrågas med följande kommandon:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Sammanfattning
När du har slutfört installationen, kan du börja använda klientbibliotek för elastisk databas. Du kan också använda [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) och [flera Fragmentera frågan](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Nästa steg
Hämta PowerShell-skript från [skript för verktyg för Azure SQL DB-elastiska Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Verktygen finns också på GitHub: [Azure/elastisk-db-tools](https://github.com/Azure/elastic-db-tools).

Använda verktyget delade dokument för att flytta data till eller från en modell för flera klienter till en enskild klient-modell. Se [dela merge tool](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ytterligare resurser
Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Frågor och Funktionsbegäranden
Frågor, Använd den [SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsbegäranden, lägga till dem i den [SQL-databas Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png


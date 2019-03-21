---
title: Migrera befintliga databaser för att skala ut | Microsoft Docs
description: Konvertera shardade databaser för att använda verktygen för elastiska databaser genom att skapa en shard kartan manager
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 49686e407b2d733c04bad31706c6c4f315bf28bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008677"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrera befintliga databaser för att skala ut
Enkelt hantera dina befintliga shardade utskalade-databaser som använder Azure SQL Database databaser (till exempel den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md)). Först konvertera en befintlig uppsättning databaser som ska användas i [karthanteraren](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Översikt
Att migrera en befintlig fragmenterade (sharded) databas: 

1. Förbereda den [databas](sql-database-elastic-scale-shard-map-management.md).
2. Skapa fragmentkartan.
3. Förbered enskilda shards.  
4. Lägga till avbildningar fragmentkartan.

Dessa metoder kan implementeras med hjälp av antingen den [klientbiblioteket för .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), eller PowerShell-skript som finns på [Azure SQL DB - skript för Elastic Database-verktyg](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). I exemplen här används PowerShell-skript.

Läs mer om ShardMapManager [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md). En översikt över elastic database-verktyg finns i [översikt över Elastic Database-funktioner](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Förbereda databasen
Fragmentkartehanteraren är en särskild databas som innehåller data för att hantera utskalade databaser. Du kan använda en befintlig databas eller skapa en ny databas. En databas som fungerar som karthanteraren får inte vara samma databas som en shard. PowerShell-skript skapar inte databasen åt dig. 

## <a name="step-1-create-a-shard-map-manager"></a>Steg 1: skapa ett fragment kartan manager
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Att hämta fragmentkartehanteraren
När du har skapat, kan du hämta fragmentkartehanteraren med denna cmdlet. Det här steget krävs varje gång du behöver använda ShardMapManager-objektet.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Steg 2: skapa fragmentkartan
Välj typ av fragmentkartan för att skapa. Valet beror på databasens arkitektur: 

1. För enskilda innehavare per databas (villkor, finns det [ordlista](sql-database-elastic-scale-glossary.md).) 
2. Flera klienter per databas (två typer):
   1. Lista mappning
   2. Mappning av intervall

För en enda klient-modell, skapar du en **lista mappning** fragmentkartan. Enskild klientmodell tilldelar en databas per klient. Det här är en effektiv modell för SaaS-utvecklare som den förenklar hanteringen.

![Lista mappning][1]

Modell för flera klienter tilldelas en individuell databas flera klienter (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig varje klient har liten databehov. I den här modellen kan du tilldela olika klienter till en databas med **intervallet mappning**. 

![Mappning av intervall][2]

Eller du kan implementera en databas för flera innehavare modellen med hjälp av en *lista mappning* ska tilldelas en individuell databas med flera klienter. Till exempel DB1 används för att lagra information om klient-ID 1 och 5 och DB2 lagrar data för 7-klient- och klienttrafik 10. 

![Flera klienter i en enda DB][3] 

**Baserat på dina val, Välj något av följande alternativ:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Alternativ 1: skapa en skärvkarta för mappning av en lista
Skapa en skärvkarta med ShardMapManager-objektet. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Alternativ 2: skapa en skärvkarta för en mappning av intervall
Om du vill använda det här mönstret för mappning av klient-ID-värden måste vara kontinuerlig intervall och det går att ha lucka i intervall genom att hoppa över intervallet när du skapar databaserna.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>Alternativ 3: Lista över mappningar på en enskild databas
Ställa in det här mönstret kräver också skapa en karta i listan som visas i steg 2, alternativ 1.

## <a name="step-3-prepare-individual-shards"></a>Steg 3: Förbereda enskilda fragment
Lägg till varje shard (databas) i fragmentkartehanteraren. Detta förbereder de enskilda databaserna för att lagra mappningsinformation. Köra den här metoden på varje shard.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Steg 4: Lägga till mappningar
Att lägga till mappningar beror på vilken typ av fragmentkartan som du skapade. Om du har skapat en karta i listan kan du lägga till listan mappningar. Om du har skapat en karta för intervall du lägger till mappningar för intervallet.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Alternativ 1: mappa data för en lista-mappning
Mappa data genom att lägga till en lista-mappning för varje klient.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Alternativ 2: mappa data för en rad-mappning
Lägg till intervall mappningar för alla klient-ID intervallet - database-associationer:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Steg 4 alternativ 3: mappa data för flera klienter i en enskild databas
Kör Add-ListMapping (alternativ 1) för varje klient. 

## <a name="checking-the-mappings"></a>Kontrollera mappningarna
Information om befintliga shards och mappningar som är associerade med dem kan efterfrågas med följande kommandon:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Sammanfattning
När du har slutfört installationen, kan du börja använda Elastic Database-klientbiblioteket. Du kan också använda [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) och [Multi-shard fråga](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Nästa steg
Hämta PowerShell-skript från [Azure SQL DB-Elastic Database-verktyg skript](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Verktygen finns även på GitHub: [Azure/elastisk-db-tools](https://github.com/Azure/elastic-db-tools).

Verktyget Dela / sammanslå används för att flytta data till eller från en modell för flera innehavare till en enda klient-modell. Se [dela merge verktyget](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ytterligare resurser
Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Frågor och funktionsförfrågningar
Frågor kan du använda den [SQL Database-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsbegäranden, lägga till dem i den [SQL Database-Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png


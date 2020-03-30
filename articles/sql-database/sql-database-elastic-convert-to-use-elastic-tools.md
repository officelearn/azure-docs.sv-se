---
title: Migrera befintliga databaser för att skala ut | Microsoft-dokument
description: Konvertera fragmenterade databaser för att använda elastiska databasverktyg genom att skapa en fragmentkarthanterare
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c776f4ac09626f0abd1eb754cde391a1c5447627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421219"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrera befintliga databaser för att skala ut

Hantera enkelt dina befintliga skalas ut fragmenterade databaser med hjälp av Azure SQL Database-databasverktyg (till exempel [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md)). Konvertera först en befintlig uppsättning databaser för att använda [fragmentkarthanteraren](sql-database-elastic-scale-shard-map-management.md).

## <a name="overview"></a>Översikt

Så här migrerar du en befintlig fragmenterad databas:

1. Förbered [databasen för fragmentkarthanteraren](sql-database-elastic-scale-shard-map-management.md).
2. Skapa fragmentkartan.
3. Förbered de enskilda skärvorna.  
4. Lägg till mappningar på fragmentkartan.

Dessa tekniker kan implementeras med hjälp av [antingen .NET Framework-klientbiblioteket](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)eller PowerShell-skripten som finns på [Azure SQL DB - Elastiska databasverktyg skript](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Exemplen här använder PowerShell-skripten.

Mer information om ShardMapManager finns i [Shard map management](sql-database-elastic-scale-shard-map-management.md). En översikt över de elastiska databasverktygen finns i [Översikt över elastiska databasfunktioner](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Förbereda databasen för fragmentkarthanteraren

Fragmentkarthanteraren är en särskild databas som innehåller data för att hantera utskalade databaser. Du kan använda en befintlig databas eller skapa en ny databas. En databas som fungerar som fragmentkarthanterare bör inte vara samma databas som en shard. PowerShell-skriptet skapar inte databasen åt dig.

## <a name="step-1-create-a-shard-map-manager"></a>Steg 1: skapa en fragmentkartahanterare

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Så här hämtar du karthanteraren för fragment

När du har skapat det kan du hämta fragmentkarthanteraren med den här cmdleten. Det här steget behövs varje gång du behöver använda ShardMapManager-objektet.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Steg 2: skapa fragmentkartan

Välj den typ av fragmentkarta som ska skapas. Valet beror på databasarkitekturen:

1. En klient per databas (För termer, se [ordlistan](sql-database-elastic-scale-glossary.md).)
2. Flera klienter per databas (två typer):
   1. Mappning av lista
   2. Mappning av område

Skapa en fragmentkarta för **en mappning** för en enda klient. Modellen med en klient tilldelar en databas per klient. Detta är en effektiv modell för SaaS-utvecklare eftersom det förenklar hanteringen.

![Mappning av lista][1]

Modellen med flera innehavare tilldelar flera klienter till en enskild databas (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig att varje klient ska ha små databehov. I den här modellen tilldelar du ett antal klienter till en databas med hjälp av **områdesmappning**.

![Mappning av område][2]

Du kan också implementera en databasmodell med flera innehavare med hjälp av en *listmappning* för att tilldela flera klienter till en enskild databas. DB1 används till exempel för att lagra information om klient-ID 1 och 5, och DB2 lagrar data för klient 7 och klient 10.

![Flera klienter på en enda DB][3]

**Välj ett av följande alternativ utifrån ditt val:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Alternativ 1: skapa en fragmentkarta för en listmappning

Skapa en fragmentkarta med ShardMapManager-objektet.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Alternativ 2: skapa en fragmentkarta för en områdesmappning

För att använda det här mappningsmönstret måste klient-ID-värden vara kontinuerliga intervall och det är acceptabelt att ha mellanrum i intervallen genom att hoppa över intervallet när databaserna skapas.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Alternativ 3: Lista mappningar i en enskild databas

Ställa in detta mönster kräver också att skapa en lista karta som visas i steg 2, alternativ 1.

## <a name="step-3-prepare-individual-shards"></a>Steg 3: Förbered enskilda skärvor

Lägg till varje fragment (databas) i fragmentkarthanteraren. Detta förbereder de enskilda databaserna för lagring av mappningsinformation. Kör den här metoden på varje shard.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Steg 4: Lägga till mappningar

Tillägget av mappningar beror på vilken typ av fragmentkarta du skapade. Om du har skapat en listkarta lägger du till listmappningar. Om du har skapat en områdeskarta lägger du till områdesmappningar.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Alternativ 1: mappa data för en listmappning

Mappa data genom att lägga till en listmappning för varje klient.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Alternativ 2: mappa data för en områdesmappning

Lägg till intervallmappningar för alla klient-ID-intervall - databasassociationer:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Steg 4 alternativ 3: mappa data för flera klienter i en enskild databas

För varje klient kör du Add-ListMapping (alternativ 1).

## <a name="checking-the-mappings"></a>Kontrollera mappningarna

Information om befintliga shards och mappningar som är associerade med dem kan efterfrågas med hjälp av följande kommandon:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Sammanfattning

När du har slutfört installationen kan du börja använda klientbiblioteket för elastisk databas. Du kan också använda [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) och [fråga med flera fragment](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Nästa steg

Hämta PowerShell-skript från [Azure SQL DB-Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Verktygen finns också på GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Använd verktyget för delad koppling för att flytta data till eller från en modell med flera innehavare till en enda klientmodell. Se [Verktyget Dela koppling](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ytterligare resurser

Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Frågor och funktionsförfrågningar

Om du vill ha frågor kan du använda forumet för [SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsförfrågningar, lägg till dem i [feedbackforumet](https://feedback.azure.com/forums/217321-sql-database/)för SQL Database .

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

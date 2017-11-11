---
title: "Skapa och optimera tabeller för snabb parallella import av data till en SQL Server på en virtuell dator i Azure | Microsoft Docs"
description: "Parallell massimport av data med SQL-tabeller för partition"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 77638ff52edbc2b782b21a4ca1c727a2b46f22f3
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Parallell massimport av data med SQL-tabeller för partition
Det här dokumentet beskrivs hur du skapar partitionerade tabeller för snabb parallella massimport av data till en SQL Server-databas. För stora inläsning/överföring av data till en SQL-databas, importera data till SQL-databas och efterföljande frågor kan förbättras genom att använda *partitionerade tabeller och vyer*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Skapa en ny databas och en uppsättning filgrupper
* [Skapa en ny databas](https://technet.microsoft.com/library/ms176061.aspx), om den inte redan finns.
* Lägga till databasen filgrupper i databasen, som innehåller de partitionerade fysiska filerna. 
* Detta kan göras med [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) om nya eller [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) om databasen finns redan.
* Lägga till en eller flera filer (om det behövs) i varje filgrupp för databasen.
  
  > [!NOTE]
  > Ange mål-filgruppen, där data för den här partitionen och den fysiska databasen filnamn där filgruppsdata lagras.
  > 
  > 

I följande exempel skapas en ny databas med tre filgrupper än primärt och loggrupper, som innehåller en fysisk fil i varje. Databasfilerna skapas i SQL Server-Data standardmapp som konfigurerats i SQL Server-instansen. Läs mer om standardsökvägar [filplatser för standard- och namngivna instanser av SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Skapa en partitionerad tabell
Om du vill skapa partitionerade tabeller enligt dataschemat mappas till databasen filgrupperna skapade i föregående steg, måste du först skapa en partitionsfunktion och schema. När data samtidigt som importeras i en partitionerad tabell fördelas posterna mellan filgrupper enligt ett partitionsschema som beskrivs nedan.

### <a name="1-create-a-partition-function"></a>1. Skapa en partitionsfunktion
[Skapa en partitionsfunktion](https://msdn.microsoft.com/library/ms187802.aspx) funktionen definierar en uppsättning värden/gränser som ska ingå i varje enskild partitionstabell, till exempel, för att begränsa partitioner per månad (vissa\_datetime\_fältet) år 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Skapa ett partitionsschema
[Skapa ett partitionsschema](https://msdn.microsoft.com/library/ms179854.aspx). Det här schemat mappningar varje partition intervallet i partitionsfunktionen fysiska filgruppen, till exempel:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Kontrollera intervall gäller i varje partition enligt funktionen/schemat genom att köra följande fråga:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Skapa en partitionstabell
[Skapa en partitionerad tabell](https://msdn.microsoft.com/library/ms174979.aspx)(s) enligt dataschemat och ange partition schema och begränsning fält som används för att partitionera tabellen, till exempel:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Mer information finns i [skapa partitionerade tabeller och index](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Massimportera data för varje enskild partitionstabellen

* Du kan använda BCP BULK INSERT eller andra metoder som [Migreringsguiden för SQL Server](http://sqlazuremw.codeplex.com/). Följande exempel används metoden BCP.
* [Ändra databasen](https://msdn.microsoft.com/library/bb522682.aspx) ändra schemat för loggning av transaktionen till bulkloggad att minimera arbetet med att logga, till exempel:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Starta import massåtgärder parallellt för att påskynda inläsning av data. Tips om snabbare bulk importerar av stordata i SQL Server-databaser finns [läsa in 1TB på mindre än 1 timme](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Följande PowerShell-skript är ett exempel på parallella datainläsning med hjälp av BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Skapa index för att optimera kopplingar och prestanda för frågor
* Om du extraherar data för modellering från flera tabeller, skapa index för koppling för att förbättra prestanda för kopplingen.
* [Skapa index](https://technet.microsoft.com/library/ms188783.aspx) (klustrade eller icke-grupperade) mål samma filgruppen för varje partition för till exempel:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Eller,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Du kan välja att skapa index innan du importerar data samtidigt. Skapandet av index innan massimport långsammare datainläsning.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Processen för avancerade analyser och teknik i åtgärden exempel
En slutpunkt till slutpunkt genomgången exempel med en offentlig dataset Team av vetenskapliga data finns [Team vetenskap av data i praktiken: använder SQL Server](sql-walkthrough.md).


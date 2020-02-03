---
title: Flytta data till en SQL Server-dator – Team Data Science Process
description: Flytta data från flata filer eller från en lokal SQL Server till SQL Server på Azure VM.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721378"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flytta data till SQL Server på en virtuell Azure-dator

Den här artikeln beskriver alternativ för att flytta data från flata filer (CSV- eller TSV format) eller från en lokal SQL Server till SQL Server på virtuella Azure-datorer. Dessa uppgifter för att flytta data till molnet är en del av Team Data Science Process.

Ett avsnitt som beskriver alternativ för att flytta data till en Azure SQL Database för Machine Learning finns i [Flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

I följande tabell sammanfattas alternativen för att flytta data till SQL Server på virtuella Azure-datorer.

| <b>KÄLLICENSSERVERN</b> | <b>MÅL: SQL Server på virtuell Azure-dator</b> |
| --- | --- |
| <b>Flat fil</b> |1. <a href="#insert-tables-bcp">kommando rads verktyget Mass kopiering (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Mass infogning av SQL-fråga</a><br> 3. <a href="#sql-builtin-utilities">grafiska inbyggda verktyg i SQL Server</a> |
| <b>Lokala SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">distribuera en SQL Server-databas till en Microsoft Azure-guide för virtuella datorer</a><br> 2. <a href="#export-flat-file">Exportera till en platt fil</a><br> 3. <a href="#sql-migration">guiden SQL Database migrering</a> <br> 4. <a href="#sql-backup">säkerhetskopiera och återställa databasen</a><br> |

Det här dokumentet förutsätter att SQL-kommandon körs från SQL Server Management Studio eller Visual Studio Databasutforskare.

> [!TIP]
> Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att skapa och schemalägga en pipeline som kommer att flytta data till en SQL Server VM på Azure. Mer information finns i [Kopiera data med Azure Data Factory (kopierings aktivitet)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Förhandskrav
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure Storage-konto**. För att lagra data i den här självstudien använder du ett Azure storage-konto. Om du inte har ett Azure Storage-konto går du till artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md) . När du har skapat lagringskontot kan behöver du hämta den kontonyckel som används för att komma åt lagringsutrymmet. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Etablerade **SQL Server på en virtuell Azure-dator**. Instruktioner finns i [Konfigurera en virtuell Azure SQL Server-dator som en IPython Notebook-Server för avancerad analys](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Flytta data från en platt fil källa till SQL Server på en virtuell Azure-dator
Om dina data är i en platt fil (ordnade i rader/kolumner), kan de flyttas till SQL Server-VM på Azure via följande metoder:

1. [Kommando rads verktyget för Mass kopiering (BCP)](#insert-tables-bcp)
2. [Mass infogning av SQL-fråga](#insert-tables-bulkquery)
3. [Grafiska inbyggda verktyg i SQL Server (import/export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Kommando rads verktyget för Mass kopiering (BCP)
BCP är ett kommandoradsverktyg som installerats med SQL Server och ett av de snabbaste sätten att flytta data. Den fungerar på alla tre SQL Server varianter (lokala SQL Server, SQL Azure och SQL Server VM på Azure).

> [!NOTE]
> **Var ska mina data vara för BCP?**  
> Det är inte obligatoriskt, kan med filer som innehåller källdata finns på samma dator som SQL Server-målservern snabbare överföring (network hastighet jämfört med lokala disk i/o-hastighet). Du kan flytta de flata filerna som innehåller data till den dator där SQL Server installeras med olika fil kopierings verktyg som [AzCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](https://storageexplorer.com/) eller Windows Copy/klistra in via Remote Desktop Protocol (RDP).
>
>

1. Se till att databasen och tabellerna har skapats i måldatabasen i SQL Server. Här är ett exempel på hur du gör med `Create Database`-och `Create Table`-kommandon:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Generera format filen som beskriver schemat för tabellen genom att utfärda följande kommando från kommando raden på den dator där BCP är installerat.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Infoga data i databasen med kommandot BCP, som ska fungera från kommando raden när SQL Server installeras på samma dator:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimera BCP-infogningar** Mer information om hur du optimerar sådana infogningar finns i följande artikel [rikt linjer för att optimera Mass import](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) .
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Parallella infogningar för snabbare data förflyttning
Om de data som du flyttar är stora kan du påskynda det genom att samtidigt köra flera BCP-kommandon parallellt i ett PowerShell-skript.

> [!NOTE]
> **Big data inmatning** För att optimera data inläsningen för stora och väldigt stora data uppsättningar, partitionerar du dina logiska och fysiska databas tabeller med flera fil grupper och partitionstabeller. Mer information om hur du skapar och läser in data för att partitionera tabeller finns i [SQL partition tables Parallel load](parallel-load-sql-partitioned-tables.md).
>
>

Följande exempel på PowerShell-skript visar parallella infogningar med BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="insert-tables-bulkquery"></a>Mass infogning av SQL-fråga
[Mass infogning av SQL-fråga](https://msdn.microsoft.com/library/ms188365) kan användas för att importera data till databasen från rad/kolumnbaserade filer (de typer som stöds finns i avsnittet[förbereda data för Mass export eller import (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Här följer några exempelkommandon för Bulk Insert är enligt nedan:  

1. Analysera dina data och ange några anpassade alternativ innan du importerar för att se till att SQL Server-databasen förutsätter samma format för eventuella särskilda fält, till exempel datum. Här är ett exempel på hur du konfigurerar datumformatet som år-månad-dag (om dina data innehåller datumet i formatet år-månad-dag):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importera data med hjälp av bulk importuttryck:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="sql-builtin-utilities"></a>Inbyggda verktyg i SQL Server
Du kan använda SQL Server Integration Services (SSIS) för att importera data till SQL Server VM på Azure från en platt fil.
SSIS är tillgängligt i två studio-miljöer. Mer information finns i avsnittet om [integrerings tjänster (SSIS) och Studio miljöer](https://technet.microsoft.com/library/ms140028.aspx):

* Mer information om SQL Server Data Tools finns i [Microsoft SQL Server data verktyg](https://msdn.microsoft.com/data/tools.aspx)  
* Mer information om guiden Importera/exportera finns i [SQL Server import-och export guiden](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Flytta data från lokala SQL Server till SQL Server på en virtuell Azure-dator
Du kan också använda följande migreringsstrategier:

1. [Distribuera en SQL Server-databas till en Microsoft Azure VM-guide](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportera till flat fil](#export-flat-file)
3. [Guiden SQL Database migrering](#sql-migration)
4. [Säkerhetskopiera och återställa databas](#sql-backup)

Vi beskriver vart och ett av följande alternativ:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuera en SQL Server-databas till en Microsoft Azure VM-guide
Att **distribuera en SQL Server databas till en Microsoft Azure VM-guide** är ett enkelt och rekommenderat sätt att flytta data från en lokal SQL Server-instans till SQL Server på en virtuell Azure-dator. Detaljerade anvisningar och en beskrivning av andra alternativ finns i [Migrera en databas till SQL Server på en virtuell Azure-dator](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportera till flat fil
Du kan använda olika metoder för att massredigera data från en lokal SQL Server som dokumenteras i avsnittet [Mass import och export av data (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Det här dokumentet beskriver Bulk Copy Program (BCP) som ett exempel. När data har exporterats till en platt fil kan kan det importeras till en annan SQLServer med massimport.

1. Exportera data från en lokal SQL Server till en fil med BCP för följande

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Skapa databasen och tabellen på SQL Server VM på Azure med hjälp av `create database` och `create table` för det tabell schema som exporterades i steg 1.
3. Skapa en formatfil för att beskriva tabellschemat av data som exporteras/importerade. Information om format filen beskrivs i [skapa en format fil (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatera filen när du kör BCP från SQL Server-datorn

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formatera filen när du kör BCP via en fjärranslutning mot en SQL-Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Använd någon av de metoder som beskrivs i avsnittet [Flytta data från fil källan](#filesource_to_sqlonazurevm) för att flytta data i flata filer till en SQL Server.

### <a name="sql-migration"></a>Guiden SQL Database migrering
Med guiden för att [migrera SQL Server Database](https://sqlazuremw.codeplex.com/) kan du använda ett användarvänligt sätt för att flytta data mellan två SQL Server-instanser. Det gör att användaren vill mappa dataschemat mellan källor och måltabellerna kolumntyper och olika funktioner. Masskopiering (BCP) under försättsbladen används. En skärmbild på välkomstskärmen för migrering av SQL Database-guiden visas nedan.  

![Guiden för SQL Server-migrering][2]

### <a name="sql-backup"></a>Säkerhetskopiera och återställa databas
Har stöd för SQL Server:

1. [Databas säkerhets kopierings-och återställnings funktioner](https://msdn.microsoft.com/library/ms187048.aspx) (både till en lokal fil eller BACPAC exporteras till BLOB) och [data skikts program](https://msdn.microsoft.com/library/ee210546.aspx) (med BACPAC).
2. Möjlighet att skapa SQL Server-datorer direkt i Azure med en kopierade databasen eller kopiera till en befintlig SQL Azure-databas. Mer information finns i [använda guiden Kopiera databas](https://msdn.microsoft.com/library/ms188664.aspx).

En skärmbild av databasen tillbaka upp/återställning alternativ från SQL Server Management Studio visas nedan.

![Verktyget för SQL Server-Import][1]

## <a name="resources"></a>Resurser
[Migrera en databas till SQL Server på en virtuell Azure-dator](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Översikt över SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png

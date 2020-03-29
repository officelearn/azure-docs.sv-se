---
title: Flytta data till en virtuell SQL Server-dator - Team Data Science Process
description: Flytta data från platta filer eller från en lokal SQL Server till SQL Server på Azure VM.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721378"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flytta data till SQL Server på en virtuell Azure-dator

I den här artikeln beskrivs alternativen för att flytta data antingen från platta filer (CSV- eller TSV-format) eller från en lokal SQL Server till SQL Server på en virtuell Azure-dator. Dessa uppgifter för att flytta data till molnet är en del av teamdatavetenskapsprocessen.

Ett ämne som beskriver alternativen för att flytta data till en Azure SQL-databas för maskininlärning finns i [Flytta data till en Azure SQL-databas för Azure Machine Learning](move-sql-azure.md).

I följande tabell sammanfattas alternativen för att flytta data till SQL Server på en virtuell Azure-dator.

| <b>Källkod</b> | <b>DESTINATION: SQL Server på Azure VM</b> |
| --- | --- |
| <b>Platt fil</b> |1. <a href="#insert-tables-bcp">Kommandoradsverktyget för masskopiering (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">SQL-fråga för massinfogning</a><br> 3. <a href="#sql-builtin-utilities">Grafiska inbyggda verktyg i SQL Server</a> |
| <b>Lokal SQL-server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Distribuera en SQL Server-databas till en Microsoft Azure VM-guide</a><br> 2. <a href="#export-flat-file">Exportera till en platt fil</a><br> 3. <a href="#sql-migration">Guiden Migrering av SQL-databas</a> <br> 4. <a href="#sql-backup">Databas säkerhetskopiera och återställa</a><br> |

Det här dokumentet förutsätter att SQL-kommandon körs från SQL Server Management Studio eller Visual Studio Database Explorer.

> [!TIP]
> Som ett alternativ kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att skapa och schemalägga en pipeline som flyttar data till en VIRTUELL SQL Server på Azure. Mer information finns i [Kopiera data med Azure Data Factory (Kopiera aktivitet)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Krav
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure-lagringskonto**. Du kommer att använda ett Azure-lagringskonto för att lagra data i den här självstudien. Om du inte har ett Azure-lagringskonto läser du artikeln [Skapa ett lagringskonto.](../../storage/common/storage-account-create.md) När du har skapat lagringskontot måste du hämta kontonyckeln som används för att komma åt lagringen. Se [Hantera åtkomstnycklar för lagringskonto.](../../storage/common/storage-account-keys-manage.md)
* Etablerad **SQL Server på en Virtuell Azure.** Instruktioner finns i [Konfigurera en virtuell Azure SQL Server-dator som en IPython Notebook-server för avancerad analys](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Flytta data från en platt filkälla till SQL Server på en Virtuell Azure
Om dina data finns i en platt fil (ordnade i ett rad-/kolumnformat) kan de flyttas till VIRTUELL SQL Server på Azure via följande metoder:

1. [Kommandoradskopieringsverktyg (BCP)](#insert-tables-bcp)
2. [SQL-fråga för massinfogning](#insert-tables-bulkquery)
3. [Grafiska inbyggda verktyg i SQL Server (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Kommandoradskopieringsverktyg (BCP)
BCP är ett kommandoradsverktyg som installeras med SQL Server och är ett av de snabbaste sätten att flytta data. Det fungerar i alla tre SQL Server-varianter (Lokal SQL Server, SQL Azure och SQL Server VM på Azure).

> [!NOTE]
> **Var ska mina data vara för BCP?**  
> Även om det inte krävs, med filer som innehåller källdata som finns på samma dator som målet SQL Server möjliggör snabbare överföringar (nätverkshastighet vs lokal disk IO hastighet). Du kan flytta de platta filer som innehåller data till den dator där SQL Server är installerat med hjälp av olika filkopieringsverktyg som [AZCopy,](../../storage/common/storage-use-azcopy.md) [Azure Storage Explorer](https://storageexplorer.com/) eller Windows Kopiera/klistra in via RDP (Remote Desktop Protocol).
>
>

1. Kontrollera att databasen och tabellerna skapas i målet SQL Server-databasen. Här är ett exempel på hur `Create Database` `Create Table` du gör det med kommandona och:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Generera formatfilen som beskriver schemat för tabellen genom att utfärda följande kommando från kommandoraden på den dator där bcp är installerat.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Infoga data i databasen med kommandot bcp, som ska fungera från kommandoraden när SQL Server är installerat på samma dator:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimera BCP-infogningar** Se följande artikel ["Riktlinjer för optimering av massimport" för](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) att optimera sådana skär.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Parallellisera skär för snabbare dataförflyttning
Om de data du flyttar är stora kan du snabba upp saker och ting genom att samtidigt köra flera BCP-kommandon parallellt i ett PowerShell-skript.

> [!NOTE]
> **Intag av stordata** Om du vill optimera datainläsning för stora och mycket stora datauppsättningar partitionerar du dina logiska och fysiska databastabeller med hjälp av flera filgrupper och partitionstabeller. Mer information om hur du skapar och läser in data till partitionstabeller finns i [SQL Partition-tabeller för parallell belastning](parallel-load-sql-partitioned-tables.md).
>
>

Följande exempel på PowerShell-skript visar parallella infogningar med bcp:

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

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-fråga för massinfogning
[Massinfogning SQL Query](https://msdn.microsoft.com/library/ms188365) kan användas för att importera data till databasen från rad-/kolumnbaserade filer (de typer som stöds beskrivs i avsnittet[Förbered data för massexport eller import (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Här är några exempelkommandon för Massinfogning är följande:  

1. Analysera dina data och ange eventuella anpassade alternativ innan du importerar för att se till att SQL Server-databasen antar samma format för alla specialfält, till exempel datum. Här är ett exempel på hur du ställer in datumformatet som årsmånadsdag (om dina data innehåller datumet i årsmånadsformat):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importera data med hjälp av massimportsatser:

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

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Inbyggda verktyg i SQL Server
Du kan använda SQL Server Integration Services (SSIS) för att importera data till SQL Server VM på Azure från en platt fil.
SSIS finns i två studiomiljöer. Mer information finns i [Integrationstjänster (SSIS) och Studio-miljöer:](https://technet.microsoft.com/library/ms140028.aspx)

* Mer information om SQL Server-dataverktyg finns i [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Mer information om guiden Importera/exportera finns i [guiden Import och export av SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Flytta data från lokal SQL Server till SQL Server på en virtuell Azure-dator
Du kan också använda följande migreringsstrategier:

1. [Distribuera en SQL Server-databas till en Microsoft Azure VM-guide](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportera till platt fil](#export-flat-file)
3. [Guiden Migrering av SQL-databas](#sql-migration)
4. [Databasen säkerhetskopierar och återställer](#sql-backup)

Vi beskriver vart och ett av dessa alternativ nedan:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuera en SQL Server-databas till en Microsoft Azure VM-guide
**Guiden Distribuera en SQL Server-databas till en Microsoft Azure VM-guide** är ett enkelt och rekommenderat sätt att flytta data från en lokal SQL Server-instans till SQL Server på en Virtuell Azure-dator. Detaljerade steg och en diskussion om andra alternativ finns i [Migrera en databas till SQL Server på en Virtuell Azure.](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportera till platt fil
Olika metoder kan användas för att massexport exportera data från en lokal SQL Server som dokumenteras i avsnittet [Massimport och export av data (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Det här dokumentet täcker bcp (Bulk Copy Program) som ett exempel. När data har exporterats till en platt fil kan de importeras till en annan SQL-server med massimport.

1. Exportera data från lokal SQL Server till en fil med bcp-verktyget enligt följande

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Skapa databasen och tabellen på SQL Server `create database` VM `create table` på Azure med hjälp av och för tabellschemat som exporteras i steg 1.
3. Skapa en formatfil för att beskriva tabellschemat för de data som exporteras/importeras. Information om formatfilen beskrivs i [Skapa en formatfil (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatera filgenerering när BCP körs från SQL Server-datorn

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formatera filgenerering när BCP körs på distans mot en SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Använd någon av de metoder som beskrivs i avsnittet [Flytta data från filkälla för](#filesource_to_sqlonazurevm) att flytta data i platta filer till en SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Guiden Migrering av SQL-databas
[Guiden Migrering av SQL Server-databas](https://sqlazuremw.codeplex.com/) är ett användarvänligt sätt att flytta data mellan två SQL-serverinstanser. Det gör det möjligt för användaren att mappa dataschemat mellan källor och måltabeller, välja kolumntyper och olika andra funktioner. Den använder bulk kopia (BCP) under täcket. En skärmbild av välkomstskärmen för guiden Sql Database Migration visas nedan.  

![Guiden Migrering av SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Databasen säkerhetskopierar och återställer
SQL Server stöder:

1. [Databas säkerhetskopiering och återställning (både](https://msdn.microsoft.com/library/ms187048.aspx) till en lokal fil eller bacpac export till blob) och [Data Tier Applications](https://msdn.microsoft.com/library/ee210546.aspx) (med bacpac).
2. Möjlighet att direkt skapa virtuella SQL Server-datorer på Azure med en kopierad databas eller kopiera till en befintlig SQL Azure-databas. Mer information finns [i Använda guiden Kopiera databas](https://msdn.microsoft.com/library/ms188664.aspx).

En skärmbild av alternativen för säkerhetskopiering/återställning av databasen från SQL Server Management Studio visas nedan.

![Importverktyg för SQL Server][1]

## <a name="resources"></a>Resurser
[Migrera en databas till SQL Server på en virtuell Azure-dator](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Översikt över SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png

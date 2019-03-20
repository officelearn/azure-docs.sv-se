---
title: Flytta data till en SQL Server-dator – Team Data Science Process
description: Flytta data från flata filer eller från en lokal SQL Server till SQL Server på Azure VM.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 47a77def43a9577e5a3506899da47db2f684b495
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835130"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flytta data till SQL Server på en virtuell Azure-dator

Den här artikeln beskriver alternativ för att flytta data från flata filer (CSV- eller TSV format) eller från en lokal SQL Server till SQL Server på virtuella Azure-datorer. Dessa uppgifter för att flytta data till molnet är en del av Team Data Science Process.

Ett avsnitt som visar alternativ för att flytta data till en Azure SQL-databas för Machine Learning, se [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

I följande tabell sammanfattas alternativen för att flytta data till SQL Server på virtuella Azure-datorer.

| <b>KÄLLA</b> | <b>MÅL: SQLServer på Azure VM</b> |
| --- | --- |
| <b>Flat fil</b> |1. <a href="#insert-tables-bcp">Kommandoradsverktyget bulk copy-verktyget (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Bulk Insert SQL-fråga </a><br> 3. <a href="#sql-builtin-utilities">Grafiska inbyggda verktyg i SQLServer</a> |
| <b>En lokal SQLServer</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Distribuera en SQL Server-databas till en Microsoft Azure VM-guide</a><br> 2. <a href="#export-flat-file">Exportera till en flat fil </a><br> 3. <a href="#sql-migration">Migreringsguide för SQL-databas </a> <br> 4. <a href="#sql-backup">Databasen tillbaka upp och återställa </a><br> |

Observera att det här dokumentet förutsätter att SQL-kommandon utförs från SQL Server Management Studio eller Visual Studio Database Explorer.

> [!TIP]
> Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) att skapa och schemalägga en pipeline som flyttar data till en SQL Server-VM på Azure. Mer information finns i [kopiera data med Azure Data Factory (Kopieringsaktiviteten)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Förhandskrav
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. För att lagra data i den här självstudien använder du ett Azure storage-konto. Om du inte har ett Azure storage-konto kan du läsa den [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md) artikeln. När du har skapat lagringskontot kan behöver du hämta den kontonyckel som används för att komma åt lagringsutrymmet. Se [hantera dina lagringsåtkomstnycklar](../../storage/common/storage-account-manage.md#access-keys).
* Etablerade **SQLServer på en Azure-VM**. Anvisningar finns i [ställa in en Azure SQL Server-dator som en IPython Notebook-server för avancerade analyser](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Installerat och konfigurerat **Azure PowerShell** lokalt. Anvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Flytta data från en flat fil-källa till SQL Server på en Azure VM
Om dina data är i en platt fil (ordnade i rader/kolumner), kan de flyttas till SQL Server-VM på Azure via följande metoder:

1. [Kommandoradsverktyget bulk copy-verktyget (BCP)](#insert-tables-bcp)
2. [Bulk Insert SQL-fråga](#insert-tables-bulkquery)
3. [Grafiska inbyggda verktyg i SQLServer (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Kommandoradsverktyget bulk copy-verktyget (BCP)
BCP är ett kommandoradsverktyg som installerats med SQL Server och ett av de snabbaste sätten att flytta data. Det fungerar över alla tre SQL Server-varianter (en lokal SQLServer, SQL Azure och SQL Server-VM på Azure).

> [!NOTE]
> **Var ska Mina data för BCP?**  
> Det är inte obligatoriskt, kan med filer som innehåller källdata finns på samma dator som SQL Server-målservern snabbare överföring (network hastighet jämfört med lokala disk i/o-hastighet). Du kan flytta flata filer som innehåller data till datorn där SQL Server installeras med hjälp av olika filkopieringen verktyg som [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](https://storageexplorer.com/) eller windows kopiera och klistra in via fjärrskrivbord Protokoll (RDP).
>
>

1. Se till att databasen och tabellerna har skapats i måldatabasen i SQL Server. Här är ett exempel på hur du använder den `Create Database` och `Create Table` kommandon:

```sql
CREATE DATABASE <database_name>

CREATE TABLE <tablename>
(
    <columnname1> <datatype> <constraint>,
    <columnname2> <datatype> <constraint>,
    <columnname3> <datatype> <constraint>
)
```

1. Generera-fil som beskriver schemat för tabellen genom att följande kommando från kommandoraden för datorn där bcp är installerad.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Infoga data i databasen med kommandot bcp på följande sätt. Detta bör fungera från kommandoraden, förutsatt att SQL Server är installerad på samma dator:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimera BCP infogar** finns i följande artikel [”riktlinjer för att optimera massimport'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) att optimera sådana infogningar.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Parallellisera infogningar för snabbare dataförflyttning
Om du flyttar data är stort, kan du påskynda processen genom att samtidigt köra flera BCP kommandon parallellt i ett PowerShell-skript.

> [!NOTE]
> **Stordata inmatning** för att optimera datainläsning för stora och mycket stora datauppsättningar, partitionera dina logiska och fysiska databastabeller som använder flera filgrupper och partitionera tabeller. Läs mer om att skapa och läser in data till partitionstabeller [parallell inläsning SQL-partitionstabeller](parallel-load-sql-partitioned-tables.md).
>
>

Följande PowerShell-exempelskript visar parallella infogningar med bcp:

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

### <a name="insert-tables-bulkquery"></a>Bulk Insert SQL-fråga
[Bulk Insert SQL-fråga](https://msdn.microsoft.com/library/ms188365) kan användas för att importera data till databasen från rad/kolumn baserad filer (typerna som stöds beskrivs i den[förbereda Data för Bulk exportera eller importera (SQL Server)](https://msdn.microsoft.com/library/ms188609)) avsnittet.

Här följer några exempelkommandon för Bulk Insert är enligt nedan:  

1. Analysera dina data och ange några anpassade alternativ innan du importerar för att se till att SQL Server-databasen förutsätter samma format för eventuella särskilda fält, till exempel datum. Här är ett exempel på hur du konfigurerar datumformatet som år-månad-dag (om dina data innehåller datumet i formatet år-månad-dag):

```sql
SET DATEFORMAT ymd;
```
1. Importera data med hjälp av bulk importuttryck:

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

### <a name="sql-builtin-utilities"></a>Inbyggda verktyg i SQLServer
Du kan använda SQL Server integration Services (SSIS) för att importera data till SQL Server-VM på Azure från en platt fil.
SSIS är tillgängligt i två studio-miljöer. Mer information finns i [Integration Services (SSIS) och Studio miljöer](https://technet.microsoft.com/library/ms140028.aspx):

* Mer information om SQL Server Data Tools finns [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Mer information om guiden Import/Export finns [SQL Server importera och exportera guiden](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Information om hur du flyttar Data från en lokal SQLServer till SQLServer på en Azure virtuell dator
Du kan också använda följande migreringsstrategier:

1. [Distribuera en SQL Server-databas till en Microsoft Azure VM-guide](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportera till Flat fil](#export-flat-file)
3. [Migreringsguide för SQL-databas](#sql-migration)
4. [Databasen tillbaka upp och återställa](#sql-backup)

Vi beskriver varje nedan:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuera en SQL Server-databas till en Microsoft Azure VM-guide
Den **distribuera en SQL Server-databas till en Microsoft Azure VM-Guide** är ett enkelt och rekommenderade sätt att flytta data från en lokal SQL Server-instansen till SQL Server på en Azure VM. För detaljerade anvisningar och en beskrivning av andra alternativ, se [migrera en databas till SQL Server på en Azure VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportera till Flat fil
Olika-metoder kan användas för att massregistrera exportera data från en lokal SQL Server enligt beskrivningen i den [massimport och Export av Data (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) avsnittet. Det här dokumentet beskriver Bulk Copy Program (BCP) som ett exempel. När data har exporterats till en platt fil kan kan det importeras till en annan SQLServer med massimport.

1. Exportera data från en lokal SQL Server till en fil med BCP för följande

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Skapa databasen och tabellen på SQL Server-VM på Azure med hjälp av den `create database` och `create table` för tabellschemat exporterades i steg 1.
3. Skapa en formatfil för att beskriva tabellschemat av data som exporteras/importerade. Formatfilen beskrivs i [skapa en formatfil (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatera filen när du kör BCP från SQL Server-datorn

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formatera filen när du kör BCP via en fjärranslutning mot en SQL-Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Använda någon av metoderna som beskrivs i avsnittet [flyttar Data från filkälla](#filesource_to_sqlonazurevm) att flytta data i flata filer till en SQL Server.

### <a name="sql-migration"></a>Migreringsguide för SQL-databas
[Migreringsguide för SQL Server-databas](https://sqlazuremw.codeplex.com/) är ett användarvänligt sätt att flytta data mellan två SQL server-instanser. Det gör att användaren vill mappa dataschemat mellan källor och måltabellerna kolumntyper och olika funktioner. Masskopiering (BCP) under försättsbladen används. En skärmbild på välkomstskärmen för migrering av SQL Database-guiden visas nedan.  

![Guiden för SQL Server-migrering][2]

### <a name="sql-backup"></a>Databasen tillbaka upp och återställa
Har stöd för SQL Server:

1. [Databasen tillbaka upp- och återställningsfunktionen](https://msdn.microsoft.com/library/ms187048.aspx) (båda till en lokal fil eller bacpac export till blob) och [nivån dataprogram](https://msdn.microsoft.com/library/ee210546.aspx) (med bacpac).
2. Möjlighet att skapa SQL Server-datorer direkt i Azure med en kopierade databasen eller kopiera till en befintlig SQL Azure-databas. Mer information finns i [använda guiden Kopiera databasen](https://msdn.microsoft.com/library/ms188664.aspx).

En skärmbild av databasen tillbaka upp/återställning alternativ från SQL Server Management Studio visas nedan.

![Verktyget för SQL Server-Import][1]

## <a name="resources"></a>Resurser
[Migrera en databas till SQLServer på en virtuell Azure-dator](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Översikt över SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png

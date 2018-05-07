---
title: Flytta data till SQL Server på en virtuell Azure-dator | Microsoft Docs
description: Flytta data från flata filer eller från en lokal SQL Server till SQL Server på Azure VM.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 56a03347556f9ae3452548e85ce5d46f3961ed93
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flytta data till SQL Server på en virtuell Azure-dator
Det här avsnittet beskrivs alternativen för att flytta data från flata filer (CSV eller TVS format) eller från en lokal SQL Server till SQL Server på en virtuell Azure-dator. Dessa uppgifter för att flytta data till molnet är en del av Team av vetenskapliga data.

Ett avsnitt som visar alternativen för att flytta data till en Azure SQL Database för Machine Learning finns [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

Den **menyn** nedan länkar till avsnitt som beskriver hur du mata in data i andra mål-miljöer där data kan lagras och behandlas under Team Data vetenskap processen (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

I följande tabell sammanfattas alternativ för att flytta data till SQL Server på en virtuell Azure-dator.

| <b>KÄLLA</b> | <b>MÅL: SQLServer på Azure VM</b> |
| --- | --- |
| <b>Flat-fil</b> |1. <a href="#insert-tables-bcp">Kommandoradsverktyget bulk copy-verktyget (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Bulk Insert SQL-fråga </a><br> 3. <a href="#sql-builtin-utilities">Grafisk inbyggda verktyg i SQLServer</a> |
| <b>Lokal SQLServer</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Distribuera en SQL Server-databas till en Microsoft Azure VM-guiden</a><br> 2. <a href="#export-flat-file">Exportera till en flat-fil </a><br> 3. <a href="#sql-migration">Migreringsguiden för SQL-databas </a> <br> 4. <a href="#sql-backup">Databasen tillbaka in och återställa </a><br> |

Observera att det här dokumentet förutsätts att SQL-kommandon körs från SQL Server Management Studio eller Visual Studio Database Explorer.

> [!TIP]
> Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) att skapa och schemalägga en pipeline som flyttar data till en virtuell dator med SQL Server på Azure. Mer information finns i [kopiera data med Azure Data Factory (Kopieringsaktiviteten)](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Förhandskrav
Den här kursen förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du använder ett Azure storage-konto för att lagra data i den här kursen. Om du inte har ett Azure storage-konto finns i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artikel. När du har skapat lagringskontot kan behöver du hämta nyckeln konto används för åtkomst till lagringen. Se [hantera åtkomstnycklar för lagring](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Etablerad **SQLServer på en virtuell dator i Azure**. Instruktioner finns i [ställa in en Azure SQL Server-dator som en bärbar dator IPython server för avancerade analyser](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Flytta data från en flat filkälla till SQL Server på en Azure VM
Om dina data finns i en flat-fil (ordnade i rader/kolumner), kan den flyttas till SQL Server-VM på Azure via följande metoder:

1. [Kommandoradsverktyget bulk copy-verktyget (BCP)](#insert-tables-bcp)
2. [Bulk Insert SQL-fråga ](#insert-tables-bulkquery)
3. [Grafisk inbyggda verktyg i SQLServer (importera och exportera, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Kommandoradsverktyget bulk copy-verktyget (BCP)
BCP är ett kommandoradsverktyg som installerats med SQL Server och ett av de snabbaste sätten att flytta data. Fungerar för alla tre SQL Server-varianter (lokal SQLServer, SQL Azure och SQL Server-VM på Azure).

> [!NOTE]
> **Var ska Mina data för BCP?**  
> Det är inte obligatoriskt, kan med filer som innehåller källdata finns på samma dator som SQL Server-målservern för snabbare överföring (network hastighet vs lokal disk i/o-hastighet). Du kan flytta flat-filer som innehåller data till datorn där SQL Server installeras med hjälp av olika filkopieringen verktyg som [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Lagringsutforskaren](http://storageexplorer.com/) eller windows kopiera och klistra in via fjärrskrivbord Protokoll (RDP).
>
>

1. Kontrollera att databasen och tabeller skapas på SQL Server måldatabasen. Här är ett exempel på hur du gör detta med hjälp av den `Create Database` och `Create Table` kommandon:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Skapa formatfilen som beskriver schemat för tabellen genom att utfärda kommandot från kommandoraden på datorn där bcp är installerad.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Infoga data i databasen med bcp-kommandot på följande sätt. Detta bör fungera från kommandoraden, förutsatt att SQL Server är installerat på samma dator:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimera BCP infogar** finns i följande artikel ['Riktlinjer för hur du optimerar massimport'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) att optimera sådana infogningar.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Parallelizing infogningar för snabbare dataflyttning
Om du flyttar data är stort, kan du påskynda processen genom att samtidigt köra flera BCP kommandon parallellt i ett PowerShell-skript.

> [!NOTE]
> **Stordata införandet** partitions logiska och fysiska databastabeller med flera filgrupper och partition tabeller för att optimera datainläsning för stora och mycket stora datamängder. Mer information om att skapa och läsa in data för partitionstabeller finns [parallella belastningen SQL-partitionstabell](parallel-load-sql-partitioned-tables.md).
>
>

Exempel PowerShell-skriptet nedan visar parallella infogningar med bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
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


### <a name="insert-tables-bulkquery"></a>Bulk Insert SQL-fråga
[Bulk Insert SQL-frågan](https://msdn.microsoft.com/library/ms188365) kan användas för att importera data till databasen från raden/kolumnen baserat filer (beskrivs typerna som stöds i den[förbereder Data för Bulk exportera eller importera (SQL Server)](https://msdn.microsoft.com/library/ms188609)) avsnittet.

Här följer några exempelkommandon för Bulk Insert är enligt nedan:  

1. Analysera dina data och ange anpassade alternativ innan du importerar för att se till att samma format för eventuella särskilda fält, till exempel datum förutsätter att SQL Server-databasen. Här är ett exempel på hur du anger formatet som år-månad-dag (om dina data innehåller datumet i formatet år-månad-dag):

        SET DATEFORMAT ymd;    
2. Importera data med hjälp av bulk importuttryck:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Inbyggda verktyg i SQLServer
Du kan använda SQL Server integration Services (SSIS) för att importera data till SQL Server-VM på Azure från en flat-fil.
SSIS finns i två studio miljöer. Mer information finns i [Integration Services (SSIS) och Studio miljöer](https://technet.microsoft.com/library/ms140028.aspx):

* Mer information om SQL Server Data Tools finns [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Mer information om guiden Importera och exportera finns [SQL Server importera och exportera](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Flytta Data från lokala SQLServer till SQLServer på en virtuell dator i Azure
Du kan också använda följande migreringsstrategier:

1. [Distribuera en SQL Server-databas till en Microsoft Azure VM-guiden](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportera till Flat-fil](#export-flat-file)
3. [Migreringsguiden för SQL-databas](#sql-migration)
4. [Databasen tillbaka in och återställa](#sql-backup)

Vi beskrivs var och en av dessa nedan:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuera en SQL Server-databas till en Microsoft Azure VM-guiden
Den **distribuera en SQL Server-databas till en Microsoft Azure VM-guiden** är ett enkelt och rekommenderade sätt att flytta data från en lokal SQL Server-instansen till SQL Server på en Azure VM. För detaljerade anvisningar samt en beskrivning av andra alternativ, se [migrera en databas till SQL Server på en Azure VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportera till Flat-fil
Olika metoder som kan användas för att Massredigera exportera data från en lokal SQL Server enligt beskrivningen i den [massimport och Export av Data (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) avsnittet. Det här dokumentet beskriver Bulk Copy Program (BCP) som ett exempel. När data har exporterats till en flat-fil kan importeras den till en annan SQLServer med massimport.

1. Exportera data från lokala SQL Server till en fil med bcp-verktyget på följande sätt

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Skapa databasen och tabellen på SQL Server-VM på Azure med hjälp av `create database` och `create table` för tabellschemat exporterade i steg 1.
3. Skapa en fil för att beskriva tabellens schema för data som exporteras eller importerade. Information om formatfilen beskrivs i [skapa formatfilen (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatera Filgenerering när du kör BCP från SQL Server-datorn

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formatera Filgenerering när körs via fjärranslutning BCP mot en SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Använd någon av metoderna som beskrivs i avsnittet [flytta Data från filkälla](#filesource_to_sqlonazurevm) att flytta data i flat-filer till en SQL Server.

### <a name="sql-migration"></a>Migreringsguiden för SQL-databas
[SQL Server-databasen migreringsguiden](http://sqlazuremw.codeplex.com/) ett användarvänligt sätt att flytta data mellan två SQL server-instanser. Tillåter användaren att mappa dataschemat mellan datakällor och måltabell, Välj kolumntyper och andra funktioner. Masskopiering (BCP) under försättsbladen används. En skärmbild av välkomstskärmen för guiden Migrera för SQL-databasen visas nedan.  

![Guiden för SQL Server-migrering][2]

### <a name="sql-backup"></a>Databasen tillbaka in och återställa
Har stöd för SQL Server:

1. [Databasen tillbaka in och återställa funktionen](https://msdn.microsoft.com/library/ms187048.aspx) (både en lokal fil eller en bacpac exportera till blob) och [Data nivåer program](https://msdn.microsoft.com/library/ee210546.aspx) (med bacpac).
2. Möjlighet att skapa SQL Server-datorer direkt i Azure med en kopierade databas eller kopiera till en befintlig SQL Azure-databas. Mer information finns i [använder guiden Kopiera databasen](https://msdn.microsoft.com/library/ms188664.aspx).

En skärmbild av databasen upp/återställa alternativ från SQL Server Management Studio visas nedan.

![Importverktyget för SQL Server][1]

## <a name="resources"></a>Resurser
[Migrera en databas till SQLServer på en virtuell dator i Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Översikt över SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png

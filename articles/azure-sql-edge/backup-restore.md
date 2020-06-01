---
title: Säkerhetskopiera och återställa databaser – Azure SQL Edge (för hands version)
description: Läs mer om funktioner för säkerhets kopiering och återställning i Azure SQL Edge (för hands version)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235191"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Säkerhetskopiera och återställa databaser i Azure SQL Edge (för hands version) 

Azure SQL Edge bygger på de senaste versionerna av Microsoft SQL Server Database Engine på Linux, vilket ger liknande säkerhets kopierings-och återställnings databas funktioner som är tillgängliga i SQL Server på Linux och SQL Server som körs i behållare. Säkerhets kopierings-och återställnings komponent är ett viktigt skydd för att skydda data som lagras i dina Azure SQL Edge-databaser. För att minimera risken för katastrof data förlust rekommenderar vi att du säkerhetskopierar dina databaser regelbundet för att bevara ändringar i dina data regelbundet. Med en välplanerad strategi för säkerhetskopiering och återställning kan du skydda databaser mot dataförlust på grund av en mängd olika fel. Testa din strategi genom att återställa en uppsättning säkerhetskopior och sedan återställa databasen om du vill lära dig att svara effektivt på en katastrof.

Mer information om varför säkerhets kopieringar är viktiga finns i [säkerhetskopiera och återställa SQL Server-databaser](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Azure SQL Edge stöder säkerhets kopiering till och återställning från både lokal lagring eller från Azure-blobbar. Mer information om hur du säkerhetskopierar och återställer från Azure Blob Storage finns [SQL Server säkerhets kopiering och återställning med Microsoft Azure Blob Storage tjänst](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) och [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Säkerhetskopiera en databas i Azure SQL Edge

Azure SQL Edge stöder samma säkerhets kopierings typer som stöds av SQL Server. En fullständig lista över de säkerhets kopierings typer som stöds i SQL Server finns i [Översikt över säkerhets kopiering](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Databaser som skapats i Azure SQL Edge använder enkel återställnings modell som standard. Eftersom sådana logg säkerhets kopior inte kan utföras på dessa databaser. Om du behöver utföra logg säkerhets kopieringar på dessa databaser måste administratören ändra databas återställnings modellen till fullständig återställnings modell. En fullständig lista över återställnings modeller som stöds av SQL Server finns i [Översikt över återställnings modellen](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Säkerhetskopiera till lokal disk

I exemplet nedan används Transact-SQL-kommandot för säkerhets kopierings databasen för att skapa en säkerhets kopia av databasen i behållaren. I det här exemplet skapas en ny mapp med namnet "Backup" för att lagra säkerhetskopieringsfilerna.

1. Skapa en mapp för säkerhets kopiorna. Det här kommandot måste köras på den värd där din Azure SQL Edge-behållare körs. I kommandot nedan ersätter du **<AzureSQLEdge_Container_Name>** med namnet på Azure SQL Edge-behållaren i distributionen.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Anslut till Azure SQL Edge-instansen med SQL Server Management Studio (SSMS) eller Använd Azure Data Studio (ADS) och kör säkerhets kopierings databasen för att säkerhetskopiera användar databasen. I exemplet nedan tar vi säkerhets kopian av *IronOreSilicaPrediction* -databasen.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. När du har kört kommandot och om säkerhets kopian av databasen lyckas visas meddelanden som liknar följande i avsnittet resultat i SSMS eller ADS.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Säkerhetskopiera till URL

Azure SQL Edge stöder säkerhets kopiering till både sid-blobar och block-blobar. Mer information om Page blobbar och block blobs finns i [säkerhets kopiering för att blockera BLOB vs Page BLOB](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). I exemplet nedan säkerhets kopie ras Database *IronOreSilicaPrediction* till en Block-Blob. 

1. Det första steget i att konfigurera säkerhets kopieringar för att blockera blobbar är att generera en SAS-token (signatur för delad åtkomst) som kan användas för att skapa en SQL Server autentiseringsuppgift på Azure SQL Edge. Skriptet skapar en signatur för delad åtkomst som är associerad med en lagrad åtkomst princip. Mer information finns i [signaturer för delad åtkomst, del 1: förstå SAS-modellen](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Skriptet skriver också det T-SQL-kommando som krävs för att skapa autentiseringsuppgiften på SQL Server. Skriptet nedan förutsätter att du redan har en Azure-prenumeration med ett lagrings konto och en lagrings behållare för säkerhets kopiorna.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    När skriptet har körts kopierar du kommandot Skapa AUTENTISERINGSUPPGIFT till ett Query-verktyg, ansluter till en instans av SQL Server och kör kommandot för att skapa autentiseringsuppgiften med signaturen för delad åtkomst.

2. Anslut till Azure SQL Edge-instansen med SQL Server Management Studio (SSMS) eller Använd Azure Data Studio (ADS) och skapa autentiseringsuppgiften med hjälp av kommandot från föregående steg. Se till att ersätta kommandot Skapa AUTENTISERINGSUPPGIFT med faktiska utdata från föregående steg.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Följande kommando tar en säkerhets kopia av *IronOreSilicaPrediction* till Azure Storage-behållaren.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Återställa en databas i Azure SQL Edge

Azure SQL Edge stöder återställning från både en lokal disk, en nätverks plats eller från ett Azure Blob Storage-konto. En översikt över återställning och återställning i SQL Server finns i [Översikt över återställning och återställning](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). En översikt över den enkla återställnings modellen i SQL Server finns i [fullständig databas återställning (enkel återställnings modell)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Återställ från lokal disk

I det här exemplet används säkerhets kopieringen *IronOreSilicaPrediction* som utfördes i föregående exempel för återställning som en ny databas med ett annat namn.

1. Om säkerhets kopierings filen för databasen inte redan finns i behållaren kan du använda kommandot nedan för att kopiera filen till behållaren. Exemplet nedan förutsätter att den säkerhetskopierade filen finns på den lokala värden och kopieras till mappen/var/opt/MSSQL/backup i en Azure SQL Edge-behållare med namnet SQL1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Anslut till Azure SQL Edge-instansen med SQL Server Management Studio (SSMS) eller Använd Azure Data Studio (ADS) för att köra kommandot Restore. I exemplet nedan återställs **IronOrePredictDB. bak** för att skapa en ny databas **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. När du har kört kommandot Restore och om återställningen lyckades visas meddelandena som liknar följande i fönstret utdata. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Återställ från URL

Azure SQL Edge stöder också återställning av en databas från ett Azure Storage-konto. Återställningar kan utföras från antingen block-blobbar eller sid-BLOB-säkerhetskopieringar. I exemplet nedan återställs säkerhets kopian av *IronOreSilicaPrediction_2020_04_16. bak* -databasen på en block-BLOB för att skapa databasen *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```



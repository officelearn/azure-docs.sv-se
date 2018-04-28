---
title: XEvent-händelsefilen koden för SQL-databas | Microsoft Docs
description: Tillhandahåller PowerShell och Transact-SQL för en tvåfasmigrering kodexempel som visar händelsefilen målet i en utökad händelse på Azure SQL Database. Azure Storage är en obligatorisk del av det här scenariot.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: c8a8561c6241968419c5014bf2a37cca05723e81
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Händelsekod filen mål för utökade händelser i SQL-databas

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill använda en fullständig kodexempel för ett stabilt sätt att avbilda och rapportera information om en utökad händelse.

I Microsoft SQL Server på [händelse målfil](http://msdn.microsoft.com/library/ff878115.aspx) används för att lagra händelsen utdata till en fil på hårddisken. Men dessa filer är inte tillgänglig för Azure SQL Database. I stället använder vi Azure Storage-tjänsten för att stödja målfil för händelsen.

Det här avsnittet presenteras ett kodexempel som två faser:

* PowerShell för att skapa en Azure Storage-behållare i molnet.
* Transact-SQL:
  
  * Tilldela Azure Storage-behållare till en målfil för händelsen.
  * Att skapa och starta händelsesessionen och så vidare.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Alla databaser som du kan skapa en tabell i.
  
  * Alternativt kan du [skapa en **AdventureWorksLT** demonstrationsdatabas](sql-database-get-started.md) i minuter.
* SQL Server Management Studio (ssms.exe), helst den senaste månatliga update-versionen. 
  Du kan hämta den senaste ssms.exe från:
  
  * Avsnittet [Hämta SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [En direktlänk till nedladdningen.](http://go.microsoft.com/fwlink/?linkid=616025)
* Du måste ha den [Azure PowerShell-moduler](http://go.microsoft.com/?linkid=9811175) installerad.
  
  * Moduler med kommandon som - **ny AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fas 1: PowerShell-koden för Azure Storage-behållare

Det här PowerShell är fas 1 av två faser kodexemplet.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare kör och är rerunnable.

1. Klistra in PowerShell-skriptet i en enkel textredigerare, till exempel Notepad.exe och spara skriptet som en fil med tillägget **.ps1**.
2. Starta PowerShell ISE som administratör.
3. I Kommandotolken skriver du:<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>och tryck sedan på RETUR.
4. I PowerShell ISE öppnar din **.ps1** fil. Kör skriptet.
5. Skriptet först startar ett nytt fönster där du loggar in på Azure.
   
   * Om du kör skriptet utan att störa sessionen du välja lämplig kommentera ut den **Add-AzureAccount** kommando.

![PowerShell ISE med Azure-modulen installerad, redo att köra skriptet.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-kod

Detta PowerShell-skript förutsätter att du redan har kört cmdleten Import-Module för modulen AzureRm. Referensdokumentation finns [PowerShell-modulen webbläsare](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzureRmAccount (or  Connect-AzureRmAccount), just one time.';
#Connect-AzureRmAccount;   # Same as  Connect-AzureRmAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'West US';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzureRmStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Anteckna några namngivna värden som PowerShell-skriptet ut när det avslutas. Du måste redigera dessa värden i Transact-SQL-skript som följer som fas 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fas 2: Transact-SQL-kod som använder Azure Storage-behållare

* Fas 1 av det här kodexemplet körde du ett PowerShell-skript för att skapa en Azure Storage-behållare.
* Därefter måste följande Transact-SQL-skript i fas 2 använda behållaren.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare kör och är rerunnable.

PowerShell-skriptet ut några namngivna värden när det avslutades. Du måste redigera Transact-SQL-skript för att använda dessa värden. Hitta **TODO** i Transact-SQL-skript för att hitta redigeringspunkter.

1. Öppna SQL Server Management Studio (ssms.exe).
2. Ansluta till din Azure SQL Database-databas.
3. Klicka för att öppna en ny fråga.
4. Klistra in följande Transact-SQL-skript i frågefönstret.
5. Hitta alla **TODO** i skriptet och göra lämpliga ändringar.
6. Spara och kör skriptet.


> [!WARNING]
> SAS-nyckelvärdet som genereras av föregående PowerShell-skriptet kan börja med en '?' (frågetecken). När du använder SAS-nyckel i följande T-SQL-skript, måste du *ta bort inledande '?'* . Annars kan ditt arbete blockeras av säkerhet.


### <a name="transact-sql-code"></a>Transact-SQL-kod

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Om det inte går att koppla när du kör mål, måste du stoppa och starta om händelsesessionen:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Resultat

När Transact-SQL-skriptet har slutförts klickar du på en cell under den **event_data_XML** kolumnrubriken. En **<event>** element visas som visar en UPDATE-instruktion.

Här är en **<event>** element som genererades under testning:


```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```


Föregående Transact-SQL-skript används följande funktion i operativsystemet för att läsa event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

En förklaring av avancerade alternativ för visning av data från utökade händelser finns på:

* [Avancerade visning av måldata från utökade händelser](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konvertera kodexemplet körs på SQL Server

Anta att du vill köra föregående Transact-SQL-exemplet på Microsoft SQL Server.

* För enkelhetens skull du vill ersätta helt användning av Azure Storage-behållare i med en enkel fil **C:\myeventdata.xel**. Filen skulle skrivas till den lokala hårddisken på den dator som är värd för SQL Server.
* Du behöver inte någon form av Transact-SQL-uttryck för **CREATE MASTER KEY** och **skapa AUTENTISERINGSUPPGIFTER**.
* I den **Skapa händelse SESSION** instruktionen i dess **lägga till MÅLET** -sats, ska du ersätta Http-värdet som tilldelas gjort **filename =** med en fullständig sökvägssträng som **C:\myfile.xel**.
  
  * Inga Azure Storage-konto behöver involveras.

## <a name="more-information"></a>Mer information

Mer information om konton och behållare i Azure Storage-tjänsten finns:

* [Använda Blob storage från .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Namnge och referera till behållare, blobar och metadata](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Arbeta med Root-behållaren](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lektionen 1: Skapa en princip för lagrade åtkomst och en signatur för delad åtkomst på en Azure-behållare](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lektionen 2: Skapa en SQL Server-autentiseringsuppgifter med hjälp av en signatur för delad åtkomst](http://msdn.microsoft.com/library/dn466435.aspx)
* [Utökade händelser för Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png


---
title: Konfigurera transaktionsreplikering mellan två hanterade instanser och SQL Server
description: En självstudiekurs som konfigurerar replikering mellan en Hanterad Publisher-instans, en hanterad distributörsinstans och en SQL Server-prenumerant på en Virtuell Azure-dator, tillsammans med nödvändiga nätverkskomponenter som privat DNS-zon och VPN-peering.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719899"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Självstudiekurs: Konfigurera transaktionsreplikering mellan två hanterade instanser och SQL Server


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Konfigurera en hanterad instans som en replikerings publisher. 
> - Konfigurera en hanterad instans som replikeringsdistributör. 
> - Konfigurera en SQL Server som prenumerant. 

![Replikering mellan en SQL MI-pub, SQL MI Dist och en SQL Server-under](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Den här självstudien är avsedd för en erfaren målgrupp och förutsätter att användaren är bekant med att distribuera och ansluta till både hanterade instanser och virtuella SQL Server-datorer i Azure. Som sådan är vissa steg i den här guiden slätas över. 

Mer information finns i [azure SQL Database-översikten ,](sql-database-managed-instance-index.yml)funktionerna och [sql-transaktionsreplikeringsartiklarna.](sql-database-managed-instance-transactional-replication.md) [capabilities](sql-database-managed-instance.md)

Information om hur du konfigurerar replikering mellan en hanterad instansutgivare och en hanterad instansprenumerant finns i [Konfigurera transaktionsreplikering mellan två hanterade instanser](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudien behöver du följande:

- En [Azure-prenumeration](https://azure.microsoft.com/free/). 
- Erfarenhet av att distribuera två hanterade instanser inom samma virtuella nätverk. 
- En SQL Server-prenumerant, antingen lokalt eller en Azure VM. Den här självstudien använder en virtuell Azure-dator.  
- [SQL Server Management Studio (SSMS) 18.0 eller senare](/sql/ssms/download-sql-server-management-studio-ssms).
- Den senaste versionen av [Azure Powershell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Port 445 och 1433 tillåter SQL-trafik på både Azure-brandväggen och Windows-brandväggen. 

## <a name="1---create-the-resource-group"></a>1 - Skapa resursgruppen
Använd följande PowerShell-kodavsnitt för att skapa en ny resursgrupp:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Skapa två hanterade instanser
Skapa två hanterade instanser i den här nya resursgruppen med Hjälp av [Azure-portalen](https://portal.azure.com). 

- Namnet på den hanterade utgivarinstansen bör vara: `sql-mi-publisher` (tillsammans med några tecken `vnet-sql-mi-publisher`för randomisering) och namnet på det virtuella nätverket ska vara .
- Namnet på den hanterade förekomsten `sql-mi-distributor` för distributören bör vara: (tillsammans med några tecken för randomisering) och det ska finnas _i samma virtuella nätverk som den hanterade utgivaren-instansen_.

   ![Använd utgivarens virtuella nätverk för distributören](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Mer information om hur du skapar en hanterad instans finns [i Skapa en hanterad instans i portalen](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Av hänsyn till enkelhet, och eftersom det är den vanligaste konfigurationen, föreslår den här guiden att placera distributören hanterad instans inom samma virtuella nätverk som utgivaren. Det är dock möjligt att skapa distributören i ett separat virtuellt nätverk. För att göra det måste du konfigurera VPN-peering mellan utgivarens och distributörens virtuella nätverk och sedan konfigurera VPN-peering mellan distributörens och abonnentens virtuella nätverk. 

## <a name="3---create-a-sql-server-vm"></a>3 - Skapa en VIRTUELL SQL Server
Skapa en virtuell SQL Server-dator med [Azure-portalen](https://portal.azure.com). Den virtuella SQL Server-datorn bör ha följande egenskaper:

- Namn:`sql-vm-sub`
- Bild: SQL Server 2016 eller senare
- Resursgrupp: samma som den hanterade instansen
- Virtuellt nätverk:`sql-vm-sub-vnet` 

Mer information om hur du distribuerar en virtuell SQL Server-dator till Azure finns i [Snabbstart: Skapa VIRTUELL SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 - Konfigurera VPN-peering
Konfigurera VPN-peering för att aktivera kommunikation mellan det virtuella nätverket för de två hanterade instanserna och det virtuella nätverket av SQL Server. Det gör du genom att använda det här PowerShell-kodavsnittet:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

När VPN-peering har upprättats testar du anslutningen genom att starta SQL Server Management Studio (SSMS) på SQL Server och ansluta till båda hanterade instanserna. Mer information om hur du ansluter till en hanterad instans med SSMS finns i [Använda SSMS för att ansluta till felindikatorn](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testa anslutningen till hanterade instanser](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Skapa privat DNS-zon

En privat DNS-zon tillåter DNS-routning mellan hanterade instanser och SQL Server. 

### <a name="create-private-dns-zone"></a>Skapa privat DNS-zon
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** för att skapa en ny Azure-resurs. 
1. Sök `private dns zone` efter på Azure Marketplace. 
1. Välj den **privata DNS-zonresurs** som publiceras av Microsoft och välj sedan **Skapa** för att skapa DNS-zonen. 
1. Välj prenumerations- och resursgruppen i listrutan. 
1. Ange ett godtyckligt namn för `repldns.com`DNS-zonen, till exempel . 

   ![Skapa privat DNS-zon](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Välj **Granska + skapa**. Granska parametrarna för din privata DNS-zon och välj sedan **Skapa** för att skapa din resurs. 

### <a name="create-a-record"></a>Skapa en post

1. Gå till din nya **privata DNS-zon** och välj **Översikt**. 
1. Välj **+ Postuppsättning** om du vill skapa en ny A-post. 
1. Ange namnet på den virtuella datorn för SQL Server samt den privata interna IP-adressen. 

   ![Konfigurera en post](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Välj **OK** om du vill skapa A-posten. 

### <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

1. Gå till din nya **privata DNS-zon** och välj **Virtuella nätverkslänkar**. 
1. Välj **+ Lägg till**. 
1. Ange ett namn för länken, till exempel `Pub-link`. 
1. Välj din prenumeration i listrutan och välj sedan det virtuella nätverket för den hanterade utgivarens instans. 
1. Markera rutan **bredvid Aktivera automatisk registrering**. 

   ![Skapa vnet-länk](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Välj **OK** om du vill länka det virtuella nätverket. 
1. Upprepa dessa steg för att lägga till en länk för `Sub-link`det virtuella nätverket för prenumeranten, med ett namn som . 


## <a name="6---create-azure-storage-account"></a>6 - Skapa Azure Storage-konto

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbetskatalogen och skapa sedan en [filresurs](../storage/files/storage-how-to-create-file-share.md) i lagringskontot. 

Kopiera sökvägen till filresursen i formatet:`\\storage-account-name.file.core.windows.net\file-share-name`   

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera anslutningssträngen för lagringsåtkomstnyckel i formatet:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7 - Skapa en databas
Skapa en ny databas på utgivar-MI. Det gör du på följande sätt:

1. Starta SQL Server Management Studio (SSMS) på SQL Server. 
1. Anslut till `sql-mi-publisher` den hanterade instansen. 
1. Öppna ett nytt **frågefönster** och kör följande T-SQL-fråga för att skapa databasen:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - Konfigurera distribution 
När anslutningen har upprättats och du har en exempeldatabas `sql-mi-distributor` kan du konfigurera distributionen på den hanterade instansen. Det gör du på följande sätt:

1. Starta SQL Server Management Studio (SSMS) på SQL Server. 
1. Anslut till `sql-mi-distributor` den hanterade instansen. 
1. Öppna ett nytt **frågefönster** och kör följande Transact-SQL-kod för att konfigurera distribution på den hanterade distributörens hanterade instans: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Var noga med att bara`\`använda omvänt @working_directory snedstreck ( ) för parametern. Om du använder`/`ett snedstreck ( ) kan det orsaka ett fel när du ansluter till filresursen. 

1. Anslut till `sql-mi-publisher` den hanterade instansen. 
1. Öppna ett nytt **frågefönster** och kör följande Transact-SQL-kod för att registrera distributören på utgivaren: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Skapa publikationen
När distributionen har konfigurerats kan du nu skapa publikationen. Det gör du på följande sätt: 

1. Starta SQL Server Management Studio (SSMS) på SQL Server. 
1. Anslut till `sql-mi-publisher` den hanterade instansen. 
1. Expandera noden **Replikering** i **Objektutforskaren**och högerklicka på mappen **Lokal publicering.** Välj **Ny publikation...**. 
1. Välj **Nästa** om du vill gå förbi välkomstsidan. 
1. Markera databasen som du `ReplTutorial` skapade tidigare på sidan **Publikationsdatabas.** Välj **Nästa**. 
1. På sidan **Publikationstyp** väljer du **Transaktionspublikation**. Välj **Nästa**. 
1. Markera kryssrutan bredvid **Tabeller**på sidan **Artiklar** . Välj **Nästa**. 
1. På sidan **Filtrera tabellrader** väljer du **Nästa** utan att lägga till några filter. 
1. På sidan **Snapshot Agent** markerar du kryssrutan **bredvid Skapa ögonblicksbild omedelbart och håller ögonblicksbilden tillgänglig för att initiera prenumerationer**. Välj **Nästa**. 
1. Välj **Säkerhetsinställningar på**sidan **Agentsäkerhet..** . Ange SQL Server-inloggningsuppgifter som ska användas för Snapshot-agenten och för att ansluta till Publisher. Välj **OK** om du vill stänga säkerhetssidan **för ögonblicksbildagenten.** Välj **Nästa**. 

   ![Konfigurera säkerhet för ögonblicksbildagent](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. På sidan **Guideåtgärder** väljer du att **Skapa publikationen** och (alternativt) välja att **generera en skriptfil med steg för att skapa publikationen** om du vill spara skriptet till senare. 
1. På sidan **Slutför guiden** namnger `ReplTest` du publikationen och väljer **Nästa** för att skapa publikationen. 
1. När publikationen har skapats uppdaterar du **noden Replikering** i **Objektutforskaren** och expanderar **Lokala publikationer** för att se den nya publikationen. 


## <a name="10---create-the-subscription"></a>10 - Skapa prenumerationen 

När publikationen har skapats kan du skapa prenumerationen. Det gör du på följande sätt: 

1. Starta SQL Server Management Studio (SSMS) på SQL Server. 
1. Anslut till `sql-mi-publisher` den hanterade instansen. 
1. Öppna ett nytt **frågefönster** och kör följande Transact-SQL-kod för att lägga till prenumerations- och distributionsagenten. Använd DNS som en del av prenumerantnamnet. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Testreplikering 

När replikeringen har konfigurerats kan du testa den genom att infoga nya objekt på utgivaren och titta på ändringarna som sprids till prenumeranten. 

Kör följande T-SQL-kodavsnitt om du vill visa raderna på prenumeranten:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Kör följande T-SQL-kodavsnitt för att infoga ytterligare rader på utgivaren och kontrollera sedan raderna igen på prenumeranten. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

1. Navigera till din resursgrupp i [Azure-portalen](https://portal.azure.com). 
1. Markera hanterade instanser och välj sedan **Ta bort**. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden, och tills den är klar kan du inte ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken Aktivitet för att bekräfta att den hanterade instansen har tagits bort. 
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resursgruppen och väljer sedan **Ta bort**. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. 
1. Ta bort eventuella återstående resurser. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. 
1. Ta bort resursgruppen genom att välja **Ta bort resursgrupp**, `myResourceGroup`skriva in namnet på resursgruppen och sedan välja **Ta bort**. 

## <a name="known-errors"></a>Kända fel

### <a name="windows-logins-are-not-supported"></a>Windows-inloggningar stöds inte

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agenten konfigurerades med en Windows-inloggning och måste använda en SQL Server-inloggning i stället. Använd sidan **Agent Security** i **egenskaperna Publikation** för att ändra inloggningsuppgifterna till en SQL Server-inloggning. 


### <a name="failed-to-connect-to-azure-storage"></a>Det gick inte att ansluta till Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Erhållen Azure Storage-anslutningssträng för återlagring 2019-11-19 02:21:05.07 Anslutning till Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Det gick inte att ansluta till Azure Storage '' med OS-fel: 53.


Detta beror sannolikt på att port 445 är stängd i antingen Azure-brandväggen, Windows-brandväggen eller båda. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Om du använder ett snedstreck i stället för omvänt snedstreck i filsökvägen för filresursen kan felet uppstå. Detta är `\\replstorage.file.core.windows.net\replshare` okej: Detta kan orsaka ett OS 55-fel:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Det gick inte att ansluta till Prenumeranten

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Möjliga lösningar:
- Se till att port 1433 är öppen. 
- Se till att TCP/IP är aktiverat på prenumeranten. 
- Bekräfta att DNS-namnet användes när du skapade prenumeranten. 
- Kontrollera att dina virtuella nätverk är korrekt länkade i den privata DNS-zonen. 
- Kontrollera att A-posten är korrekt konfigurerad. 
- Kontrollera att VPN-peering är korrekt konfigurerad. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Inga publikationer som du kan prenumerera på

När du lägger till en ny prenumeration med guiden **Ny prenumeration** kan det hända att det inte finns några databaser och publikationer som tillgängliga alternativ på sidan **Publikation:**

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Även om det är möjligt att det här felmeddelandet är korrekt, och det finns verkligen inte publikationer tillgängliga på utgivaren du anslöt till, eller om du saknar tillräcklig behörighet, kan det här felet också orsakas av en äldre version av SQL Server Management Studio. Prova att uppgradera till SQL Server Management Studio 18.0 eller senare för att utesluta detta som en grundorsak. 


## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Se följande artikel med säkerhetsfunktioner för [hanterade instansfunktioner](sql-database-managed-instance.md#azure-sql-database-security-features) för en omfattande lista över olika sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [Granskning av hanterade instanser](sql-database-managed-instance-auditing.md) 
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](sql-database-managed-instance-threat-detection.md) 
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) 
- [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Hanterade instansfunktioner

En fullständig översikt över funktionerna för hanterade instanser finns i:

> [!div class="nextstepaction"]
> [Funktioner för hanterade instanser](sql-database-managed-instance.md)

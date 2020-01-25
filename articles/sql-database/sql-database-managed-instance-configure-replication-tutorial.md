---
title: Konfigurera Transaktionsreplikering mellan två hanterade instanser och SQL Server
description: En själv studie kurs som konfigurerar replikering mellan en hanterad utgivar instans, en distributörs hanterad instans och en SQL Server prenumerant på en virtuell Azure-dator, tillsammans med nödvändiga nätverks komponenter som privat DNS-zon och VPN-peering.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719899"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Självstudie: Konfigurera Transaktionsreplikering mellan två hanterade instanser och SQL Server


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Konfigurera en hanterad instans som en replik utgivare. 
> - Konfigurera en hanterad instans som en replikerings-distributör. 
> - Konfigurera en SQL Server som prenumerant. 

![Replikering mellan en SQL MI pub, SQL MI dist och en SQL Server sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Den här självstudien är avsedd för en erfaren mål grupp och förutsätter att användaren är bekant med att distribuera och ansluta till både hanterade instanser och SQL Server virtuella datorer i Azure. Därför är vissa steg i den här självstudien glansiga. 

Läs mer i [Översikt över Azure SQL Database hanterade instanser](sql-database-managed-instance-index.yml), [funktioner](sql-database-managed-instance.md)och SQL- [transaktionella replikering](sql-database-managed-instance-transactional-replication.md) .

Information om hur du konfigurerar replikering mellan en hanterad instans utgivare och en hanterad instans prenumerant finns i [Konfigurera transaktionell replikering mellan två hanterade instanser](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudien behöver du följande:

- En [Azure-prenumeration](https://azure.microsoft.com/free/). 
- Erfarenhet av distribution av två hanterade instanser i samma virtuella nätverk. 
- En SQL Server prenumerant, antingen lokalt eller en virtuell Azure-dator. I den här självstudien används en virtuell Azure-dator.  
- [SQL Server Management Studio (SSMS) 18,0 eller senare](/sql/ssms/download-sql-server-management-studio-ssms).
- Den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Port 445 och 1433 tillåter SQL-trafik både på Azure-brandväggen och Windows-brandväggen. 

## <a name="1---create-the-resource-group"></a>1 – Skapa resurs gruppen
Använd följande PowerShell-kodfragment för att skapa en ny resurs grupp:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 – skapa två hanterade instanser
Skapa två hanterade instanser i den här nya resurs gruppen med hjälp av [Azure Portal](https://portal.azure.com). 

- Namnet på den hanterade instansen av utgivare ska vara: `sql-mi-publisher` (tillsammans med några tecken för slumpmässigheten) och namnet på det virtuella nätverket ska vara `vnet-sql-mi-publisher`.
- Namnet på den hanterade instansen av distributör ska vara: `sql-mi-distributor` (tillsammans med några tecken för slumpmässigheten) och bör finnas _i samma virtuella nätverk som utgivarens hanterade instans_.

   ![Använda Publisher VNet för distributören](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Mer information om hur du skapar en hanterad instans finns i [skapa en hanterad instans i portalen](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > För enkelhetens skull, och eftersom det är den vanligaste konfigurationen, föreslår den här självstudien placeringen av den hanterade instansen av distributör i samma virtuella nätverk som utgivaren. Det är dock möjligt att skapa distributören i ett separat virtuellt nätverk. För att göra det måste du konfigurera VPN-peering mellan virtuella nätverk för utgivaren och distributören och sedan konfigurera VPN-peering mellan distributörens virtuella nätverk och prenumerant. 

## <a name="3---create-a-sql-server-vm"></a>3 – skapa en SQL Server VM
Skapa en virtuell SQL Server virtuell dator med hjälp av [Azure Portal](https://portal.azure.com). Den SQL Server virtuella datorn måste ha följande egenskaper:

- Namn: `sql-vm-sub`
- Bild: SQL Server 2016 eller mer
- Resurs grupp: samma som den hanterade instansen
- Virtuellt nätverk: `sql-vm-sub-vnet` 

Mer information om hur du distribuerar en SQL Server VM till Azure finns i [snabb start: skapa SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 – Konfigurera VPN-peering
Konfigurera VPN-peering för att aktivera kommunikation mellan det virtuella nätverket för de två hanterade instanserna och det virtuella nätverket för SQL Server. Det gör du genom att använda det här PowerShell-kodfragmentet:

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

När VPN-peering har upprättats kan du testa anslutningen genom att starta SQL Server Management Studio (SSMS) på din SQL Server och ansluta till båda hanterade instanserna. Mer information om hur du ansluter till en hanterad instans med hjälp av SSMS finns i [använda SSMS för att ansluta till mi](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testa anslutningen till de hanterade instanserna](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 – skapa en privat DNS-zon

En privat DNS-zon tillåter DNS-routning mellan de hanterade instanserna och SQL Server. 

### <a name="create-private-dns-zone"></a>Skapa en privat DNS-zon
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **skapa en resurs** för att skapa en ny Azure-resurs. 
1. Sök efter `private dns zone` på Azure Marketplace. 
1. Välj den **privat DNS zon** resurs som publicerats av Microsoft och välj sedan **skapa** för att skapa DNS-zonen. 
1. Välj prenumerationen och resurs gruppen i list rutan. 
1. Ange ett godtyckligt namn för DNS-zonen, till exempel `repldns.com`. 

   ![Skapa en privat DNS-zon](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Välj **Granska + skapa**. Granska parametrarna för din privata DNS-zon och välj **skapa** för att skapa din resurs. 

### <a name="create-a-record"></a>Skapa en post

1. Gå till din nya **privat DNS zon** och välj **Översikt**. 
1. Välj **+ post uppsättning** för att skapa en ny a-post. 
1. Ange namnet på din SQL Server VM samt den privata interna IP-adressen. 

   ![Konfigurera en post](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Välj **OK** för att skapa en post. 

### <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

1. Gå till din nya **privat DNS zon** och välj **länkar till virtuella nätverk**. 
1. Välj **+ Lägg till**. 
1. Ange ett namn för länken, till exempel `Pub-link`. 
1. Välj din prenumeration i list rutan och välj sedan det virtuella nätverket för utgivarens hanterade instans. 
1. Markera kryss rutan bredvid **Aktivera automatisk registrering**. 

   ![Skapa VNet-länk](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Välj **OK** för att länka ditt virtuella nätverk. 
1. Upprepa de här stegen för att lägga till en länk för det virtuella nätverket för prenumerant, med ett namn som `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6 – Skapa Azure Storage konto

[Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) för arbets katalogen och skapa sedan en [fil resurs](../storage/files/storage-how-to-create-file-share.md) i lagrings kontot. 

Kopiera sökvägen till fil resursen i formatet: `\\storage-account-name.file.core.windows.net\file-share-name`   

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera anslutnings strängen för lagrings åtkomst nyckeln i formatet: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7 – skapa en databas
Skapa en ny databas på utgivar MI. Det gör du genom att följa dessa steg:

1. Starta SQL Server Management Studio (SSMS) på din SQL Server. 
1. Anslut till den `sql-mi-publisher` hanterade instansen. 
1. Öppna ett **nytt frågefönster** och kör följande T-SQL-fråga för att skapa databasen:

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

## <a name="8---configure-distribution"></a>8 – konfigurera distribution 
När anslutningen har upprättats och du har en exempel databas kan du konfigurera distribution på din `sql-mi-distributor` hanterade instans. Det gör du genom att följa dessa steg:

1. Starta SQL Server Management Studio (SSMS) på din SQL Server. 
1. Anslut till den `sql-mi-distributor` hanterade instansen. 
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att konfigurera distributionen på den hanterade distributörs instansen: 

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
   > Se till att endast använda omvänt snedstreck (`\`) för @working_directory-parametern. Om du använder ett snedstreck (`/`) kan det orsaka ett fel när du ansluter till fil resursen. 

1. Anslut till den `sql-mi-publisher` hanterade instansen. 
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att registrera distributören på utgivaren: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 – skapa publikationen
När distributionen har kon figurer ATS kan du nu skapa publikationen. Det gör du genom att följa dessa steg: 

1. Starta SQL Server Management Studio (SSMS) på din SQL Server. 
1. Anslut till den `sql-mi-publisher` hanterade instansen. 
1. I **Object Explorer**expanderar du noden **replikering** och högerklickar på mappen för den **lokala publikationen** . Välj **ny publikation...** . 
1. Välj **Nästa** för att gå förbi Välkomst sidan. 
1. På sidan **publicerings databas** väljer du `ReplTutorial` databas som du skapade tidigare. Välj **Nästa**. 
1. På sidan **publikationstyp** väljer du **transaktionell publikation**. Välj **Nästa**. 
1. Markera kryss rutan bredvid **tabeller**på sidan **artiklar** . Välj **Nästa**. 
1. På sidan **filtrera tabell rader** väljer du **Nästa** utan att lägga till några filter. 
1. På sidan **ögonblicks bild agent** markerar du kryss rutan bredvid **skapa ögonblicks bild direkt och håller ögonblicks bilden tillgänglig för att initiera prenumerationer**. Välj **Nästa**. 
1. På sidan **agent säkerhet** väljer du **säkerhets inställningar..** . Ange SQL Server inloggnings uppgifter som ska användas för ögonblicks bild agenten och för att ansluta till utgivaren. Välj **OK** för att stänga **säkerhets sidan för ögonblicks bild agent** . Välj **Nästa**. 

   ![Konfigurera säkerhet för ögonblicks bild agent](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. På sidan **åtgärder i guiden** väljer du att **skapa publikationen** och (valfritt) Välj för att **Generera en skript fil med steg för att skapa publikationen** om du vill spara skriptet till senare. 
1. På sidan **Slutför guiden** namnger du publikationen `ReplTest` och väljer **Nästa** för att skapa publikationen. 
1. När publikationen har skapats uppdaterar du noden **replikering** i **Object Explorer** och expanderar **lokala publikationer** för att se din nya publikation. 


## <a name="10---create-the-subscription"></a>10 – skapa prenumerationen 

När publikationen har skapats kan du skapa prenumerationen. Det gör du genom att följa dessa steg: 

1. Starta SQL Server Management Studio (SSMS) på din SQL Server. 
1. Anslut till den `sql-mi-publisher` hanterade instansen. 
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att lägga till prenumerations-och distributions agenten. Använd DNS som en del av namnet på prenumeranten. 

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

## <a name="11---test-replication"></a>11-testa replikering 

När replikeringen har kon figurer ATS kan du testa den genom att infoga nya objekt i utgivaren och titta på ändringarna Sprid till prenumeranten. 

Kör följande T-SQL-kodfragment för att visa raderna på prenumeranten:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Kör följande T-SQL-kodfragment för att infoga ytterligare rader i utgivaren och kontrol lera sedan raderna igen på prenumeranten. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com). 
1. Välj de hanterade instanserna och välj sedan **ta bort**. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden och tills den är klar kan du inte ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken aktivitet för att bekräfta att den hanterade instansen har tagits bort. 
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resurs gruppen och sedan välja **ta bort**. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort eventuella återstående resurser. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort resurs gruppen genom att välja **ta bort resurs**grupp, Skriv namnet på resurs gruppen `myResourceGroup`och välj sedan **ta bort**. 

## <a name="known-errors"></a>Kända fel

### <a name="windows-logins-are-not-supported"></a>Windows-inloggningar stöds inte

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agenten konfigurerades med en Windows-inloggning och måste använda en SQL Server inloggning i stället. Använd sidan **agent säkerhet** i **publicerings egenskaperna** för att ändra inloggnings uppgifterna till en SQL Server inloggning. 


### <a name="failed-to-connect-to-azure-storage"></a>Det gick inte att ansluta till Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 erhöll Azure Storage anslutnings sträng för replstorage 2019-11-19 02:21:05.07 ansluter till Azure Files Storage\\replstorage. File. Core. Windows. net\replshare "2019-11-19 02:21:31.21 kunde inte ansluta till Azure Storage" med OS-fel: 53.


Detta beror förmodligen på att port 445 är stängd i antingen Azure-brandväggen, Windows-brandväggen eller både och. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Om du använder ett snedstreck i stället för omvänt snedstreck i fil resursens fil Sök väg kan detta fel uppstå. Detta är OK: `\\replstorage.file.core.windows.net\replshare` detta kan orsaka ett OS 55-fel: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Det gick inte att ansluta till prenumeranten

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Möjliga lösningar:
- Se till att port 1433 är öppen. 
- Se till att TCP/IP är aktiverat på prenumeranten. 
- Bekräfta att DNS-namnet användes när prenumeranten skapades. 
- Kontrol lera att de virtuella nätverken är korrekt länkade i den privata DNS-zonen. 
- Kontrol lera att A-posten är korrekt konfigurerad. 
- Kontrol lera att din VPN-peering är korrekt konfigurerad. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Det går inte att prenumerera på några publikationer

När du lägger till en ny prenumeration med hjälp av guiden **ny prenumeration** kan du se att det inte finns några databaser och publikationer som är tillgängliga på sidan **publikation** , och du kan se följande fel meddelande:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Även om det är möjligt att det här fel meddelandet är korrekt och det inte finns publikationer som är tillgängliga på utgivaren som du har anslutit till, eller om du saknar tillräckliga behörigheter, kan det här felet också orsakas av en äldre version av SQL Server Management Studio. Försök att uppgradera till SQL Server Management Studio 18,0 eller mer för att utesluta detta som en rotorsak. 


## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

I artikeln om [säkerhetsfunktioner för hanterade instanser](sql-database-managed-instance.md#azure-sql-database-security-features) finns en omfattande lista med sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [Granskning av hanterad instans](sql-database-managed-instance-auditing.md) 
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](sql-database-managed-instance-threat-detection.md) 
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) 
- [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Hanterade instansfunktioner

En fullständig översikt över funktionerna för hanterade instanser finns i:

> [!div class="nextstepaction"]
> [Hanterade instansfunktioner](sql-database-managed-instance.md)

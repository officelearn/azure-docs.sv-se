---
title: 'Azure PowerShell: Skapa en SQL-databas | Microsoft Docs'
description: "Lär dig att skapa en logisk SQL Database-server, brandväggsregel på servernivå och databaser i Azure Portal."
keywords: "sql database-självstudier, skapa en sql-databas"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7f75b57c5d409ad9c4c79c48e4b7ee0021e7846b
ms.lasthandoff: 04/12/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Skapa en enskild Azure SQL-databas med PowerShell

PowerShell används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här handboken får du information om hur du använder PowerShell för att distribuera en Azure SQL-databas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en [logisk Azure SQL Database-server](sql-database-features.md).

Kontrollera att du har installerat den senaste [Azure PowerShell](/powershell/azureps-cmdlets-docs)-versionen för att kunna slutföra den här kursen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) och följ anvisningarna på skärmen.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en logisk [Azure SQL Database-server](sql-database-features.md) med kommandot [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en ny brandväggsregel på [Azure SQL Database-servernivå](sql-database-firewall-configure.md) med kommandot [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). En brandväggsregel på servernivå tillåter att ett externt program, t.ex. SQL Server Management Studio eller SQLCMD-verktyget, ansluter till en SQL-databas visa SQL Database-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-blank-database"></a>Skapa en tom databas

Skapa en tom SQL-databas med en [S0-prestandanivå](sql-database-service-tiers.md) i servern med kommandot [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). I följande exempel skapas en databas som heter `mySampleDatabase`. Ersätt detta fördefinierade värde som du vill.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

- Om du vill ansluta och fråga med SQL Server Management Studio kan du läsa [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- Mer information om att ansluta och ställa frågor med Visual Studio Code finns i [Ansluta och fråga med Visual Studio Code](sql-database-connect-query-vscode.md).
- Mer information om att ansluta och ställa frågor med .NET finns i [Ansluta och fråga med .NET](sql-database-connect-query-dotnet.md).
- Mer information om att ansluta och ställa frågor med PHP finns i [Ansluta och fråga med PHP](sql-database-connect-query-php.md).
- Mer information om att ansluta och ställa frågor med Node.js finns i [Ansluta och fråga med Node.js](sql-database-connect-query-nodejs.md).
- Mer information om att ansluta och ställa frågor med Java finns i [Ansluta och fråga med Java](sql-database-connect-query-java.md).
- Mer information om att ansluta och ställa frågor med Python finns i [Ansluta och fråga med Python](sql-database-connect-query-python.md).
- Mer information om att ansluta och ställa frågor med Ruby finns i [Ansluta och fråga med Ruby](sql-database-connect-query-ruby.md).

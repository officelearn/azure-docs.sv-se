---
title: 'Azure CLI: Skapa en SQL-databas | Microsoft Docs'
description: "Lär dig hur du skapar en logisk SQL Database-server, brandväggsregel på servernivå och databaser med hjälp av Azure CLI."
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
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 06b6830b28745b0f6574d7bca5cca7907db8ecb1
ms.lasthandoff: 04/18/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Skapa en enskild Azure SQL-databas med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här handboken får du information om hur du använder Azure CLI för att distribuera en Azure SQL-databas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en [logisk Azure SQL Database-server](sql-database-features.md).

Kontrollera att du har installerat den senaste versionen av [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) för att slutföra den här snabbstarten. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="define-variables"></a>Definiera variabler

Definiera variabler för användning i skripten i den här snabbstartsguiden.

```azurecli
# The data center and resource name for your resources
resourcegroupname = myResourceGroup
location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
servername = server-$RANDOM
# Set an admin login and password for your database
adminlogin = ServerAdmin
password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
startip = "0.0.0.0"
endip = "0.0.0.1"
# The database name
databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```azurecli
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logisk Azure SQL Database-server](sql-database-features.md) med kommandot [az sql server create](/cli/azure/sql/server#create). En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```azurecli
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [Azure SQL Database-brandväggsregel på servernivå](sql-database-firewall-configure.md) med kommandot [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). En brandväggsregel på servernivå tillåter att ett externt program, t.ex. SQL Server Management Studio eller SQLCMD-verktyget, ansluter till en SQL-databas visa SQL Database-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.  

```azurecli
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Skapa en databas på servern med exempeldata

Skapa en databas med en [S0-prestandanivå](sql-database-service-tiers.md) på servern med kommandot [az sql db create](/cli/azure/sql/db#create). Följande exempel skapar en databas med namnet `mySampleDatabase` och läser in AdventureWorksLT-exempeldata till den här databasen. Ersätt de fördefinierade värdena efter behov (andra snabbstarter i den här samlingen bygger på värdena i den här snabbstarten).

```azurecli
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

```azurecli
az group delete --name $resourcegroupname
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


---
title: 'Azure CLI: Skapa en SQL-databas | Microsoft Docs'
description: Lär dig hur du skapar en logisk SQL Database-server, brandväggsregel på servernivå och databaser med hjälp av Azure CLI.
keywords: sql database-självstudier, skapa en sql-databas
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: carlrab
ms.openlocfilehash: 509cff23609896a019c110d8046935dfbce793f2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Skapa en enskild Azure SQL-databas med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här handboken får du information om hur du använder Azure CLI för att distribuera en Azure SQL-databas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en [logisk Azure SQL Database-server](sql-database-features.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="define-variables"></a>Definiera variabler

Definiera variabler för användning i skripten i den här snabbstartsguiden.

```azurecli-interactive
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.0"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```azurecli-interactive
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logisk Azure SQL Database-server](sql-database-features.md) med kommandot [az sql server create](/cli/azure/sql/server#az_sql_server_create). En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```azurecli-interactive
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [Azure SQL Database-brandväggsregel på servernivå](sql-database-firewall-configure.md) med kommandot [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). En brandväggsregel på servernivå tillåter att ett externt program, t.ex. SQL Server Management Studio eller SQLCMD-verktyget, ansluter till en SQL-databas visa SQL Database-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.  

```azurecli-interactive
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Skapa en databas på servern med exempeldata

Skapa en databas med en [S0-prestandanivå](sql-database-service-tiers.md) på servern med kommandot [az sql db create](/cli/azure/sql/db#az_sql_db_create). Följande exempel skapar en databas med namnet `mySampleDatabase` och läser in AdventureWorksLT-exempeldata till den här databasen. Ersätt de fördefinierade värdena efter behov (andra snabbstarter i den här samlingen bygger på värdena i den här snabbstarten).

```azurecli-interactive
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure Portal.
>

```azurecli-interactive
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

- Nu när du har en databas kan du [ansluta och fråga](sql-database-connect-query.md) med dina favoritverktyg och språk. 
- I följande självstudier lär du dig hur du utformar din första databas, skapar tabeller och infogar data:
 - [Utforma din första Azure SQL-databas med SSMS](sql-database-design-first-database.md)
  - [Utforma en Azure SQL databas och ansluta med C# och ADO.NET](sql-database-design-first-database-csharp.md)



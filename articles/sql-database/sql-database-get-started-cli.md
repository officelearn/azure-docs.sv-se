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
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3d642447526c5562ae3bfad5e4a4592e33e766aa
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Skapa en enskild Azure SQL-databas med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här handboken får du information om hur du använder Azure CLI för att distribuera en Azure SQL-databas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en [logisk Azure SQL Database-server](sql-database-features.md).

Kontrollera att Azure CLI har installerats innan du börjar. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logisk Azure SQL Database-server](sql-database-features.md) med kommandot [az sql server create](/cli/azure/sql/server#create). En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [Azure SQL Database-brandväggsregel på servernivå](sql-database-firewall-configure.md) med kommandot [az sql server firewall create](/cli/azure/sql/server/firewall#create). En brandväggsregel på servernivå tillåter att ett externt program, t.ex. SQL Server Management Studio eller SQLCMD-verktyget, ansluter till en SQL-databas visa SQL Database-tjänstens brandvägg. I följande exempel skapas en brandväggsregel för ett fördefinierat adressintervall som, i det här exemplet, är hela det möjliga intervallet med IP-adresser. Ersätt dessa fördefinierade värden med värden för den externa IP-adressen eller IP-adressintervallet. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server-with-sample-data"></a>Skapa en databas på servern med exempeldata

Skapa en databas med en [S0-prestandanivå](sql-database-service-tiers.md) på servern med kommandot [az sql db create](/cli/azure/sql/db#create). Följande exempel skapar en databas med namnet `mySampleDatabase` och läser in AdventureWorksLT-exempeldata till den här databasen. Ersätt de fördefinierade värdena efter behov (andra snabbstarter i den här samlingen bygger på värdena i den här snabbstarten).

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Om du vill ansluta och fråga med SQL Server Management Studio kan du läsa [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- Om du vill ansluta med Visual Studio kan du läsa [Ansluta och fråga med Visual Studio](sql-database-connect-query.md).
- En teknisk översikt över SQL Database finns i [Om SQL Database-tjänsten](sql-database-technical-overview.md).


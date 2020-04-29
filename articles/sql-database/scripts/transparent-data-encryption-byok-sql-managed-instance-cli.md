---
title: CLI-exempel – aktivera BYOK TDE-Azure SQL Database Hanterad instans
description: Lär dig hur du konfigurerar en Azure SQL-hanterad instans att börja använda BYOK transparent datakryptering (TDE) för kryptering vid vila med hjälp av PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061735"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Hantera transparent datakryptering i en hanterad instans med din egen nyckel från Azure Key Vault

Detta exempel på Azure CLI-skript konfigurerar transparent datakryptering (TDE) med kundhanterad nyckel för Azure SQL-hanterad instans med hjälp av en nyckel från Azure Key Vault. Detta kallas ofta för ett Bring Your Own Key scenario för TDE. Mer information om TDE med kundhanterad nyckel finns i [TDE Bring Your Own Key till Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="prerequisites"></a>Krav

En befintlig hanterad instans finns i [använda Azure CLI för att skapa en Azure SQL Database Hanterad instans](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Databas kommandon. |
| [AZ SQL-redundans – grupp](/cli/azure/sql/failover-group) | Kommandon för redundans grupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).

---
title: 'Azure CLI-skript: Skala en Azure Database for MySQL-server'
description: "Det här CLI-exempelskriptet skalar Azure Database for MySQL-servern till en annan prestandanivå när du har kört frågor mot måtten."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 1b2bba200ea98d7499c1dbf99d2db6552c314d19
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Övervaka och skala en Azure Database for MySQL-server med Azure CLI
Det här CLI-exempelskriptet skalar en enda Azure Database for MySQL-servern till en annan prestandanivå när du har kört frågor mot måtten.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. Se [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli) för att installera eller uppgradera din version av Azure CLI. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID. [!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=18-19 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Skapar en MySQL-server som är värd för databaserna. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Mer information om skalning finns i [Tjänstnivåer](../concepts-service-tiers.md) och [Beräkningsenheter och lagringsenheter](../concepts-compute-unit-and-storage.md).

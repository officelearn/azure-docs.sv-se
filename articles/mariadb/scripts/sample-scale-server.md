---
title: Azure CLI-skript – Skala en Azure Database for MariaDB-server
description: Det här CLI-exempelskriptet skalar Azure Database for MariaDB-servern till en annan prestandanivå efter frågekörning mot måtten.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: e5f6364992987e5aeb6cb1ccd2055823efae607e
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585254"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Övervaka och skala en Azure Database for MariaDB-server med Azure CLI
Det här CLI-exempelskriptet skalar en enskild Azure Database for MariaDB-server till en annan prestandanivå efter frågekörning mot måtten.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID. [!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Skapar en MariaDB-server som är värd för databaserna. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Mer information om skalning finns i [Pricing Tiers](../concepts-pricing-tiers.md.

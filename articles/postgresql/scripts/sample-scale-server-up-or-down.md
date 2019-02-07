---
title: Azure CLI-skript – Skala Azure Database for PostgreSQL
description: Azure CLI-skriptexempel – Skala Azure Database for PostgreSQL-servern till en annan prestandanivå när du har kört frågor mot måtten.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 65958eb1e2d7cc4492e0437b24db77f00ebc7eba
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728341"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Övervaka och skala en enskild PostgreSQL-server med Azure CLI
Det här CLI-exempelskriptet skalar en enda Azure Database for PostgreSQL-servern till en annan prestandanivå när du har kört frågor mot måtten. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure)
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Läs mer om skalning: [Tjänstnivåer](../concepts-service-tiers.md) samt [beräkningsenheter och lagringsenheter](../concepts-compute-unit-and-storage.md)

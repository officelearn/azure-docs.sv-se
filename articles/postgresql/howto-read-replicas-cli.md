---
title: Hantera skrivskyddade repliker för Azure Database för PostgreSQL – enskild Server från Azure CLI
description: Lär dig hur du hanterar skrivskyddade repliker i Azure Database för PostgreSQL – enskild Server från Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9730faf3191ef2e2bd0b6c3caddefa0492b33fc5
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510243"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Skapa och hantera skrivskyddade repliker från Azure CLI

I den här artikeln får du lära dig hur du skapar och hanterar skrivskyddade repliker i Azure Database for PostgreSQL med Azure CLI. Läs mer om skrivskyddade repliker i den [översikt](concepts-read-replicas.md).

> [!NOTE]
> Azure CLI stöder ännu inte skapa repliker i en annan region än huvudservern. Du kan skapa en replik över flera regioner med den [Azure-portalen](howto-read-replicas-portal.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
- En [Azure Database for PostgreSQL-server](quickstart-create-server-up-azure-cli.md) vara huvudservern.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Förbereda huvudservern
De här stegen måste användas för att förbereda en huvudserver på generell användning och den Minnesoptimerade nivån.

Den `azure.replication_support` parametern måste anges till **REPLIKEN** på huvudservern. När den här statiska parametern ändras, krävs en omstart av servern för att ändringen ska börja gälla.

1. Ange `azure.replication_support` till REPLIKEN.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Starta om för att tillämpa ändringen för servern.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Den [az postgres serverreplik skapa](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Den resursgrupp där replikservern ska skapas.  |
| namn | mydemoserver-replica | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namn eller resurs-ID för befintliga huvudservern att replikera från. |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Om du inte angett det `azure.replication_support` parameter **REPLIKEN** på en generell användning eller Minnesoptimerade huvudservern och starta om servern, du får ett felmeddelande. Slutföra de här två stegen innan du skapar en replik.

En replik skapas med hjälp av samma serverkonfiguration som huvudserver. När en replik skapas flera inställningar kan ändras oberoende från huvudservern: compute-generering, vCores, lagring och säkerhetskopiering kvarhållningsperiod. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvudserver-konfiguration har uppdaterats till nya värden, uppdatera konfigurationen för repliken till samma eller högre värden. Den här åtgärden säkerställer att replikeringen kan hålla jämna steg med ändringar som görs till huvudservern.

## <a name="list-replicas"></a>Lista repliker
Du kan visa listan över replikeringar av en huvudserver med hjälp av [az postgres server replikeringslistan](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) kommando.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikering mellan en huvudserver och en skrivskyddad replik med hjälp av [az postgres server replica sluta](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) kommando.

När du har slutat replikering till en huvudserver och en skrivskyddad replik, kan inte den ångras. Läs replik blir en fristående server som har stöd för både läsningar och skrivningar. Den fristående servern kan inte göras i en replik igen.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Ta bort en master-databasen eller repliken server
Om du vill ta bort en master-databasen eller repliken server måste du använda den [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) kommando.

När du tar bort en huvudserver har replikering till alla skrivskyddade repliker stoppats. Skrivskyddade repliker bli fristående servrar som stöder nu både läsningar och skrivningar.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [läsa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).
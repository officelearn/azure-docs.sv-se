---
title: Hantera Läs repliker för Azure Database for PostgreSQL-enskild server från Azure CLI
description: Lär dig hur du hanterar Läs repliker i Azure Database for PostgreSQL-enskild server från Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907433"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Skapa och hantera Läs repliker från Azure CLI

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL från Azure CLI. Mer information om Läs repliker finns i översikten [](concepts-read-replicas.md).

> [!IMPORTANT]
> Du kan skapa en Läs replik i samma region som din huvud server eller i valfri annan Azure-region som du väljer. Replikering mellan regioner är för närvarande en offentlig för hands version.

## <a name="prerequisites"></a>Förutsättningar
- En [Azure Database for postgresql-server](quickstart-create-server-up-azure-cli.md) som ska vara huvud servern.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Förbered huvud servern
De här stegen måste användas för att förbereda en huvud server i Generell användning-eller minnesoptimerade nivåer.

Parametern måste anges till replik på huvud servern. `azure.replication_support` När den här statiska parametern ändras krävs en omstart av servern för att ändringen ska börja gälla.

1. Ställ `azure.replication_support` in på replik.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Starta om för att tillämpa ändringen på servern.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Kommandot [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Resurs gruppen där replik servern kommer att skapas.  |
| name | mydemoserver-replica | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namnet eller resurs-ID: t för den befintliga huvud server som ska replikeras från. |

I CLI-exemplet nedan skapas repliken i samma region som huvud servern.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Använd parametern om du vill skapa en skrivskyddad replik `--location` av en kors region. CLI-exemplet nedan skapar repliken i USA, västra.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

Om du inte har angett `azure.replication_support` parametern till **replik** på en generell användning eller en minnesoptimerade huvud server och startat om servern, visas ett fel meddelande. Utför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generation, virtuella kärnor, lagring och säkerhets kopierings perioden. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvud server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

## <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en huvud server genom att använda kommandot [AZ postgres Server Replica List](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en huvud server och en Läs replik med hjälp av kommandot [AZ postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

När du har stoppat replikering till en huvud server och en Läs replik kan du inte göra det. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud-eller replik Server
Om du vill ta bort en huvud-eller replik Server använder du kommandot [AZ postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

När du tar bort en huvud server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om att [läsa repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure Portal](howto-read-replicas-portal.md).
---
title: Hantera Läs repliker – Azure CLI, REST API – Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MySQL med hjälp av Azure CLI eller REST API.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41c12dcb2e4c5e3b280bb349e81bcb58a0bafd01
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495769"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Skapa och hantera Läs repliker i Azure Database for MySQL med hjälp av Azure CLI och REST API

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MySQL-tjänsten med hjälp av Azure CLI och REST API. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera Läs repliker med hjälp av Azure CLI.

### <a name="prerequisites"></a>Förutsättningar

- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som huvud server. 

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MySQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att huvud servern är i någon av dessa pris nivåer.

### <a name="create-a-read-replica"></a>Skapa en Läs replik

> [!IMPORTANT]
> När du skapar en replik för en huvud server som inte har några befintliga repliker, startar originalet om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande kommando:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create`Kommandot kräver följande parametrar:

| Inställningen | Exempelvärde | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resurs gruppen där replik servern ska skapas.  |
| name | mydemoreplicaserver | Namnet på den nya replik servern som skapas. |
| source-server | mydemoserver | Namnet eller ID: t för den befintliga huvud server som ska replikeras från. |

Använd parametern om du vill skapa en skrivskyddad replik av en kors region `--location` . CLI-exemplet nedan skapar repliken i USA, västra.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som huvud servern. Replik Server konfigurationen kan ändras efter att den har skapats. Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större än huvud värden, för att repliken ska kunna fortsätta med huvud servern.


### <a name="list-replicas-for-a-master-server"></a>Lista repliker för en huvud server

Om du vill visa alla repliker för en specifik huvud server kör du följande kommando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list`Kommandot kräver följande parametrar:

| Inställningen | Exempelvärde | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resurs gruppen där replik servern ska skapas.  |
| server-name | mydemoserver | Namn eller ID för huvud servern. |

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en huvud server och en replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Replikering till en Läs replik Server kan stoppas med hjälp av följande kommando:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop`Kommandot kräver följande parametrar:

| Inställningen | Exempelvärde | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resurs gruppen där replik servern finns.  |
| name | mydemoreplicaserver | Namnet på replik servern där replikeringen ska stoppas. |

### <a name="delete-a-replica-server"></a>Ta bort en replik Server

Du kan ta bort en Läs replik Server genom att köra kommandot **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Ta bort en huvud server

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en huvud server kan du köra kommandot **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST-API
Du kan skapa och hantera Läs repliker med hjälp av [Azure REST API](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Skapa en Läs replik
Du kan skapa en Läs replik med hjälp av [create API](/rest/api/mysql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

Om du inte har angett `azure.replication_support` parametern till **replik** på en generell användning eller en minnesoptimerade huvud server och startat om servern, visas ett fel meddelande. Utför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generation, virtuella kärnor, lagring och säkerhets kopierings perioden. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.


> [!IMPORTANT]
> Innan en huvud server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en huvud server med hjälp av [replik listans API](/rest/api/mysql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server
Du kan stoppa replikeringen mellan en huvud server och en Läs replik med hjälp av [uppdaterings-API: et](/rest/api/mysql/servers/update).

När du har stoppat replikering till en huvud server och en Läs replik kan du inte göra det. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud-eller replik Server
Om du vill ta bort en huvud server eller replik Server använder du [borttagnings-API: et](/rest/api/mysql/servers/delete):

När du tar bort en huvud server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)

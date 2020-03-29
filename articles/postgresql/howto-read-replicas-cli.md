---
title: Hantera läsande repliker - Azure CLI, REST API - Azure Database for PostgreSQL - Single Server
description: Lär dig hur du hanterar lästa repliker i Azure Database for PostgreSQL - Single Server från Azure CLI och REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774808"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Skapa och hantera läsande repliker från Azure CLI, REST API

I den här artikeln får du lära dig hur du skapar och hanterar läsande repliker i Azure Database för PostgreSQL med hjälp av Azure CLI och REST API. Mer information om läsrepliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera läsande repliker med Hjälp av Azure CLI.

### <a name="prerequisites"></a>Krav

- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-up-azure-cli.md) som huvudserver.


### <a name="prepare-the-master-server"></a>Förbereda huvudservern
Dessa steg måste användas för att förbereda en huvudserver på nivåerna Allmänt ändamål eller Minnesoptimerade.

Parametern `azure.replication_support` måste ställas in på **REPLIK** på huvudservern. När den här statiska parametern ändras krävs en omstart av servern för att ändringen ska börja gälla.

1. Ställ `azure.replication_support` in på REPLIK.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Om du får felet "Ogiltigt värde givet" när du försöker ange azure.replication_support från Azure CLI, är det troligt att din server redan har REPLIK som standard. Ett fel förhindrar att den här inställningen återspeglas korrekt på nyare servrar där REPLIK är den interna standardinställningen. <br><br>
> Du kan hoppa över huvudstegen och gå för att skapa repliken. <br><br>
> Om du vill bekräfta att servern finns i den här kategorin besöker du serverns replikeringssida i Azure-portalen. "Inaktivera replikering" kommer att vara nedtonad och "Lägg till replik" kommer att vara aktiv i verktygsfältet.

2. Starta om servern för att tillämpa ändringen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Skapa en läsreplik

Kommandot [az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Resursgruppen där replikservern ska skapas.  |
| namn | mydemoserver-replik | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namnet eller resurs-ID:t för den befintliga huvudservern som ska replikeras från. |

I CLI-exemplet nedan skapas repliken i samma region som huvuduppsättningen.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Om du vill skapa en `--location` läsreplik för korsområde använder du parametern. CLI-exemplet nedan skapar repliken i västra USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Mer information om vilka regioner du kan skapa en replik i finns i [artikeln läsreplikbegrepp](concepts-read-replicas.md). 

Om du inte har `azure.replication_support` angett parametern till **REPLIK** på en general purpose- eller minnesoptimerad huvudserver och startat om servern visas ett felmeddelande. Slutför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings- och lagringsinställningar som huvudsã¤nde. När en replik har skapats kan flera inställningar ändras oberoende av huvudservern: beräkningsgenerering, virtuella kärnor, lagring och säkerhetskopieringsperiod. Prisnivån kan också ändras oberoende av dem, förutom till eller från basic-nivån.

> [!IMPORTANT]
> Innan en huvudserverinställning uppdateras till ett nytt värde uppdaterar du replikinställningen till ett lika stort eller större värde. Den här åtgärden hjälper repliken att hålla jämna steg med alla ändringar som gjorts i bakgrunden.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en huvudserver med hjälp av [az postgres server repliklista](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) kommando.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en huvudserver och en läsreplik med hjälp av [kommandot az postgres serverreplikstopp.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

När du har stoppat replikeringen till en huvudserver och en läsreplik kan den inte ångras. Läsrepliken blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud- eller replikserver
Om du vill ta bort en huvud- eller replikserver använder du kommandot [az postgres server delete.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

När du tar bort en huvudserver stoppas replikeringen till alla lästa repliker. Läsrepliker blir fristående servrar som nu stöder både läsningar och skrivningar.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Du kan skapa och hantera läsande repliker med Hjälp av [Azure REST API](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Förbereda huvudservern
Dessa steg måste användas för att förbereda en huvudserver på nivåerna Allmänt ändamål eller Minnesoptimerade.

Parametern `azure.replication_support` måste ställas in på **REPLIK** på huvudservern. När den här statiska parametern ändras krävs en omstart av servern för att ändringen ska börja gälla.

1. Ställ `azure.replication_support` in på REPLIK.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Starta om servern](/rest/api/postgresql/servers/restart) för att tillämpa ändringen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Skapa en läsreplik
Du kan skapa en läsreplik med hjälp av [api:et:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Mer information om vilka regioner du kan skapa en replik i finns i [artikeln läsreplikbegrepp](concepts-read-replicas.md). 

Om du inte har `azure.replication_support` angett parametern till **REPLIK** på en general purpose- eller minnesoptimerad huvudserver och startat om servern visas ett felmeddelande. Slutför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings- och lagringsinställningar som huvudsã¤nde. När en replik har skapats kan flera inställningar ändras oberoende av huvudservern: beräkningsgenerering, virtuella kärnor, lagring och säkerhetskopieringsperiod. Prisnivån kan också ändras oberoende av dem, förutom till eller från basic-nivån.


> [!IMPORTANT]
> Innan en huvudserverinställning uppdateras till ett nytt värde uppdaterar du replikinställningen till ett lika stort eller större värde. Den här åtgärden hjälper repliken att hålla jämna steg med alla ändringar som gjorts i bakgrunden.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en huvudserver med hjälp av [repliklistan API:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en huvudserver och en läsreplik med hjälp av [uppdaterings-API:et](/rest/api/postgresql/servers/update).

När du har stoppat replikeringen till en huvudserver och en läsreplik kan den inte ångras. Läsrepliken blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud- eller replikserver
Om du vill ta bort en huvud- eller replikserver använder du [borttagnings-API:et:](/rest/api/postgresql/servers/delete)

När du tar bort en huvudserver stoppas replikeringen till alla lästa repliker. Läsrepliker blir fristående servrar som nu stöder både läsningar och skrivningar.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om [att läsa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar läsande repliker i Azure-portalen](howto-read-replicas-portal.md).

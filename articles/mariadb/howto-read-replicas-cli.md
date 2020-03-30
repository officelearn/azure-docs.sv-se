---
title: Hantera läsande repliker - Azure CLI, REST API - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du konfigurerar och hanterar läsande repliker i Azure Database för MariaDB med hjälp av Azure CLI och REST API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abf80e98881b73bed53c5a939a79bc8b3a9de2db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530588"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Så här skapar och hanterar du läsande repliker i Azure Database för MariaDB med Azure CLI- och REST API

I den här artikeln får du lära dig hur du skapar och hanterar läsande repliker i Azure Database for MariaDB-tjänsten med hjälp av Azure CLI och REST API.

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera läsande repliker med Hjälp av Azure CLI.

### <a name="prerequisites"></a>Krav

- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- En [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) som ska användas som huvudserver. 

> [!IMPORTANT]
> Funktionen läsreplik är endast tillgänglig för Azure Database för MariaDB-servrar på prisnivåerna Allmänt ändamål eller Minne Optimerad. Kontrollera att huvudservern finns på en av dessa prisnivåer.

### <a name="create-a-read-replica"></a>Skapa en läsreplik

En läsreplikserver kan skapas med följande kommando:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Kommandot `az mariadb server replica create` kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där replikservern ska skapas till.  |
| namn | mydemoreplicaserver (på väg till) | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namnet eller ID:t för den befintliga huvudservern som ska replikeras från. |

Om du vill skapa en `--location` läsreplik för korsområde använder du parametern. 

> [!NOTE]
> Replikering mellan regioner är i förhandsgranskning.

CLI-exemplet nedan skapar repliken i västra USA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Mer information om vilka regioner du kan skapa en replik i finns i [artikeln läsreplikbegrepp](concepts-read-replicas.md). 

> [!NOTE]
> Läsrepliker skapas med samma serverkonfiguration som huvudprogrammet. Replikserverkonfigurationen kan ändras när den har skapats. Vi rekommenderar att replikserverns konfiguration ska hållas på lika med eller större värden än huvudprogrammet för att säkerställa att repliken kan hålla jämna steg med huvudprogrammet.

### <a name="list-replicas-for-a-master-server"></a>Lista repliker för en huvudserver

Om du vill visa alla repliker för en viss huvudserver kör du följande kommando: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Kommandot `az mariadb server replica list` kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där replikservern ska skapas till.  |
| server-name | mydemoserver | Namnet eller ID:t för huvudservern. |

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Att stoppa replikering till en server är oåterkalleligt. När replikeringen har stoppats mellan en huvudsida och replik kan den inte ångras. Replikservern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Replikeringen till en läsreplikserver kan stoppas med följande kommando:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Kommandot `az mariadb server replica stop` kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där replikservern finns.  |
| namn | mydemoreplicaserver (på väg till) | Namnet på replikservern som replikservern ska stoppa replikeringen på. |

### <a name="delete-a-replica-server"></a>Ta bort en replikserver

Ta bort en läsreplikserver kan göras genom att köra kommandot **[az mariadb-server delete.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en huvudserver kan du köra kommandot **[az mariadb server delete.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Du kan skapa och hantera läsande repliker med Hjälp av [Azure REST API](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Skapa en läsreplik
Du kan skapa en läsreplik med hjälp av [api:et:](/rest/api/mariadb/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
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
Du kan visa listan över repliker av en huvudserver med hjälp av [repliklistan API:](/rest/api/mariadb/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en huvudserver och en läsreplik med hjälp av [uppdaterings-API:et](/rest/api/mariadb/servers/update).

När du har stoppat replikeringen till en huvudserver och en läsreplik kan den inte ångras. Läsrepliken blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud- eller replikserver
Om du vill ta bort en huvud- eller replikserver använder du [borttagnings-API:et:](/rest/api/mariadb/servers/delete)

När du tar bort en huvudserver stoppas replikeringen till alla lästa repliker. Läsrepliker blir fristående servrar som nu stöder både läsningar och skrivningar.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsrepliker](concepts-read-replicas.md)

---
title: Skapa och hantera Läs repliker i Azure Database for MariaDB – AZURE CLI REST API
description: Den här artikeln beskriver hur du konfigurerar och hanterar Läs repliker i Azure Database for MariaDB med hjälp av Azure CLI och REST API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 2f5029ccbf80551721ecc363aa4c3930961d9154
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993064"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Skapa och hantera Läs repliker i Azure Database for MariaDB med hjälp av Azure CLI och REST API

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MariaDB-tjänsten med hjälp av Azure CLI och REST API.

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera Läs repliker med hjälp av Azure CLI.

### <a name="prerequisites"></a>Förutsättningar

- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-portal.md) som ska användas som huvud server. 

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MariaDB servrar i Generell användning eller Minnesoptimerade pris nivåer. Kontrollera huvudservern är i något av dessa prisnivåer.

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

En skrivskyddad replikserver kan skapas med följande kommando:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Den `az mariadb server replica create` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern skapas.  |
| namn | mydemoreplicaserver | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namn eller ID på den befintliga huvudservern för att replikera från. |

Använd parametern om du vill skapa en skrivskyddad replik `--location` av en kors region. CLI-exemplet nedan skapar repliken i USA, västra.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

> [!NOTE]
> Läs repliker skapas med samma serverkonfiguration som huvudserver. Serverkonfigurationen repliken kan ändras när den har skapats. Du rekommenderas att repliken serverkonfigurationen bör hållas lika med eller större värden än huvudservern så repliken kan hålla jämna steg med huvudservern.

### <a name="list-replicas-for-a-master-server"></a>Lista repliker för en huvudserver

Om du vill visa alla repliker för en viss huvudservern kör du följande kommando: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Den `az mariadb server replica list` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern skapas.  |
| server-name | mydemoserver | Namn eller ID för huvudservern. |

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Stoppa replikering till en server kan inte ångras. När replikering har upphört mellan huvud- och repliken och kan inte den ångras. Replikservern sedan blir en fristående server och stöder nu både läs- och skrivåtgärder. Den här servern kan inte göras i en replik igen.

Replikering till en replikserver som skrivskyddade kan stoppas med följande kommando:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Den `az mariadb server replica stop` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern finns.  |
| namn | mydemoreplicaserver | Namnet på replikservern för att stoppa replikering på. |

### <a name="delete-a-replica-server"></a>Ta bort en replikserver

Du kan ta bort en Läs replik Server genom att köra kommandot **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Tar bort en huvudserver stoppar replikeringen till alla replikservrar och borttagningar huvudservern själva. Replikservrar bli fristående servrar som stöder nu både läs- och skrivåtgärder.

Om du vill ta bort en huvud server kan du köra kommandot **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST-API
Du kan skapa och hantera Läs repliker med hjälp av [Azure REST API](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik
Du kan skapa en Läs replik med hjälp av [create API](/rest/api/mariadb/servers/create):

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
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

Om du inte har angett `azure.replication_support` parametern till **replik** på en generell användning eller en minnesoptimerade huvud server och startat om servern, visas ett fel meddelande. Utför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generation, virtuella kärnor, lagring och säkerhets kopierings perioden. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.


> [!IMPORTANT]
> Innan en huvud server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en huvud server med hjälp av [replik listans API](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en huvud server och en Läs replik med hjälp av [uppdaterings-API: et](/rest/api/mariadb/servers/update).

När du har stoppat replikering till en huvud server och en Läs replik kan du inte göra det. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

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

### <a name="delete-a-master-or-replica-server"></a>Ta bort en huvud-eller replik Server
Om du vill ta bort en huvud server eller replik Server använder du [borttagnings-API: et](/rest/api/mariadb/servers/delete):

När du tar bort en huvud server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsa repliker](concepts-read-replicas.md)

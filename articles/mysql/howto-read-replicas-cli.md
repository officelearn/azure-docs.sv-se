---
title: Skapa och hantera skrivskyddade repliker i Azure Database för MySQL
description: Den här artikeln beskriver hur du konfigurerar och hanterar skrivskyddade repliker i Azure Database for MySQL med Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304963"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Hur du skapar och hanterar Läs repliker i Azure Database för MySQL med Azure CLI

I den här artikeln får lära du dig att skapa och hantera skrivskyddade kopior på samma Azure-region som hanterare i tjänsten Azure Database for MySQL med Azure CLI.

> [!IMPORTANT]
> Du kan skapa en skrivskyddad replik i samma region som din huvudservern eller i alla andra Azure-regioner valfri. Replikering över flera regioner är för närvarande i offentlig förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som huvudserver. 

> [!IMPORTANT]
> Läs replica-funktionen är endast tillgänglig för Azure Database för MySQL-servrar i generell användning eller Minnesoptimerade prisnivåer. Kontrollera huvudservern är i något av dessa prisnivåer.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

En skrivskyddad replikserver kan skapas med följande kommando:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Den `az mysql server replica create` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern skapas.  |
| namn | mydemoreplicaserver | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namn eller ID på den befintliga huvudservern för att replikera från. |

Skapa en cross region läsas repliken, Använd den `--location` parametern. CLI-exemplet nedan skapar repliken i västra USA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Läs repliker skapas med samma serverkonfiguration som huvudserver. Serverkonfigurationen repliken kan ändras när den har skapats. Du rekommenderas att repliken serverkonfigurationen bör hållas lika med eller större värden än huvudservern så repliken kan hålla jämna steg med huvudservern.

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Stoppa replikering till en server kan inte ångras. När replikering har upphört mellan huvud- och repliken och kan inte den ångras. Replikservern sedan blir en fristående server och stöder nu både läs- och skrivåtgärder. Den här servern kan inte göras i en replik igen.

Replikering till en replikserver som skrivskyddade kan stoppas med följande kommando:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Den `az mysql server replica stop` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern finns.  |
| namn | mydemoreplicaserver | Namnet på replikservern för att stoppa replikering på. |

## <a name="delete-a-replica-server"></a>Ta bort en replikserver

Tar bort en skrivskyddad replikserver kan göras genom att köra den **[az mysql server delete](/cli/azure/mysql/server)** kommando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Tar bort en huvudserver stoppar replikeringen till alla replikservrar och borttagningar huvudservern själva. Replikservrar bli fristående servrar som stöder nu både läs- och skrivåtgärder.

Om du vill ta bort en huvudserver, kan du köra den **[az mysql server delete](/cli/azure/mysql/server)** kommando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Lista repliker för en huvudserver

Om du vill visa alla repliker för en viss huvudservern kör du följande kommando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Den `az mysql server replica list` kommandot kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där replikservern skapas.  |
| server-name | mydemoserver | Namn eller ID för huvudservern. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsa repliker](concepts-read-replicas.md)

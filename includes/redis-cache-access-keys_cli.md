---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 0289604cce7f956406d65743d5b058ec92111724
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182663"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Hämta värdnamn, portar och åtkomstnycklar via kommandoradsgränssnittet för Azure
Om du vill hämta värdnamn och portar via Azure CLI 2.0 kan du ange kommandot [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) och om du vill hämta nycklar kan du ange [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). I följande skript körs de här två kommandona så att värdnamn, portar och nycklar skickas till konsolen.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Mer information om det här skriptet finns i [Get the hostname, ports, and keys for Azure Redis Cache](../articles/redis-cache/scripts/cache-keys-ports.md) (Hämta värdnamn, portar och nycklar för Azure Redis Cache). Mer information om Azure CLI 2.0 finns i [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Installera Azure CLI 2.0) och [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Kom igång med Azure CLI 2.0).

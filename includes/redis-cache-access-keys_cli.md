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
ms.openlocfilehash: d32ea8af6ad8282872572076db4b9eef34e05166
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011866"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Hämta värdnamn, portar och åtkomstnycklar via kommandoradsgränssnittet för Azure

Hämta värdnamn och portar med hjälp av Azure CLI kan du anropa [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), och för att hämta nycklar kan du ange [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). I följande skript körs de här två kommandona så att värdnamn, portar och nycklar skickas till konsolen.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Mer information om det här skriptet finns i [hämta värdnamn, portar och nycklar för Azure Azure Cache för Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Läs mer om Azure CLI [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

---
title: Hur du använder redis-cli med Azure Redis Cache | Microsoft Docs
description: Lär dig hur du använder redis-cli med Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299099"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Hur du använder kommandoradsverktyget Redis med Azure Redis Cache

*redis-cli.exe* är ett populära kommandoradsverktyg för att interagera med en Redis-Cache som en klient. Det här verktyget är också tillgängliga för användning med Azure Redis Cache.

Verktyget är tillgängligt för Windows-plattformar genom att ladda ned den [Redis kommandoradsverktyg för Windows](https://github.com/MSOpenTech/redis/releases/). 

Om du vill köra kommandoradsverktyget på en annan plattform kan hämta Redis-Cache från [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Samla in information för cache-åtkomst

Du kan samla in information som behövs för att komma åt cachen med tre metoder:

1. Azure CLI med [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell med [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Med Azure portal.

I det här avsnittet ska du hämta nycklar från Azure-portalen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Aktivera åtkomst för redis-cli.exe

Med Azure Redis Cache, endast SSL-port (6380) är aktiverat som standard. Den `redis-cli.exe` kommandoradsverktyget stöder inte SSL. Du har två alternativ för att använda den:

1. [Aktivera icke-SSL-port (6379)](cache-configure.md#access-ports) - **rekommenderas inte den här konfigurationen** eftersom i den här konfigurationen åtkomstnycklarna skickas via TCP i klartext. Den här ändringen kan påverka åtkomst till ditt cacheminne. Det enda fallet där du kan överväga att den här konfigurationen är när du bara använder en test-cache.

2. Ladda ned och installera [stunnel](https://www.stunnel.org/downloads.html).

    Kör **stunnel GUI Start** att starta servern.

    Högerklicka på ikonen i Aktivitetsfältet för stunnel-servern och på **visa loggfönstret**.

    Klicka på menyn stunnel loggfönstret **Configuration** > **redigera konfiguration** att öppna den aktuella konfigurationsfilen.

    Lägg till följande post för *redis-cli.exe* under den **tjänsten definitioner** avsnittet. Infoga dina faktiska cachenamn i stället för `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Spara och Stäng konfigurationsfilen. 
  
    Klicka på menyn stunnel loggfönstret **Configuration** > **ladda konfigurationen**.


## <a name="connect-using-the-redis-command-line-tool"></a>Ansluta med hjälp av kommandoradsverktyget Redis.

När du använder stunnel kör *redis-cli.exe*, och skicka bara din *port*, och *åtkomstnyckel* (primär eller sekundär) för att ansluta till cachen.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel med redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Om du använder en test-cache med den **oskyddade** icke-SSL-port, kör `redis-cli.exe` och skicka din *värdnamn*, *port*, och *åtkomstnyckel*(primär eller sekundär) för att ansluta till test-cachen.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel med redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder den [Redis-konsolen](cache-configure.md#redis-console) att utfärda kommandon.


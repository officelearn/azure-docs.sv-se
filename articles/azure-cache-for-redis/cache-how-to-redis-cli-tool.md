---
title: Så här använder du redis-cli med Azure Cache för Redis
description: Lär dig hur du använder *redis-cli.exe* som kommandoradsverktyg för att interagera med en Azure-cache för Redis som klient.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010778"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Så här använder du kommandoradsverktyget Redis med Azure Cache för Redis

*redis-cli.exe* är ett populärt kommandoradsverktyg för att interagera med en Azure Cache för Redis som klient. Det här verktyget är också tillgängligt för användning med Azure Cache för Redis.

Verktyget är tillgängligt för Windows-plattformar genom att hämta [Redis kommandoradsverktyg för Windows](https://github.com/MSOpenTech/redis/releases/). 

Om du vill köra kommandoradsverktyget på en annan plattform [https://redis.io/download](https://redis.io/download)hämtar du Azure Cache för Redis från .

## <a name="gather-cache-access-information"></a>Information om insamling av cacheåtkomst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan samla in den information som behövs för att komma åt cachen med tre metoder:

1. Azure CLI med [az redis list-nycklar](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell med [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Använda Azure-portalen.

I det här avsnittet hämtar du nycklarna från Azure-portalen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Aktivera åtkomst för redis-cli.exe

Med Azure Cache för Redis är endast TLS-porten (6380) aktiverad som standard. Kommandoradsverktyget `redis-cli.exe` stöder inte TLS. Du har två konfigurationsalternativ för att använda den:

1. [Aktivera icke-TLS-porten (6379)](cache-configure.md#access-ports) - **Den här konfigurationen rekommenderas inte** eftersom åtkomstnycklarna i den här konfigurationen skickas via TCP i klartext. Den här ändringen kan äventyra åtkomsten till cacheminnet. Det enda scenariot där du kan överväga den här konfigurationen är när du bara har åtkomst till en testcache.

2. Ladda ner och installera [stunnel](https://www.stunnel.org/downloads.html).

    Kör **stunnel GUI Börja** med att starta servern.

    Högerklicka på ikonen i Aktivitetsfältet för stunnelservern och klicka på **Visa loggfönster**.

    Klicka på > **Konfigurationsredigeringskonfiguration** på menyn stunnelloggfönster för att öppna den aktuella konfigurationsfilen. **Configuration**

    Lägg till följande post för *redis-cli.exe* under avsnittet **Tjänstdefinitioner.** Infoga ditt faktiska cachenamn `yourcachename`i stället för . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Spara och stäng konfigurationsfilen. 
  
    Klicka på > **Konfigurationsladdningskonfiguration**på menyn stunnelloggfönster . **Configuration**


## <a name="connect-using-the-redis-command-line-tool"></a>Anslut med kommandoradsverktyget Redis.

När du använder stunnel kör du *redis-cli.exe*och skickar endast *porten*och *åtkomstnyckeln* (primär eller sekundär) för att ansluta till cachen.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel med redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Om du använder en testcache med den **osäkra** icke-TLS-porten `redis-cli.exe` kör och skickar *du värdnamnet,* *porten*och *åtkomstnyckeln* (primär eller sekundär) för att ansluta till testcachen.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel med redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder [Redis-konsolen](cache-configure.md#redis-console) för att utfärda kommandon.


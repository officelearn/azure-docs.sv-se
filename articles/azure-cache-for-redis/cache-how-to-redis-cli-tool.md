---
title: Använda Redis-CLI med Azure cache för Redis
description: Lär dig hur du använder Redis-CLI med Azure cache för Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 510110a201e943b8ffce07fc7d0de1361264ae62
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122716"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Använda kommando rads verktyget Redis med Azure cache för Redis

*Redis-cli. exe* är ett populärt kommando rads verktyg för att interagera med en Azure-cache för Redis som en klient. Det här verktyget är också tillgängligt för användning med Azure cache för Redis.

Verktyget är tillgängligt för Windows-plattformar genom att hämta [kommando rads verktygen för Redis för Windows](https://github.com/MSOpenTech/redis/releases/). 

Om du vill köra kommando rads verktyget på en annan plattform laddar du ned Azure cache för Redis från [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Samla in information om cache-åtkomst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan samla in den information som behövs för att komma åt cachen med tre metoder:

1. Azure CLI med [AZ Redis List-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell att använda [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Använda Azure Portal.

I det här avsnittet hämtar du nycklarna från Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Aktivera åtkomst för Redis-cli. exe

Med Azure cache för Redis är endast SSL-porten (6380) aktive rad som standard. Kommando rads verktyget `redis-cli.exe` stöder inte SSL. Du har två konfigurations alternativ att använda den:

1. [Aktivera icke-SSL-porten (6379)](cache-configure.md#access-ports) - **den här konfigurationen rekommenderas inte** eftersom i den här konfigurationen skickas åtkomst nycklarna via TCP i klartext. Den här ändringen kan kompromettera åtkomst till din cache. Det enda scenariot där du kan tänka på den här konfigurationen är när du bara har åtkomst till en testcache.

2. Hämta och installera [stunnelserver](https://www.stunnel.org/downloads.html).

    Kör **STUNNELSERVER GUI start** för att starta-servern.

    Högerklicka på ikonen i aktivitets fältet för stunnelserver-servern och klicka på **Visa logg fönster**.

    På stunnelserver logg fönstret klickar du på **konfiguration** > **Redigera konfiguration** för att öppna den aktuella konfigurations filen.

    Lägg till följande post för *Redis-cli. exe* under avsnittet **tjänst definitioner** . Infoga det faktiska cache-namnet i stället för `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Spara och Stäng konfigurations filen. 
  
    På stunnelserver-fönstrets logg fönster klickar du på **konfiguration** > **laddar om konfigurationen**.


## <a name="connect-using-the-redis-command-line-tool"></a>Anslut med hjälp av kommando rads verktyget Redis.

När du använder stunnelserver kör du *Redis-cli. exe*och skickar bara din *port*och *åtkomst nyckeln* (primär eller sekundär) för att ansluta till cacheminnet.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnelserver med Redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Om du använder en testcache med en **osäker** icke-SSL-port kör `redis-cli.exe` och skickar *värd namnet*, *porten*och *åtkomst nyckeln* (primär eller sekundär) för att ansluta till test-cachen.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnelserver med Redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Nästa steg

Lär dig mer om att använda [Redis-konsolen](cache-configure.md#redis-console) för att utfärda kommandon.


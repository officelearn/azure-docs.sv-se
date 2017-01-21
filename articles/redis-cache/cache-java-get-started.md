---
title: "Så här använder du Azure Redis Cache med Java | Microsoft Docs"
description: "Kom igång med Azure Redis Cache med Java"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: c42aebb3aaf5c32ebdc4f79e2ace2f127e4fb20d
ms.openlocfilehash: fe875fba2651b770d910d257282f5e9f41f8a043


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Så här använder du Azure Redis Cache med Java
> [!div class="op_single_selector"]
> * [NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache ger dig tillgång till en dedikerad Redis-cache som hanteras av Microsoft. Din cache är tillgänglig från alla program i Microsoft Azure.

I det här avsnittet visar vi hur du kommer igång med Azure Redis Cache med Java.

## <a name="prerequisites"></a>Förutsättningar
[Jedis](https://github.com/xetorthio/jedis) – Java-klient för Redis

Den här självstudien använder Jedis, men du kan använda valfri Java-klient som finns i [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Skapa en Redis-cache på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Hämta värdnamn och åtkomstnycklar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Ansluta till cache på ett säkert sätt med hjälp av SSL
De senaste build-versionerna av [jedis](https://github.com/xetorthio/jedis) stöder anslutning av Azure Redis Cache med hjälp av SSL. I följande exempel visas hur du ansluter till Azure Redis Cache med hjälp av SSL-slutpunkten 6380. Ersätt `<name>` med namnet på din cache och `<key>` med antingen en primär eller sekundär nyckel som beskrivs i föregående avsnitt, [Hämta värdnamn och åtkomstnycklar](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Lägg till något i cachen och hämta det
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Nästa steg
* [Aktivera cachediagnostik](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) så att du kan [övervaka](https://msdn.microsoft.com/library/azure/dn763945.aspx) hälsotillståndet för cacheminnet.
* Läs den officiella [Redis-dokumentationen](http://redis.io/documentation).



<!--HONumber=Dec16_HO3-->



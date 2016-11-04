---
title: Så här använder du Azure Redis Cache med Java | Microsoft Docs
description: Kom igång med Azure Redis Cache med Java
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/24/2016
ms.author: sdanie

---
# Så här använder du Azure Redis Cache med Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache ger dig tillgång till en dedikerad Redis-cache som hanteras av Microsoft. Din cache är tillgänglig från alla program i Microsoft Azure.

I det här avsnittet visar vi hur du kommer igång med Azure Redis Cache med Java.

## Förutsättningar
[Jedis](https://github.com/xetorthio/jedis) – Java-klient för Redis

Den här självstudien använder Jedis, men du kan använda valfri Java-klient som finns i [http://redis.io/clients](http://redis.io/clients).

## Skapa en Redis-cache på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Hämta värdnamn och åtkomstnycklar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Aktivera icke-SSL-slutpunkten
Vissa Redis-klienter stöder inte SSL, och som standard är [icke-SSL-porten inaktiverad för nya Azure Redis Cache-instanser](cache-configure.md#access-ports). När detta skrivs stöder [Jedis](https://github.com/xetorthio/jedis)-klienten inte SSL. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## Lägg till något i cachen och hämta det
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## Nästa steg
* [Aktivera cachediagnostik](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) så att du kan [övervaka](https://msdn.microsoft.com/library/azure/dn763945.aspx) hälsotillståndet för cacheminnet.
* Läs den officiella [Redis-dokumentationen](http://redis.io/documentation).

<!--HONumber=Sep16_HO3-->



---
title: Så här använder du Azure Redis Cache med Node.js | Microsoft Docs
description: Kom igång med Azure Redis Cache med hjälp av Node.js och node_redis.
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Så här använder du Azure Redis Cache med Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache ger dig tillgång till en säker och dedikerad Redis-cache som hanteras av Microsoft. Din cache är tillgänglig från alla program i Microsoft Azure.

I det här avsnittet visar vi hur du kommer igång med Azure Redis Cache med Node.js. Ett annat exempel på Azure Redis Cache med Node.js finns i [Bygga ett Node.js chattprogram med Socket.IO på en Azure-webbplats](../app-service-web/web-sites-nodejs-chat-app-socketio.md).

## Förutsättningar
Installera [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Den här självstudien använder [node_redis](https://github.com/mranney/node_redis). Exempel på användning av andra Node.js-klienter finns i dokumentationen till enskilda Node.js-klienter som anges i [Node.js Redis-klienter](http://redis.io/clients#nodejs).

## Skapa en Redis-cache på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Hämta värdnamn och åtkomstnycklar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Ansluta till cache på ett säkert sätt med hjälp av SSL
De senaste build-versionerna av [node_redis](https://github.com/mranney/node_redis) ger stöd för att ansluta till Azure Redis Cache med hjälp av SSL. I följande exempel visas hur du ansluter till Azure Redis Cache med hjälp av SSL-slutpunkten 6380. Ersätt `<name>` med namnet på din cache och `<key>` med antingen en primär eller sekundär nyckel som beskrivs i föregående avsnitt, [Hämta värdnamn och åtkomstnycklar](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Lägg till något i cachen och hämta det
I följande exempel visas hur du ansluter till en Azure Redis Cache-instans och lagrar och hämtar ett objekt från cachen. Fler exempel på hur du använder Redis med [node_redis](https://github.com/mranney/node_redis)-klienten finns på [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Resultat:

    OK
    value


## Nästa steg
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet.
* Läs den officiella [Redis-dokumentationen](http://redis.io/documentation).

<!--HONumber=sep16_HO1-->



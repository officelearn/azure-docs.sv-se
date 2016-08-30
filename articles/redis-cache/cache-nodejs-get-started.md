<properties
    pageTitle="Så här använder du Azure Redis Cache med Node.js | Microsoft Azure"
    description="Kom igång med Azure Redis Cache med hjälp av Node.js och node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Så här använder du Azure Redis Cache med Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache ger dig tillgång till en säker och dedikerad Redis-cache som hanteras av Microsoft. Din cache är tillgänglig från alla program i Microsoft Azure.

I det här avsnittet visar vi hur du kommer igång med Azure Redis Cache med Node.js. Ett annat exempel på Azure Redis Cache med Node.js finns i [Bygga ett Node.js chattprogram med Socket.IO på en Azure-webbplats](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Förutsättningar

Installera [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Den här självstudien använder [node_redis](https://github.com/mranney/node_redis), men du kan använda valfri Node.js-klient som anges i [http://redis.io/clients](http://redis.io/clients).

## Skapa en Redis-cache på Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Hämta värdnamn och åtkomstnycklar

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Aktivera icke-SSL-slutpunkten

Vissa Redis-klienter stöder inte SSL, och som standard är [icke-SSL-porten inaktiverad för nya Azure Redis Cache-instanser](cache-configure.md#access-ports). När detta skrivs stöder [node_redis](https://github.com/mranney/node_redis)-klienten inte SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Lägg till något i cachen och hämta det

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

- [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet.
- Läs den officiella [Redis-dokumentationen](http://redis.io/documentation).






<!--HONumber=jun16_HO2-->



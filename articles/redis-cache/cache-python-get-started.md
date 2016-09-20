<properties
    pageTitle="Så här använder du Azure Redis Cache med Python| Microsoft Azure"
    description="Kom igång med Azure Redis Cache med Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# Så här använder du Azure Redis Cache med Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Det här avsnittet visar hur du kommer igång med Azure Redis Cache med Python.


## Förutsättningar

Installera [redis-py](https://github.com/andymccurdy/redis-py).


## Skapa en Redis-cache på Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Hämta värdnamn och åtkomstnycklar

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Aktivera icke-SSL-slutpunkten

Vissa Redis-klienter stöder inte SSL, och som standard är [icke-SSL-porten inaktiverad för nya Azure Redis Cache-instanser](cache-configure.md#access-ports). När detta skrivs stöder [redis-py](https://github.com/andymccurdy/redis-py)-klienten inte SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Lägg till något i cachen och hämta det


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Ersätt `<name>` med ditt cachenamn och `key` med din åtkomstnyckel.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png



<!--HONumber=sep16_HO1-->



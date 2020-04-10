---
title: 'Snabbstart: Skapa en Python-app - Azure Cache för Redis'
description: I den här snabbstarten får du lära dig hur du skapar en Python-app som använder Azure Cache för Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 6ce3dd1cbb694988af3555765342a1c4ca3850b5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010859"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Snabbstart: Skapa en Python-app som använder Azure Cache för Redis

I den här artikeln införlivar du Azure Cache för Redis i en Python-app för att få åtkomst till en säker, dedikerad cache som är tillgänglig från alla program i Azure.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Python 2 eller 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Azure Cache for Redis. Använd python-paketverktyget *pip*för att installera *paketet redis-py* från en kommandotolk. 

I följande exempel används *pip3* för Python 3 för att installera *redis-py* på Windows 10 från en administratörskommandofråga.

![Installera redis-py Python-gränssnittet i Azure Cache för Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Läsa och skriva till cachen

Kör Python från kommandoraden och testa cacheminnet med hjälp av följande kod. Ersätt `<Your Host Name>` `<Your Access Key>` och med värdena från Azure Cache för Redis-instansen. Värdnamnet är av formuläret * \<DNS-namn>.redis.cache.windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> För Azure Cache för Redis version 3.0 eller senare tillämpas TLS/SSL-certifikatkontrollen. ssl_ca_certs måste uttryckligen anges när du ansluter till Azure Cache för Redis. För RedHat Linux finns ssl_ca_certs i certifikatmodulen */etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Skapa en Python-exempelapp

Skapa en ny textfil, lägg till följande skript och spara filen som *PythonApplication1.py*. Ersätt `<Your Host Name>` `<Your Access Key>` och med värdena från Azure Cache för Redis-instansen. Värdnamnet är av formuläret * \<DNS-namn>.redis.cache.windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Kör *PythonApplication1.py* med Python. Du bör se resultat som följande exempel:

![Kör Python-skript för att testa cacheåtkomst](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med Azure-resursgruppen och resurser som du skapade i den här snabbstarten kan du ta bort dem för att undvika avgifter.

> [!IMPORTANT]
> Det går inte att ta bort en resursgrupp och resursgruppen och alla resurser i resursen tas bort permanent. Om du har skapat azure-cachen för Redis-instansen i en befintlig resursgrupp som du vill behålla kan du bara ta bort cacheminnet genom att välja **Ta bort** från sidan Översikt **för cache.** 

Så här tar du bort resursgruppen och dess Redis-cache för Azure-instans:

1. Sök efter och välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com).
1. I textrutan **Filter efter namn** anger du namnet på resursgruppen som innehåller cacheinstansen och markerar den sedan i sökresultaten. 
1. Välj **Ta bort resursgrupp** på din resursgruppssida.
1. Skriv resursgruppsnamnet och välj sedan **Ta bort**.
   
   ![Ta bort resursgruppen för Azure Cache för Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)


---
title: 'Snabb start: skapa en python-app som använder Azure cache för Redis'
description: I den här snabbstarten lär du dig hur du skapar en Python-app som använder Azure Cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 5367896c931bf7c5f52b0874d49ede2fc78614ab
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435520"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Snabb start: skapa en python-app som använder Azure cache för Redis

I den här artikeln införlivar du Azure cache för Redis i en python-app för att få åtkomst till en säker, dedikerad cache som är tillgänglig från alla program i Azure.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Python 2 eller 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Skapa Azure Cache for Redis på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Azure Cache for Redis. Använd Python-verktygspaketet *pip* för att installera redis-py-paketet. 

I följande exempel används *PIP3* för python3 för att installera Redis-py-paketet på Windows 10 med hjälp av en Visual Studio 2019 Developer-kommando tolk som körs med utökade administratörs privilegier.

```python
    pip3 install redis
```

![Installera Redis-py python-gränssnittet till Azure cache för Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Läsa och skriva till cachen

Kör Python och testa med hjälp av cachen från kommandoraden. Ersätt `<Your Host Name>` och `<Your Access Key>` med värdena för Azure Cache for Redis. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> För Redis-versionen är 3,0 eller högre tillämpas SSL-certifikats kontrollen. ssl_ca_certs måste anges explicit vid anslutning till Redis. Om du använder RH Linux finns ssl_ca_certs i certifikats-modulen "/etc/PKI/TLS/certs/ca-Bundle.CRT".

## <a name="create-a-python-script"></a>Skapa ett Python-skript

Skapa en ny textfil i skriptet med namnet *PythonApplication1.py*.

Lägg till följande skript till *PythonApplication1.py* och spara filen. Det här skriptet kommer att testa cacheåtkomst. Ersätt `<Your Host Name>` och `<Your Access Key>` med värdena för Azure Cache for Redis. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
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

Kör skriptet med Python.

![Kör Python-skript för att testa cache-åtkomst i Azure cache för Redis](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Rensa resurser

Om du ska fortsätta till nästa självstudie kan du behålla resurserna som du har skapat i den här självstudien och använda dem igen.

Om du är klar med exempelappen för snabbstart kan du ta bort Azure-resurserna som du skapade i snabbstarten för att undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.
>

Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

I text rutan **Filtrera efter namn...** anger du namnet på din resurs grupp. Anvisningarna för den här artikeln använde en resursgrupp med namnet *TestResources*. I resurs gruppen i resultat listan väljer du **...** och sedan **ta bort resurs grupp**.

![Ta bort din snabb starts resurs grupp för Azure cache för Redis](./media/cache-web-app-howto/delete-your-resource-group-for-azure-cache-for-redis.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Ange namnet på resurs gruppen som ska bekräftas och välj **ta bort**.

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

---
title: Snabbstart för att skapa en Python-app som använder Azure Cache for Redis | Microsoft Docs
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
ms.custom: mvc
ms.openlocfilehash: 8682a335372a1516be49b35400a2605f08d6b45e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779461"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Snabbstart: Använda Azure Cache for Redis med Python


## <a name="introduction"></a>Introduktion

Den här snabbstarten visar hur du ansluter Azure Cache for Redis med Python för att läsa och skriva cache. 

![Python-testet slutfördes](./media/cache-python-get-started/cache-python-completed.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Python 2 eller 3 för Python-miljön](https://www.python.org/downloads/) installeras med [pip](https://pypi.org/project/pip/). 

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Skapa Azure Cache for Redis på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Azure Cache for Redis. Använd Python-verktygspaketet *pip* för att installera redis-py-paketet. 

I följande exempel används *pip3* för Python3 för att installera redis-py-paketet på Windows 10 med utvecklarkommandotolken för Visual Studio 2017 med förhöjd administratörsbehörighet.

    pip3 install redis

![Installera redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


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

## <a name="create-a-python-script"></a>Skapa ett Python-skript

Skapa en ny textfil i skriptet med namnet *PythonApplication1.py*.

Lägg till följande skript till *PythonApplication1.py* och spara filen. Det här skriptet kommer att testa cacheåtkomst. Ersätt `<Your Host Name>` och `<Your Access Key>` med värdena för Azure Cache for Redis. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

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

![Python-testet slutfördes](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Rensa resurser

Om du ska fortsätta till nästa självstudie kan du behålla resurserna som du har skapat i den här självstudien och använda dem igen.

Om du är klar med exempelappen för snabbstart kan du ta bort Azure-resurserna som du skapade i snabbstarten för att undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.
>

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filtrera efter namn...**. Anvisningarna för den här artikeln använde en resursgrupp med namnet *TestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

![Ta bort](./media/cache-web-app-howto/cache-delete-resource-group.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och klicka på **Ta bort**.

Efter en liten stund tas resursgruppen och resurser som finns i den bort.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

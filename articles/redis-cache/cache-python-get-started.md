---
title: Snabbstart för att skapa en Python-app som använder Azure Redis Cache | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en Python-app som använder Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: b03139bda44886eba13892b23dc17fd3f030cc3f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639545"
---
# <a name="quickstart-use-azure-redis-cache-with-python"></a>Snabbstart: Så här använder du Azure Redis Cache med Python


## <a name="introduction"></a>Introduktion

Den här snabbstarten visar hur du ansluter en Azure Redis Cache med Python för att läsa och skriva cache. 

![Python-testet slutfördes](./media/cache-python-get-started/cache-python-completed.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 2 eller 3 för Python-miljön](https://www.python.org/downloads/) installeras med [pip](https://pypi.org/project/pip/). 

## <a name="create-a-redis-cache-on-azure"></a>Skapa en Redis-cache på Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-kopiera](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Redis Cache. Använd Python-verktygspaketet *pip* för att installera redis-py-paketet. 

I följande exempel används *pip3* för Python3 för att installera redis-py-paketet på Windows 10 med utvecklarkommandotolken för Visual Studio 2017 med förhöjd administratörsbehörighet.

    pip3 install redis

![Installera redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Läsa och skriva till cachen

Kör Python och testa med hjälp av cachen från kommandoraden. Ersätt `<Your Host Name>` och `<Your Access Key>` med värden för din Redis Cache. 

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

Lägg till följande skript till *PythonApplication1.py* och spara filen. Det här skriptet kommer att testa cacheåtkomst. Ersätt `<Your Host Name>` och `<Your Access Key>` med värden för din Redis Cache. 

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
> [Skapa ett enkelt ASP.NET-webbprogram som använder Azure Redis Cache.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

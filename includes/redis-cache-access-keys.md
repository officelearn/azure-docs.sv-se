---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73720452"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Hämta värdnamn, portar och åtkomstnycklar från Azure-portalen

För att ansluta till en Azure-cache för Redis-instans behöver cacheklienter värdnamnet, portarna och en nyckel för cachen. Vissa klienter kan hänvisa till dessa objekt med namn som skiljer sig något. Du kan hämta värdnamn, portar och nycklar från [Azure-portalen](https://portal.azure.com).

- Om du vill hämta åtkomstnycklarna väljer du **Access-nycklar**från cachen till vänster. 
  
  ![Nycklar för Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Om du vill hämta värdnamnet och portarna väljer du **Egenskaper**från cachens vänstra navigering . Värdnamnet är av formuläret * \<DNS-namn>.redis.cache.windows.net*.

  ![Egenskaper för Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)


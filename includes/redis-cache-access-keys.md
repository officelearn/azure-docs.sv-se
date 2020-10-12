---
title: inkludera fil
description: inkludera fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "73720452"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Hämta värdnamn, portar och åtkomst nycklar från Azure Portal

För att ansluta till en Azure-cache för Redis-instansen behöver cache-klienterna värd namnet, portarna och en nyckel för cachen. Vissa klienter kan hänvisa till dessa objekt med namn som skiljer sig något. Du kan hämta värdnamn, portar och nycklar från [Azure Portal](https://portal.azure.com).

- Hämta åtkomst nycklarna genom att gå till vänster navigering i cachen och välja **åtkomst nycklar**. 
  
  ![Nycklar för Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- För att hämta värd namnet och portarna, från din cache-vänstra navigering, väljer du **Egenskaper**. Värd namnet är av formatet * \<DNS name> . Redis.cache.Windows.net*.

  ![Egenskaper för Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)


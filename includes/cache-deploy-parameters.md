---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187152"
---
### <a name="cacheskuname"></a>cacheSKUName

Prisnivån för den nya Azure-cachen för Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Mallen definierar de värden som är tillåtna för den här parametern (Basic, Standard eller Premium) och tilldelar ett standardvärde (Basic) om inget värde anges. Basic ger en enda nod med flera storlekar tillgängliga upp till 53 GB. Standard ger tvånod primär/replik med flera storlekar tillgängliga upp till 53 GB och 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Familjen till sku.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUKapacitet

Storleken på den nya Azure-cachen för Redis-instansen.

För basic- och standardfamiljer:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Cachekapaciteten för Premium-värde definieras på samma sätt, förutom att de tillåtna värdena körs från 1 till 5 i stället för från 0 till 6.

Mallen definierar de heltalsvärden som är tillåtna för den här parametern (0 till 6 för basic- och standardfamiljer; 1 till 5 för Premium-familjen). Om inget värde anges tilldelar mallen ett standardvärde på 0 för Basic och Standard, 1 för Premium.

Värdena motsvarar följande cachestorlekar:

| Värde | Grundläggande och standard<br>cachestorlek | Premium<br>cachestorlek |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (standard)                 | Saknas                   |
| 1     | 1 GB                             | 6 GB (standard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | Saknas                   |

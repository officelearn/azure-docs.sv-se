---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118889"
---
### <a name="cacheskuname"></a>cacheSKUName

Prisnivån för den nya Azure-Cache för Redis.

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

Mallen definierar de värden som tillåts för den här parametern (Basic, Standard eller Premium), och tilldelar ett standardvärde (grundläggande), om inget värde anges. Basic tillhandahåller en enda nod med flera storlekar upp till 53 GB. Standard ger tvånods primär/replik med flera storlekar upp till 53 GB och 99,9% serviceavtal (SLA).

### <a name="cacheskufamily"></a>cacheSKUFamily

Familjen för SKU: n.

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

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Storleken på den nya Azure-Cache för Redis-instans.

För Basic och Standard-serier:

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

Premium värdet cache-kapacitet definieras samma, förutom värdena som tillåts köra från 1 till 5 i stället för från 0 till 6.

Mallen definierar integer-värden som tillåts för den här parametern (0 till 6 för Basic och Standard-familjer; 1 – 5 för Premium-serien). Om inget värde anges tilldelar mallen används värdet 0 för Basic och Standard, 1 för Premium.

Värdena motsvarar följande cachestorlekarna:

| Value | Basic och Standard<br>Cachestorlek | Premium<br>Cachestorlek |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (standard)                 | Saknas                   |
| 1     | 1 GB                             | 6 GB (standard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | Saknas                   |

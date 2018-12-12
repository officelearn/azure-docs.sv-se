---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111956"
---
### <a name="cacheskuname"></a>cacheSKUName
Prisnivån för den nya Azure-Cache för Redis.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

Mallen definierar de värden som tillåts för den här parametern (Basic eller Standard), och tilldelar ett standardvärde (grundläggande), om inget värde anges. Basic tillhandahåller en enda nod med flera storlekar upp till 53 GB.
Standard ger tvånods primär/replik med flera storlekar upp till 53 GB och 99,9% serviceavtal (SLA).

### <a name="cacheskufamily"></a>cacheSKUFamily
Familjen för SKU: n.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Storleken på den nya Azure-Cache för Redis-instans. 

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


Mallen definierar de värden som tillåts för den här parametern (0, 1, 2, 3, 4, 5 eller 6), och tilldelar ett standardvärde (0) om inget värde anges. Dessa siffror motsvarar följande cachestorlekarna: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB


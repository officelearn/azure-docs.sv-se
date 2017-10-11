
### <a name="cacheskuname"></a>cacheSKUName
Prisnivån för den nya Azure Redis-Cache.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

Mallen definierar de värden som tillåts för den här parametern (Basic eller Standard), och tilldelas ett standardvärde (grundläggande), om inget värde anges. Basic tillhandahåller en enda nod med flera storlekar som är tillgängliga för upp till 53 GB.
Standarden erbjuder två noder primära/repliken med flera storlekar som är tillgängliga för dig av 53 GB och 99,9% serviceavtal.

### <a name="cacheskufamily"></a>cacheSKUFamily
Familj för SKU: n.

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
Storleken på den nya Azure Redis-Cache-instansen. 

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
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


Mallen definierar de värden som tillåts för den här parametern (0, 1, 2, 3, 4, 5 eller 6) och tilldelar ett standardvärde (1) om inget värde anges. Dessa siffror motsvarar följande cache-storlekar: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB


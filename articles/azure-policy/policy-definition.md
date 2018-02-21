---
title: Azure principstruktur definition | Microsoft Docs
description: "Beskriver hur resurs principdefinitionen används av Azure principen för att etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och åtgärd att vidta."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: ffff4a663b64342142f42a662905a290044e2dfb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursdefinitionen princip som används av principen i Azure kan du etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och åtgärd att vidta. Du kan styra kostnader genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer är tillåtna. Eller, du kan kräva att alla resurser som har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip används för en resursgrupp, är det så gäller för alla resurser i resursgruppen.

Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

* läge
* parameters
* Visningsnamn
* description
* Principregel
  * logiska utvärdering
  * effekt

Till exempel visar följande JSON en princip som begränsar där resurser har distribuerats:

```json
{
  "properties": {
    "mode": "all",
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

Alla Azure princip mallen prover finns på [mallar för Azure princip](json-samples.md).

## <a name="mode"></a>Läge

Den **läge** bestämmer vilka typer av resurser som ska utvärderas för en princip. Läget stöds är:
* `all`: utvärderar resursgrupper och alla typer av resurser 
* `indexed`: endast utvärdera resurstyper som har stöd för etiketter och plats

Vi rekommenderar att du ställer in **läge** till `all`. Alla principdefinitioner som skapats via portalen användningen av `all` läge. Om du använder PowerShell eller Azure CLI, måste du ange den **läge** parametern och ange det till `all`. 

## <a name="parameters"></a>Parametrar

Parametrarna underlätta hantering av din genom att minska antalet principdefinitioner. Se parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar alltid samma, men deras värden ändras baserat på enskilda fyller i formuläret. Parametrarna fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

Du kan till exempel definiera en princip för en resursegenskap att begränsa de platser där resurser kan distribueras. I det här fallet, skulle du deklarera följande parametrar när du skapar din princip:


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

Typ av en parameter kan vara antingen sträng eller matris. Metadataegenskapen används för verktyg som Azure-portalen för att visa användarvänliga information.

Du kan använda i metadataegenskap **strongType** att tillhandahålla en flerval lista över alternativen i Azure-portalen.  Tillåtna värden för **strongType** nu:

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

I principregeln referera parametrar med följande syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Namn och beskrivning

Du kan använda **displayName** och **beskrivning** identifiera principdefinitionen och ge kontext för när den används.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block du definierar en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenario för en princip.

I den **sedan** block du definiera vad som händer när de **om** villkor är uppfyllda.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Logiska operatorer

Logiska operatorer som stöds är:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

Den **inte** syntax inverterar resultatet av villkoret. Den **allOf** syntax (liknar den logiska **och** åtgärden) kräver att alla villkor vara uppfyllda. Den **anyOf** syntax (liknar den logiska **eller** åtgärden) kräver en eller flera villkor vara uppfyllda.

Du kan kapsla logiska operatorer. Följande exempel visar en **inte** åtgärden som är kapslad i en **allOf** igen.

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Villkor

Utvärderar ett villkor om en **fältet** uppfyller vissa villkor. Villkor som stöds är:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

När du använder den **som** tillstånd, kan du ange ett jokertecken (*) i värdet.

När du använder den **matchar** villkor, ange `#` som representerar en siffra `?` för en bokstav och alla andra tecken som representerar det faktiska tecknet. Exempel finns i [godkända VM-avbildningar](scripts/allowed-custom-images.md).

### <a name="fields"></a>Fält
Villkor bildas genom att använda fält. Ett fält representerar egenskaper i nyttolasten för begäran resurs som används för att beskriva tillståndet för resursen.  

Följande fält stöds:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*`
* Egenskapen alias - lista, se [alias](#aliases).

### <a name="effect"></a>Verkan
Stöder följande typer av gälla:

* **Neka**: genererar en händelse i händelseloggen och misslyckas begäran
* **Granska**: genererar en varning-händelse i granskningsloggen men misslyckas inte begäran
* **Lägg till**: lägger till en definierad uppsättning fält i begäran
* **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
* **DeployIfNotExists**: distribuerar en resurs om den inte redan finns. Detta stöds för närvarande endast via inbyggda principer.

För **bifoga**, måste du ange följande information:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

Värdet kan vara en sträng eller ett JSON-format-objekt.

Med **AuditIfNotExists** och **DeployIfNotExists** du kan utvärdera förekomsten av en underordnad resurs och tillämpa en regel och en motsvarande effekt när resursen inte finns. Du kan till exempel kräva att en nätverksbevakaren distribueras för alla virtuella nätverk.
Ett exempel på granskning när ett tillägg för virtuell dator inte har distribuerats, se [granska om tillägg inte finns](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Alias

Du kan använda egenskapen alias för att få åtkomst till specifika egenskaper för en resurstyp. Alias kan du begränsa vilka värden eller villkor tillåts för en egenskap för en resurs. Varje alias mappar till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärdering av hämtar principmodulen egenskapssökvägen för den API-versionen.

**Microsoft.Cache/Redis**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Ange är om Redis-servern ssl-port (6379) aktiverat. |
| Microsoft.Cache/Redis/shardCount | Ange antalet shards som ska skapas på en Premium klustret Cache.  |
| Microsoft.Cache/Redis/sku.capacity | Ange storleken för Redis-cache för att distribuera.  |
| Microsoft.Cache/Redis/sku.family | Ange SKU-familjen att använda. |
| Microsoft.Cache/Redis/sku.name | Ange vilken typ av Redis-Cache för att distribuera. |

**Microsoft.Cdn/profiles**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Ange namnet på prisnivån. |

**Microsoft.Compute/disks**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Ange erbjudandet om plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imagePublisher | Ange utgivaren av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageSku | Ange SKU plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageVersion | Ange versionen av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |


**Microsoft.Compute/virtualMachines**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Compute/imageId | Ange identifieraren för den bild som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageOffer | Ange erbjudandet om plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imagePublisher | Ange utgivaren av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageSku | Ange SKU plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageVersion | Ange versionen av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/licenseType | Ange att avbildningen eller disken är licensierad lokalt. Det här värdet används bara för bilder som innehåller operativsystemet Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Ange erbjudandet om plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/virtualMachines/imagePublisher | Ange utgivaren av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/virtualMachines/imageSku | Ange SKU plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/virtualMachines/imageVersion | Ange versionen av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Ange vhd-URI. |
| Microsoft.Compute/virtualMachines/sku.name | Ange storleken på den virtuella datorn. |
| Microsoft.Compute/virtualMachines/availabilitySet.id | Anger tillgänglighetsuppsättning-id för den virtuella datorn. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Ange namnet på tilläggets utgivare. |
| Microsoft.Compute/virtualMachines/extensions/type | Ange vilken typ av tillägget. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Ange versionen av tillägget. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Compute/imageId | Ange identifieraren för den bild som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageOffer | Ange erbjudandet om plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imagePublisher | Ange utgivaren av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageSku | Ange SKU plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/imageVersion | Ange versionen av plattformsavbildning eller marketplace-avbildning som används för att skapa den virtuella datorn. |
| Microsoft.Compute/licenseType | Ange att avbildningen eller disken är licensierad lokalt. Det här värdet används bara för bilder som innehåller operativsystemet Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Ange prefixet för alla virtuella datorer i skaluppsättning. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Ange blob-URI för användaravbildning. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Ange behållare webbadresserna som används för att lagra operativsystemet diskar för skalan. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Ange storleken på virtuella datorer i en skaluppsättning. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Ange nivån för virtuella datorer i en skaluppsättning. |

**Microsoft.Network/applicationGateways**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Ange storleken för gatewayen. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Ange vilken typ av den här virtuella nätverksgatewayen. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Ange namnet på gateway-SKU. |

**Microsoft.Sql/servers**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Ange versionen av servern. |

**Microsoft.Sql/databases**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Ange versionen av databasen. |
| Microsoft.Sql/servers/databases/elasticPoolName | Ange namnet på den elastiska poolen databasen är i. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Ange konfigurerade tjänsten nivå mål-ID för databasen. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Ange namnet på det konfigurerade servicenivåmålet för databasen.  |

**Microsoft.Sql/elasticpools**

| Alias | Beskrivning |
| ----- | ----------- |
| servrar/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Ange totalt antal delade DTU för elastiska databaspool. |
| servrar/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Ange version för den elastiska poolen. |

**Microsoft.Storage/storageAccounts**

| Alias | Beskrivning |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Ange den åtkomstnivå som används för fakturering. |
| Microsoft.Storage/storageAccounts/accountType | Ange namnet på SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Ange om tjänsten krypterar data som lagras i blob storage-tjänst. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Ange om tjänsten krypterar data som lagras i tjänsten file storage. |
| Microsoft.Storage/storageAccounts/sku.name | Ange namnet på SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Ange för att tillåta endast https-trafik till storage-tjänst. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Kontrollera om tjänstslutpunkten för virtuella nätverk har aktiverats. |

## <a name="initiatives"></a>Initiativ

Initiativ aktivera du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enskilt objekt. Du kan till exempel gruppera alla relaterade märkning principdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt kan använda du initiativ.

Följande exempel illustrerar hur du skapar ett initiativ för att hantera två taggar: `costCenter` och `productName`. Två inbyggda principer används för att tillämpa taggen standardvärdet.


```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Nästa steg

- Granska Azure princip mallen exemplen på [mallar för Azure princip](json-samples.md).

---
title: Azure resursprinciper | Microsoft Docs
description: "Beskriver hur du använder Azure Resource Manager principer för att säkerställa konsekvent resursegenskaper anges under distributionen. Principer kan tillämpas på grupperna prenumerationen eller resursen."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: cfdbf35b76b6a7f3cddb2deb35dfc475e0fc600f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resource-policy-overview"></a>Översikt över princip för resurs
Principer för företagsresurser kan du etablera konventioner för resurser i din organisation. Du kan styra kostnader genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer är tillåtna. Eller, du kan kräva att alla resurser som har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip används för en resursgrupp, är det så gäller för alla resurser i resursgruppen.

Det finns två begrepp att förstå om principer:

* principdefinitionen - beskrivs när principen tillämpas och åtgärd att vidta
* tilldelning av principer - installation av principdefinitionen till ett omfång (prenumerationen eller resursen grupp)

Det här avsnittet fokuserar på principdefinitionen. Information om tilldelning av principer finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md) eller [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md).

Principer utvärderas när du skapar och uppdaterar resurser (PLACERA och korrigering operations).

## <a name="how-is-it-different-from-rbac"></a>Hur skiljer den från RBAC?
Det finns några viktiga skillnader mellan principen och rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på **användaren** åtgärder på olika omfång. Till exempel har också lagts till deltagarrollen för en resursgrupp i det önskade omfånget, så du kan göra ändringar till resursgruppen. Principen fokuserar på **resurs** egenskaper under distributionen. Du kan till exempel styra vilka typer av resurser som kan etableras via principer. Eller så kan du begränsa de platser där resurser kan etableras. Till skillnad från RBAC, principen är en standard Tillåt och explicit neka system. 

Om du vill använda principer måste du autentiseras via RBAC. Mer specifikt måste ditt konto den:

* `Microsoft.Authorization/policydefinitions/write`behörighet att definiera en princip
* `Microsoft.Authorization/policyassignments/write`behörighet att tilldela en princip 

Dessa behörigheter ingår inte i den **deltagare** roll.

## <a name="built-in-policies"></a>Inbyggda principer

Azure tillhandahåller vissa inbyggda principdefinitioner som kan minska antalet principer måste du definiera. Innan du fortsätter med principdefinitioner, bör du överväga om en princip för inbyggda redan innehåller definitionen som du behöver. De inbyggda principdefinitioner är:

* Tillåtna platser
* Tillåtna resurstyper
* Tillåtna lagringskonto SKU: er
* Tillåtna virtuella SKU: er
* Värdet för taggen och standard
* Framtvinga taggen och värdet
* Inte tillåtet resurstyper
* Kräv SQL Server version 12.0
* Kräv kryptering för storage-konto

Du kan tilldela någon av dessa policys via den [portal](resource-manager-policy-portal.md), [PowerShell](resource-manager-policy-create-assign.md#powershell), eller [Azure CLI](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Definition av principstruktur
Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

* Läge
* parameters
* Visningsnamn
* description
* Principregel
  * logiska utvärdering
  * effekt

I följande exempel visas en princip som begränsar där resurser har distribuerats:

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

## <a name="mode"></a>Läge

Vi rekommenderar att du ställer in `mode` till `all`. När du anger till **alla**, resursgrupper och alla resurstyper utvärderas för principen. Portalen använder **alla** för alla principer. Om du använder PowerShell eller Azure CLI, måste du ange den `mode` parametern och ange det till **alla**.
 
Tidigare utvärderades principen om endast resurstyper som stöds taggar och plats. Den `indexed` läge fortsätter problemet. Om du använder den **alla** läge principer också utvärderas på resurstyper som inte stöder taggar och plats. [Undernät för virtuellt nätverk](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) är ett exempel på en nyligen tillagd typen. Dessutom resursgrupper utvärderas när läget är inställt på **alla**. Du kan till exempel [genomdriva taggarna i en resursgrupp](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Parametrar
Med parametrar kan förenkla din för principhantering genom att minska antalet principdefinitioner. Definiera en princip för en resursegenskap (till exempel att begränsa de platser där resurser kan distribueras) och inkludera parametrar i definitionen. Sedan kan du återanvända den principdefinitionen för olika scenarier genom att passera i olika värden (t.ex att ange en uppsättning platser för en prenumeration) när tilldelade principen.

Du kan ange parametrar när du skapar principdefinitioner.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

Typ av en parameter kan vara antingen sträng eller matris. Metadataegenskapen används för verktyg som Azure-portalen för att visa användarvänliga information. 

I principregeln referera parametrar med följande syntax: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Namn och beskrivning

Du använder den **displayName** och **beskrivning** identifiera principdefinitionen och ge kontext för när den används.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block du definierar en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenario för en princip. I den **sedan** block du definiera vad som händer när de **om** villkor är uppfyllda.

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
De logiska operatorerna som stöds är:

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
Villkoret utvärderas om en **fältet** uppfyller vissa villkor. Villkor som stöds är:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

När du använder den **som** tillstånd, kan du ange ett jokertecken (*) i värdet.

När du använder den **matchar** villkor, ange `#` som representerar en siffra `?` för en bokstav och alla andra tecken som representerar det faktiska tecknet. Exempel finns i [gäller resursprinciper för namn och text](resource-manager-policy-naming-convention.md).

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
Stöder tre typer av effekt - `deny`, `audit`, `append`, `AuditIfNotExists`, och `DeployIfNotExists`. 

* **Neka** genererar en händelse i händelseloggen och misslyckas begäran
* **Granska** genererar en varning-händelse i granskningsloggen men misslyckas inte begäran
* **Lägg till** lägger till en definierad uppsättning fält i begäran 
* **AuditIfNotExists** -aktivera granskning om en resurs inte finns
* **DeployIfNotExists** – distribuerar en resurs om den inte redan finns. Detta stöds för närvarande endast via inbyggda principer.

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

Med **AuditIfNotExists** och **DeployIfNotExists**, du kan utvärdera förekomsten av en underordnad resurs och tillämpa en regel när resursen inte finns. Du kan till exempel kräva att en nätverksbevakaren distribueras för alla virtuella nätverk.

Ett exempel på granskning när ett tillägg för virtuell dator inte har distribuerats, se [Audit VM-tillägg](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

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

## <a name="policy-sets"></a>Principen anger

Principen anger kan du gruppera flera relaterade principdefinitioner. Principen förenklar tilldelning och hantering eftersom du arbetar med gruppen som ett enskilt objekt. Du kan till exempel gruppera alla relaterade märkning principer i en enda princip. I stället för att tilldela varje princip individuellt kan tillämpa du principen.
 
Följande exempel illustrerar hur du skapar en princip för hantering av två taggar (costCenter och productName). Två inbyggda principer används för att använda standardvärdet för taggen och genomdriva Taggvärdet. Principen deklarerar två parametrar, costCenterValue och productNameValue för återanvändning. Den refererar till två inbyggda principdefinitioner flera gånger med olika parametrar. Du kan antingen ange ett fast värde som visas för tagName eller ange en parameter från principen som visas för tagValue för varje parameter.

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

Du lägger till en princip med de **ny AzureRMPolicySetDefinition** PowerShell-kommando.

REST-åtgärder, Använd den **2017-06-01-preview** API-versionen som visas i följande exempel:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Nästa steg
* När du har definierat en regel för att tilldela det till ett omfång. Om du vill tilldela principer via portalen finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Om du vill tilldela principer via REST-API, PowerShell eller Azure CLI, se [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md).
* Till exempel principer, se [GitHub-lagringsplatsen för Azure-resurs princip](https://github.com/Azure/azure-policy-samples).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
* Princip-schemat har publicerats på [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


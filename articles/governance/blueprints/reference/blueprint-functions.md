---
title: Azure functions för skisser
description: Beskriver funktionerna för användning med Azure skisser definitioner och tilldelningar.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209405"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funktioner för användning med Azure skisser

Azure skisser innehåller funktioner som gör en skissdefinitionen mer dynamiska. Dessa funktioner är för användning med skissdefinitioner och skiss artefakter. En Resource Manager-mall-artefakt stöder fullständig användning av Resource Manager-funktioner utöver att hantera ett dynamiskt värde en skiss-parameter.

Följande funktioner stöds:

- [Artefakter](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [ResourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [prenumeration](#subscription)

## <a name="artifacts"></a>Artefakter

`artifacts(artifactName)`

Returnerar ett objekt av egenskaper som fyllts med de skissartefakter utdata.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| artifactName |Ja |string |Namnet på en skissartefakten. |

### <a name="return-value"></a>Returvärde

Ett objekt av output-egenskaper. Den **matar ut** egenskaper är beroende på vilken typ av skissartefakten som refereras. Alla typer av följer formatet:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Princip för tilldelning av artefakt

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakten för Resource Manager-mall

Den **matar ut** egenskaperna för det returnerade objektet definieras i Resource Manager-mallen och returneras av distributionen.

#### <a name="role-assignment-artifact"></a>Rolltilldelningsartefakten

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exempel

En Resource Manager-mall-artefakt med ID _myTemplateArtifact_ som innehåller följande exempel utdata egenskapen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Några exempel på hämtning av data från den _myTemplateArtifact_ exempel är:

| Uttryck | Typ | Värde |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matris | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | ”första” |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | ”Min sträng-value” |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {”myproperty”: ”min value”, ”anotherProperty”: true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | ”Min value” |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Booleskt | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Kombinerar flera strängvärden och returnerar en sammanfogad sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| sträng1 |Ja |string |Det första värdet för sammanfogning. |
| ytterligare argument |Nej |string |Ytterligare värden i sekventiell ordning för sammanfogning |

### <a name="return-value"></a>Returvärde

En sträng med sammansatta värden.

### <a name="remarks"></a>Kommentarer

Funktionen Azure skissen skiljer sig från funktionen Azure Resource Manager-mall i att det fungerar bara med strängar.

### <a name="example"></a>Exempel

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Returnerar ett värde för parametern skissen. Det angivna parameternamnet måste definieras i skissdefinitionen eller skissartefakter.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |string |Namnet på parametern för att returnera. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna skissen eller artefakt skissparametern.

### <a name="remarks"></a>Kommentarer

Funktionen Azure skissen skiljer sig från funktionen Azure Resource Manager-mall i att det fungerar bara med skissparametrar.

### <a name="example"></a>Exempel

Definiera parametern _principalIds_ i skissdefinition:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Använd sedan _principalIds_ som argument för `parameters()` i en skissartefakten:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Returnerar ett objekt som representerar den aktuella resursgruppen.

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Kommentarer

Funktionen Azure skissen skiljer sig från funktionen Azure Resource Manager-mall. Den `resourceGroup()` funktionen kan inte användas i en prenumeration på artefakt eller skissdefinitionen. Det kan bara användas i skissartefakter som ingår i en resource group-artefakt.

Ett vanligt användningsområde för den `resourceGroup()` funktion är att skapa resurser i samma plats som resource group artefakten.

### <a name="example"></a>Exempel

Om du vill använda resursgruppens plats, ange antingen skissdefinitionen eller under tilldelning som platsen för en annan artefakt deklarera ett resurs platshållaren gruppobjekt i din skissdefinitionen. I det här exemplet _NetworkingPlaceholder_ är namnet på resursen grupp platshållaren.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Använd sedan den `resourceGroup()` funktion i kontexten för en skissartefakten som är mål för en platshållare för resursen gruppobjekt. I det här exemplet mall artefakten distribueras till den _NetworkingPlaceholder_ resursgrupp och ger parametern _resourceLocation_ dynamiskt fylls i automatiskt med den  _NetworkingPlaceholder_ resursgruppens plats för mallen. Platsen för den _NetworkingPlaceholder_ resursgrupp kan ha statiskt definieras för skissdefinitionen eller dynamiskt definierade under tilldelning. I båda fallen artefakten mall har angetts som en parameter och används för att distribuera resurserna till rätt plats.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Returnerar ett objekt som representerar den angivna resurs grupp-artefakten. Till skillnad från `resourceGroup()`, vilket kräver kontexten för artefakten, den här funktionen används för att hämta egenskaperna för en specifik resurs grupp platshållare när inte i kontexten för resursgruppen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| placeholderName |Ja |string |Platshållarnamnet på resursen grupp artefakten ska returneras. |

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exempel

Om du vill använda resursgruppens plats, ange antingen skissdefinitionen eller under tilldelning som platsen för en annan artefakt deklarera ett resurs platshållaren gruppobjekt i din skissdefinitionen. I det här exemplet _NetworkingPlaceholder_ är namnet på resursen grupp platshållaren.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Använd sedan den `resourceGroups()` funktion från kontexten för alla skissartefakten att hämta en referens till platshållare för resursen gruppobjekt. I det här exemplet med mallen artefakten utanför den _NetworkingPlaceholder_ resursgrupp och ger parametern _artifactLocation_ dynamiskt fylls i automatiskt med den  _NetworkingPlaceholder_ resursgruppens plats för mallen. Platsen för den _NetworkingPlaceholder_ resursgrupp kan ha statiskt definieras för skissdefinitionen eller dynamiskt definierade under tilldelning. I båda fallen artefakten mall har angetts som en parameter och används för att distribuera resurserna till rätt plats.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>prenumeration

`subscription()`

Returnerar information om prenumerationen för den aktuella skisstilldelningen för.

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exempel

Använda prenumerationens namn och `concat()` att skapa en namngivningskonvention som angavs som parameter _resourceName_ till mall-artefakt.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).
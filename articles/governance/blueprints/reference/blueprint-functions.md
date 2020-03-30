---
title: Azure Blueprints-funktioner
description: Beskriver de funktioner som är tillgängliga för användning med skissartefakter i Definitioner och tilldelningar i Azure Blueprints.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280682"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funktioner för användning med Azure Blueprints

Azure Blueprints tillhandahåller funktioner som gör en skissdefinition mer dynamisk. Dessa funktioner är för användning med skissdefinitioner och skissartefakter. En Resource Manager-mallartefakt stöder full användning av Resource Manager-funktioner förutom att få ett dynamiskt värde via en skissparameter.

Följande funktioner stöds:

- [Artefakter](#artifacts)
- [Concat](#concat)
- [Parametrar](#parameters)
- [resursGrupp](#resourcegroup)
- [resursGrupper](#resourcegroups)
- [Prenumeration](#subscription)

## <a name="artifacts"></a>Artefakter

`artifacts(artifactName)`

Returnerar ett objekt med egenskaper som fylls med att skissartefakter utdata.

> [!NOTE]
> Funktionen `artifacts()` kan inte användas inifrån en Resource Manager-mall. Funktionen kan bara användas i skissdefinitionen JSON eller i artefakten JSON när skissen hanteras med Azure PowerShell eller REST API som en del av [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| artifactName (artefaktNamn) |Ja |sträng |Namnet på en skissartefakt. |

### <a name="return-value"></a>Returvärde

Ett objekt med utdataegenskaper. **Utdataegenskaperna** är beroende av vilken typ av skissartefakt som refereras. Alla typer följer formatet:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefakt för principtilldelning

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefakt för Resource Manager-mall

**Utdataegenskaperna** för det returnerade objektet definieras i resource manager-mallen och returneras av distributionen.

#### <a name="role-assignment-artifact"></a>Artefakt för rolltilldelning

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

En Resource Manager-mallartefakt med ID _myTemplateArtifact_ som innehåller följande exempelutdataegenskap:

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

Några exempel på att hämta data från _myTemplateArtifact-exemplet_ är:

| Uttryck | Typ | Värde |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matris | \["första", "andra"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "först" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "mitt strängvärde" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objekt | { "myproperty": "mitt värde", "anotherProperty": sant } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "Mitt värde" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Kombinerar flera strängvärden och returnerar den sammanfogade strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| sträng1 |Ja |sträng |Det första värdet för sammanfogning. |
| ytterligare argument |Inga |sträng |Ytterligare värden i sekventiell ordning för sammanfogning |

### <a name="return-value"></a>Returvärde

En sträng med sammanfogade värden.

### <a name="remarks"></a>Anmärkningar

Azure Blueprint-funktionen skiljer sig från Azure Resource Manager-mallfunktionen genom att den bara fungerar med strängar.

### <a name="example"></a>Exempel

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Returnerar ett skissparametervärde. Det angivna parameternamnet måste definieras i skissdefinitionen eller i skissartefakter.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName (parameterName) |Ja |sträng |Namnet på parametern som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna skiss- eller skissartefaktparametern.

### <a name="remarks"></a>Anmärkningar

Azure Blueprint-funktionen skiljer sig från Azure Resource Manager-mallfunktionen genom att den bara fungerar med skissparametrar.

### <a name="example"></a>Exempel

Definiera _parameterprincipIds_ i skissdefinitionen:

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

Använd sedan _principalIds_ som `parameters()` argument för i en skissartefakt:

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

### <a name="remarks"></a>Anmärkningar

Azure Blueprint-funktionen skiljer sig från mallfunktionen Azure Resource Manager. Funktionen `resourceGroup()` kan inte användas i en prenumerationsnivåartefakt eller skissdefinitionen. Den kan bara användas i skissartefakter som ingår i en resursgruppartefakt.

En vanlig användning `resourceGroup()` av funktionen är att skapa resurser på samma plats som resursgruppartefakten.

### <a name="example"></a>Exempel

Om du vill använda resursgruppens plats anger du i skissdefinitionen eller under tilldelningen, som plats för en annan artefakt, ett platshållarobjekt för resursgrupp i skissdefinitionen. I det här exemplet är _NetworkingPlaceholder_ namnet på resursgruppens platshållare.

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

Använd sedan `resourceGroup()` funktionen i kontexten för en skissartefakt som riktar sig till ett platshållarobjekt för en resursgrupp. I det här exemplet distribueras mallartefakten till resursgruppen _Nätverkspositionshållare_ och tillhandahåller _parameterresursLocation_ dynamiskt ifylld med resursgruppen Nätverksställe till resursgruppen för _nätverkspositionshållare_ till mallen. Platsen för resursgruppen _Nätverksställebehållar_ kan ha definierats statiskt i skissdefinitionen eller dynamiskt definierats under tilldelningen. I båda fallen tillhandahålls mallartefakten den informationen som en parameter och använder den för att distribuera resurserna till rätt plats.

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

## <a name="resourcegroups"></a>resursGrupper

`resourceGroups(placeholderName)`

Returnerar ett objekt som representerar den angivna resursgruppartefakten. Till `resourceGroup()`skillnad från , som kräver kontext för artefakten, används den här funktionen för att hämta egenskaperna för en specifik resursgruppplatshållare när den inte är i kontexten för den resursgruppen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| platsholderName |Ja |sträng |Platshållarnamnet på den resursgruppsartefakt som ska returneras. |

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exempel

Om du vill använda resursgruppens plats anger du i skissdefinitionen eller under tilldelningen, som plats för en annan artefakt, ett platshållarobjekt för resursgrupp i skissdefinitionen. I det här exemplet är _NetworkingPlaceholder_ namnet på resursgruppens platshållare.

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

Använd sedan `resourceGroups()` funktionen från kontexten för en skissartefakt för att få en referens till platshållarobjektet för resursgruppen. I det här exemplet distribueras mallartefakten utanför resursgruppen _Nätverksbehållarresurs_ och ger _parameterartefaktplats_ dynamiskt ifylld med resursgruppen Nätverksställe till resursgruppen _nätverkspositionshållare_ till mallen. Platsen för resursgruppen _Nätverksställebehållar_ kan ha definierats statiskt i skissdefinitionen eller dynamiskt definierats under tilldelningen. I båda fallen tillhandahålls mallartefakten den informationen som en parameter och använder den för att distribuera resurserna till rätt plats.

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

Returnerar information om prenumerationen för den aktuella skisstilldelningen.

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

Använd prenumerationens visningsnamn `concat()` och funktionen för att skapa en namngivningskonvention som skickas som parameter _resourceName_ till mallartefakten.

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

- Läs mer om [skisslivscykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).
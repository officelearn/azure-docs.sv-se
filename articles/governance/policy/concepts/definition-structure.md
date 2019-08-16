---
title: Information om princip definitions strukturen
description: Beskriver hur resource principdefinitionen används av Azure Policy för att etablera konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken effekt ska börja.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 44bf3171f9da73dac17b29e86c80fc8f0d011498
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557928"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursen principdefinitioner används av Azure Policy för att etablera konventioner för resurser. Varje definition beskriver resurskompatibilitet och vad i kraft för att vidta när en resurs är inkompatibla.
Du kan styra kostnaderna genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att bara vissa typer av virtuella datorer är tillåtna. Eller så kan du kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resursgrupp, kan den användas för alla resurser i resursgruppen.

Ett schema som används av Azure Policy finns här: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

- läge
- parameters
- Visningsnamn
- beskrivning
- principregel
  - logiska utvärdering
  - effekt

Följande JSON visar exempelvis en princip som begränsar där resurser har distribuerats:

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
                },
                "defaultValue": [ "westus2" ]
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

Alla Azure Policys exempel finns på [Azure policy exempel](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Läge

**Läge** konfigureras beroende på om principen är riktad mot en Azure Resource Manager-egenskap eller en resurs leverantörs egenskap.

### <a name="resource-manager-modes"></a>Lägen i Resource Manager

Den **läge** avgör vilka typer av resurser kommer att utvärderas för en princip. De stödda lägena är:

- `all`: utvärdera resursgrupper och alla resurstyper
- `indexed`: endast utvärdera resurstyper som stöder taggar och plats

Vi rekommenderar att du ställer in **läge** till `all` i de flesta fall. Alla principdefinitioner som skapats via portalen användning i `all` läge. Om du använder PowerShell eller Azure CLI kan du ange den **läge** parametern manuellt. Om principdefinitionen inte innehåller en **läge** , den standardvärdet `all` i Azure PowerShell och till `null` i Azure CLI. En `null` läge är detsamma som att använda `indexed` att stödja bakåtkompatibilitet kompatibilitet.

`indexed` ska användas när du skapar principer som tillämpar taggar eller platser. Även om det inte krävs, förhindrar det att resurser som inte stöder taggar och platser visas som icke-kompatibla i resultatet av efterlevnaden. Undantaget är **resursgrupper**. Principer som framtvinga plats eller taggar på en resursgrupp bör ange **läge** till `all` och specifikt mål den `Microsoft.Resources/subscriptions/resourceGroups` typen. Ett exempel finns i [framtvinga grupp resurstaggar](../samples/enforce-tag-rg.md). En lista över resurser som stöder taggar finns i [tagga stöd för Azure-resurser](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Resurs leverantörs lägen

Det enda resurs leverantörs läge som stöds `Microsoft.ContainerService.Data` för närvarande är för att hantera regler för regler för åtkomst kontroll i [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för Kubernetes](rego-for-aks.md) finns i en offentlig för hands version och stöder bara inbyggda princip definitioner.

## <a name="parameters"></a>Parametrar

Parametrar underlätta hanteringen av principer genom att minska antalet principdefinitioner. Tänk på parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar desamma alltid, men deras värden ändras baserat på enskilda fyller i formuläret.
Parametrar fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

> [!NOTE]
> Parametrar kan läggas till i en befintlig och tilldelad definition. Den nya parametern måste innehålla egenskapen **DefaultValue** . Detta förhindrar att befintliga tilldelningar för principen eller initiativ indirekt görs ogiltig.

### <a name="parameter-properties"></a>Parameter egenskaper

En parameter har följande egenskaper som används i princip definitionen:

- **name**: Parameterns namn. Används av `parameters` distributions funktionen i princip regeln. Mer information finns i [använda ett parameter värde](#using-a-parameter-value).
- `type`: Anger om parametern är en **sträng**, en **matris**, ett **objekt**, ett **booleskt värde**, ett **flyttal**eller en **datetime**.
- `metadata`: Definierar under egenskaper som främst används av Azure Portal för att Visa användarvänlig information:
  - `description`: En förklaring av vad parametern används för. Kan användas för att ge exempel på acceptabla värden.
  - `displayName`: Det egna namnet som visas i portalen för-parametern.
  - `strongType`: Valfritt Används när du tilldelar princip definitionen via portalen. Innehåller en Sammanhangs medveten lista. Mer information finns i [strongType](#strongtype).
  - `assignPermissions`: Valfritt Ange som _Sant_ om du vill att Azure Portal skapa roll tilldelningar under princip tilldelning. Den här egenskapen är användbar om du vill tilldela behörigheter utanför tilldelnings omfånget. Det finns en roll tilldelning per roll definition i principen (eller per roll definition i alla principer i initiativet). Parametervärdet måste vara en giltig resurs eller ett giltigt omfång.
- `defaultValue`: Valfritt Anger värdet för parametern i en tilldelning om inget värde anges.
  Krävs när du uppdaterar en befintlig princip definition som är tilldelad.
- `allowedValues`: Valfritt Innehåller en matris med värden som parametern accepterar under tilldelningen.

Du kan till exempel definiera en princip definition för att begränsa de platser där resurser kan distribueras. En parameter för den princip definitionen kan vara **allowedLocations**. Den här parametern används av varje tilldelning av princip definitionen för att begränsa de godkända värdena. Användningen av **strongType** ger en bättre upplevelse när du slutför tilldelningen via portalen:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Använda ett parameter värde

I principregeln du referera till parametrar med följande `parameters` distribution värdet funktionens syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till den **allowedLocations** -parameter som visades i [parameter egenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I egenskapen kan du använda strongType för att ange en lista med alternativ för flera val i Azure Portal. `metadata` Tillåtna värden för **strongType** nu:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definitionens plats

När du skapar ett initiativ- eller är det nödvändigt att ange platsen för definition. Definitionens plats måste vara en hanteringsgrupp eller en prenumeration. Den här platsen anger omfånget som den initiativ- eller kan tilldelas. Resurser måste vara direkta medlemmar i eller underordnade objekt inom hierarkin för definitionens plats för att rikta för tilldelning.

Om den definition lagras a:

- **Prenumeration** – endast resurser inom den prenumerationen kan tilldelas principen.
- **Hanteringsgruppen** – endast resurser i underordnade hanteringsgrupper och underordnade prenumerationer kan du tilldela principen. Om du planerar att använda principdefinitionen till flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller dessa prenumerationer.

## <a name="display-name-and-description"></a>Namn och beskrivning

Du använder **displayName** och **beskrivning** identifiera principdefinitionen och ge ett sammanhang för när den används. **DisplayName** får innehålla högst _128_ tecken och **beskrivningen** får bestå av högst _512_ tecken.

## <a name="policy-rule"></a>Principregel

Principregeln består av **om** och **sedan** block. I den **om** block, definierar du en eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att ange exakt scenariot för en princip.

I den **sedan** block, definierar du den effekt som händer när den **om** villkor är uppfyllda.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logiska operatorer

Logiska operatorer som stöds är:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Den **inte** syntax inverterar resultatet av villkoret. Den **allOf** syntax (liknar den logiska **och** åtgärden) kräver att alla villkor vara uppfyllda. Den **anyOf** syntax (liknar den logiska **eller** åtgärden) kräver en eller flera villkor vara uppfyllda.

Du kan kapsla logiska operatorer. I följande exempel visas en **inte** åtgärd som är kapslade i en **allOf** igen.

```json
"if": {
    "allOf": [{
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

Ett villkor utvärderar om ett **fält** eller **värde** accessor uppfyller vissa villkor. Villkor som stöds är:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

När du använder den **som** och **notLike** villkor du anger ett jokertecken `*` i värdet.
Värdet får inte innehålla fler än ett jokertecken `*`.

När du använder **matchnings** -och **notMatch** - `#` villkor, anger du för `?` att matcha en siffra `.` , för en bokstav, för att matcha alla tecken och andra tecken som ska matcha det faktiska tecknet.
**matchnings** -och **notMatch** är Skift läges känsliga. Skift läges känsliga alternativ är tillgängliga i **matchInsensitively** och **notMatchInsensitively**. Exempel finns i [Tillåt flera namn mönster](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fält

Villkor bildas genom att använda fält. Ett fält matchar egenskaper i nyttolasten i begäran av resursen och beskriver tillståndet för resursen.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar det fullständiga namnet på resursen. Det fullständiga namnet på en resurs är resursnamnet föregås av ett anpassningsprefix av alla överordnade resursnamn (till exempel ”minserver/myDatabase”).
- `kind`
- `type`
- `location`
  - Använd **Global** för resurser som är plats oberoende. Ett exempel finns i [samples-tillåtna platser](../samples/allowed-locations.md).
- `identity.type`
  - Returnerar typen av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) som är aktive rad på resursen.
- `tags`
- `tags['<tagName>']`
  - Den här klammerns syntax stöder taggnamn som har skiljetecken, till exempel bindestreck, punkter eller blank steg.
  - Där **\<tagName\>** är namnet på taggen för att verifiera villkoret för.
  - Exempel: `tags['Acct.CostCenter']` där **acct. CostCenter** är namnet på taggen.
- `tags['''<tagName>''']`
  - Den här klammerns syntax stöder taggnamn som har apostrofer i den genom att använda dubbla apostrofer.
  - Där **"\<TagName\>"** är namnet på taggen som verifierar villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **'\<TagName\>'** är namnet på taggen.
- Egenskapen alias – en lista i [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, och `tags[tag.with.dots]` är fortfarande acceptabla sätt att deklarera ett Tags-fält. De prioriterade uttrycken är dock de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parameter värde kan skickas till ett tagg-fält. Att skicka en parameter till ett taggnamn ökar flexibiliteten i princip definitionen under princip tilldelning.

I följande exempel `concat` används för att skapa ett fält uppslag för taggen som heter värdet för **TagName** -parametern. Om taggen inte finns används Lägg till -resultatet för att lägga till taggen med värdet för samma namngivna tagg uppsättning på den överordnade resurs gruppen granskade resurser med hjälp `resourcegroup()` av funktionen lookup.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Value

Villkor kan även skapas med hjälp av **värde**. **värde** kontrollerar villkor mot [parametrar](#parameters), [mall funktioner som stöds](#policy-functions)eller litteraler.
**värdet** kombineras med alla [villkor](#conditions)som stöds.

> [!WARNING]
> Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är enimplicit nekande. Mer information finns i [undvika mall](#avoiding-template-failures)-haverier.

#### <a name="value-examples"></a>Värde exempel

I den här princip regel exemplet används **värde** för att jämföra resultatet `resourceGroup()` av funktionen `*netrg`och egenskapen returnerat **namn** till ett **like** -villkor. Regeln nekar en resurs som inte är av `Microsoft.Network/*` **typen** i någon resurs grupp vars namn slutar med `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Den här princip regel exemplet använder **värdet** för att kontrol lera om resultatet av flera kapslade funktioner `true` **är lika med** . Regeln nekar en resurs som inte har minst tre taggar.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Undvika synkroniseringsfel

Användningen av _Template Functions_ i **Value** tillåter många komplexa kapslade funktioner. Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är enimplicit nekande. Ett exempel på ett **värde** som inte fungerar i vissa scenarier:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Exempel princip regeln ovan använder [del sträng ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) för att jämföra de tre första tecken **namnen** med **ABC**. Om **namnet** är kortare än tre tecken `substring()` resulterar funktionen i ett fel. Det här felet gör att principen blir en **neka** -påverkan.

Använd i stället funktionen [IF ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) för att kontrol lera om de tre första tecknen i **namn** är lika med **ABC** utan att ett **namn** som är kortare än tre tecken kan orsaka ett fel:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Med den reviderade princip regeln `if()` kontrollerar **namnet på namnet** innan det försöker hämta ett `substring()` värde med färre än tre tecken. Om **namnet** är för kort returneras värdet "inte börjar med ABC" i stället och jämförs med **ABC**. En resurs med ett kort namn som inte börjar med **ABC** kan fortfarande inte utföra princip regeln, men inte längre orsaka ett fel under utvärderingen.

### <a name="effect"></a>Verkan

Azure Policy stöder följande typer av påverkan:

- **Neka**: Generera en händelse i aktivitetsloggen och misslyckas begäran
- **Granska**: genererar en varning-händelse i aktivitetsloggen men inte misslyckas begäran
- **Lägg till**: lägger till en definierad uppsättning fält på begäran
- **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
- **DeployIfNotExists**: distribuerar en resurs om den inte redan finns
- **Inaktiverad**: inte utvärdera resurser för principregeln
- **EnforceRegoPolicy**: konfigurerar kontrollanten för öppen Policy Agent inspelare i Azure Kubernetes-tjänsten (för hands version)

För **lägga till**, måste du ange följande information:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Värdet kan vara en sträng eller ett JSON-format-objekt.

**AuditIfNotExists** och **DeployIfNotExists** utvärdera förekomsten av en relaterad resurs och tillämpa en regel. Om resursen inte matchar regeln, implementeras effekten. Du kan till exempel kräva att en nätverksbevakare distribueras för alla virtuella nätverk. Mer information finns i den [granska om tillägg inte finns](../samples/audit-ext-not-exist.md) exempel.

Den **DeployIfNotExists** effekt kräver den **roleDefinitionId** -egenskapen i den **information** delen av principregeln. Mer information finns i [reparation – konfigurera principdefinitionen](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Fullständig information om varje effekt, utvärderings ordning, egenskaper och exempel finns i [förstå Azure policys effekter](effects.md).

### <a name="policy-functions"></a>Princip fungerar

Alla [funktioner i Resource Manager-mallar](../../../azure-resource-manager/resource-group-template-functions.md) är tillgängliga för användning i en princip regel, förutom följande funktioner och användardefinierade funktioner:

- copyIndex()
- distribution ()
- lista
- newGuid()
- pickZones()
- providers ()
- referens ()
- resourceId ()
- variabler ()

Följande funktioner är tillgängliga för användning i en princip regel, men skiljer sig från användningen i en Azure Resource Manager-mall:

- addDays (dateTime, numberOfDaysToAdd)
  - **datetime**: [required] sträng sträng i Universal ISO 8601 datetime-formatet ' ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffZ '
  - **numberOfDaysToAdd**: [required] heltal-antal dagar som ska läggas till
- utcNow () – till skillnad från en Resource Manager-mall kan detta användas utanför defaultValue.
  - Returnerar en sträng som har angetts till aktuellt datum och aktuell tid i universellt ISO 8601 DateTime-format ' ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffZ '

Dessutom kan den `field` funktionen är tillgänglig för hanteringsprincipregler (MPR). `field` används främst med **AuditIfNotExists** och **DeployIfNotExists** till referensfält på resursen som utvärderas. Ett exempel på den här användningen visas på den [DeployIfNotExists exempel](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Exempel på princip funktion

Den här principen regelexempel använder den `resourceGroup` resurs-funktionen för att hämta den **namn** egenskapen tillsammans med den `concat` matris och objekt-funktionen för att skapa en `like` villkor som tillämpar resursnamnet att starta med resursgruppens namn.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Alias

Du kan använda egenskapen alias för att få åtkomst till specifika egenskaper för en resurstyp. Alias kan du begränsa vilka värden eller villkor är tillåtna för en egenskap för en resurs. Varje alias som mappas till sökvägar i olika API-versioner för en viss resurstyp. Under principutvärdering hämtar principmodulen egenskapssökvägen för den API-versionen.

Lista över alla alias växer. Använd någon av följande metoder för att hitta vilka alias är för närvarande stöds av Azure Policy:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST-API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Förstå alias [*]

Flera av de alias som är tillgängliga har en version som visas som ett ”normal” namn och en annan som har **[\*]** kopplade till den. Exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Aliaset "normal" representerar fältet som ett enda värde. Det här fältet är för exakta matchnings scenarier när hela uppsättningen med värden måste vara exakt som definierad, inte mer eller mindre.

Med aliaset **[\*]** kan du jämföra med värdet för varje element i matrisen och vissa egenskaper för varje element. Den här metoden gör det möjligt att jämföra element egenskaper för "if ingen", "om några", "eller" om alla "-scenarier. Med hjälp av **ipRules\*[]** verifierar ett exempel att varje _åtgärd_ är _nekad_, men inte bekymrar dig om hur många regler som finns eller vad IP- _värdet_ är. Den här exempel regeln söker efter eventuella matchningar av **ipRules [\*]. Value** till **10.0.4.1** och tillämpar bara **effectType** om den inte hittar minst en matchning:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Mer information finns i [utvärdera\*[]](../how-to/author-policies-for-arrays.md#evaluating-the--alias)-aliaset.

## <a name="initiatives"></a>Initiativ

Initiativ kan du gruppera flera relaterade principdefinitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enskilt objekt. Du kan exempelvis gruppera relaterade taggning principdefinitioner i ett enda initiativ. I stället för att tilldela varje princip individuellt kan använda du initiativ.

I följande exempel illustrerar hur du skapar ett initiativ för att hantera två taggar: `costCenter` och `productName`. Två inbyggda principer används för att tillämpa taggen standardvärdet.

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
        "policyDefinitions": [{
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

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).

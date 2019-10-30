---
title: Information om princip definitions strukturen
description: Beskriver hur resurs princip definitionen används av Azure Policy för att upprätta konventioner för resurser i din organisation genom att beskriva när principen tillämpas och vilken funktion som ska vidtas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: fe0f16fd4c07eac92ab3c1ae2c6f78b0bd1595eb
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053504"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resurs princips definitioner används av Azure Policy för att upprätta konventioner för resurser. Varje definition beskriver resursanvändningen och vilken verkan som ska vidtas när en resurs är icke-kompatibel.
Genom att definiera konventioner kan du kontrol lera kostnaderna och enklare hantera dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer ska tillåtas. Du kan också kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resurs grupp, gäller den för alla resurser i resurs gruppen.

Princip definitions schema finns här: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Du använder JSON för att skapa en princip definition. Princip definitionen innehåller element för:

- Offline
- parameters
- visnings namn
- beskrivning
- princip regel
  - logisk utvärdering
  - effekt

Följande JSON visar till exempel en princip som begränsar var resurserna distribueras:

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

## <a name="mode"></a>Läge

**Läge** konfigureras beroende på om principen är riktad mot en Azure Resource Manager-egenskap eller en resurs leverantörs egenskap.

### <a name="resource-manager-modes"></a>Lägen i Resource Manager

**Läget** avgör vilka resurs typer som ska utvärderas för en princip. De lägen som stöds är:

- `all`: utvärdera resurs grupper och alla resurs typer
- `indexed`: utvärdera endast resurs typer som stöder taggar och platser

Vi rekommenderar att du ställer in **läget** till `all` i de flesta fall. Alla princip definitioner som skapats via portalen använder `all` läge. Om du använder PowerShell eller Azure CLI kan du ange **läges** parametern manuellt. Om princip definitionen inte innehåller ett **läges** värde, används standardvärdet `all` i Azure PowerShell och `null` i Azure CLI. Ett `null` läge är detsamma som att använda `indexed` för att ge stöd för bakåtkompatibilitet.

`indexed` bör användas när du skapar principer som tvingar taggar eller platser. Även om det inte krävs, förhindrar det att resurser som inte stöder taggar och platser visas som icke-kompatibla i resultatet av efterlevnaden. Undantag är **resurs grupper**. Principer som tvingar plats eller taggar i en resurs grupp bör ange **läge** till `all` och särskilt rikta `Microsoft.Resources/subscriptions/resourceGroups` typen. Ett exempel finns i [tvinga resurs grupps Taggar](../samples/enforce-tag-rg.md). En lista över resurser som stöder taggar finns i [tagga stöd för Azure-resurser](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Resurs leverantörs lägen

Det enda resurs leverantörs läge som stöds för närvarande är `Microsoft.ContainerService.Data` för hantering av regler för regler för åtkomst kontroll i [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för Kubernetes](rego-for-aks.md) finns i en offentlig för hands version och stöder bara inbyggda princip definitioner.

## <a name="parameters"></a>Parametrar

Med parametrar kan du förenkla princip hanteringen genom att minska antalet princip definitioner. Tänk på parametrar som fälten i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar är alltid desamma, men deras värden ändras baserat på de enskilda som fyller i formuläret.
Parametrar fungerar på samma sätt när du skapar principer. Genom att inkludera parametrar i en princip definition kan du återanvända principen för olika scenarier genom att använda olika värden.

> [!NOTE]
> Parametrar kan läggas till i en befintlig och tilldelad definition. Den nya parametern måste innehålla egenskapen **DefaultValue** . Detta förhindrar att befintliga tilldelningar av principen eller initiativet från indirekt görs ogiltiga.

### <a name="parameter-properties"></a>Parameter egenskaper

En parameter har följande egenskaper som används i princip definitionen:

- **namn**: namnet på din parameter. Används av funktionen `parameters` distribution i princip regeln. Mer information finns i [använda ett parameter värde](#using-a-parameter-value).
- `type`: anger om parametern är en **sträng**, en **matris**, ett **objekt**, ett **booleskt värde** **, ett** **flyttal**eller en **datetime**.
- `metadata`: definierar under egenskaper som främst används av Azure Portal för att Visa användarvänlig information:
  - `description`: en förklaring av vad parametern används för. Kan användas för att ge exempel på acceptabla värden.
  - `displayName`: det egna namnet som visas i portalen för parametern.
  - `strongType`: (valfritt) som används för att tilldela princip definitionen via portalen. Innehåller en Sammanhangs medveten lista. Mer information finns i [strongType](#strongtype).
  - `assignPermissions`: (valfritt) Ange som _Sant_ för att Azure Portal skapa roll tilldelningar under princip tilldelningen. Den här egenskapen är användbar om du vill tilldela behörigheter utanför tilldelnings omfånget. Det finns en roll tilldelning per roll definition i principen (eller per roll definition i alla principer i initiativet). Parametervärdet måste vara en giltig resurs eller ett giltigt omfång.
- `defaultValue`: (valfritt) anger värdet för parametern i en tilldelning om inget värde anges.
  Krävs när du uppdaterar en befintlig princip definition som är tilldelad.
- `allowedValues`: (valfritt) tillhandahåller en matris med värden som parametern accepterar under tilldelningen.

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

I princip regeln refererar du parametrar med följande syntax för `parameters` distributions värde funktion:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till den **allowedLocations** -parameter som visades i [parameter egenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I `metadata`-egenskapen kan du använda **strongType** för att ange en lista över alternativ i Azure Portal som innehåller flera alternativ. Tillåtna värden för **strongType** är för närvarande:

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

## <a name="definition-location"></a>Definitions plats

När du skapar ett initiativ eller en princip måste du ange definitions platsen. Definitions platsen måste vara en hanterings grupp eller en prenumeration. Den här platsen avgör omfattningen som initiativet eller principen kan tilldelas till. Resurser måste vara direkta medlemmar av eller underordnade inom hierarkin för den definitions plats som ska användas som mål för tilldelningen.

Om definitions platsen är:

- Endast **prenumerations** resurser i den prenumerationen kan tilldelas principen.
- **Hanterings grupp** – endast resurser inom underordnade hanterings grupper och underordnade prenumerationer kan tilldelas principen. Om du planerar att tillämpa princip definitionen på flera prenumerationer måste platsen vara en hanterings grupp som innehåller dessa prenumerationer.

## <a name="display-name-and-description"></a>Visnings namn och beskrivning

Du kan använda **DisplayName** och **Description** för att identifiera princip definitionen och tillhandahålla kontext när den används. **DisplayName** får innehålla högst _128_ tecken och **beskrivningen** får bestå av högst _512_ tecken.

## <a name="policy-rule"></a>Principregel

Princip regeln består av **IF** och **then** -block. I **IF** -blocket definierar du ett eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att exakt definiera scenariot för en princip.

I blocket **then** definierar du den påverkan som inträffar när **IF** -villkoren är uppfyllda.

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

Syntaxen **not** inverterar resultatet av villkoret. **AllOf** -syntaxen (liknar logisk **och** åtgärd) kräver att alla villkor är sanna. **AnyOf** -syntaxen (liknar logisk **eller** åtgärd) kräver att ett eller flera villkor är sanna.

Du kan kapsla logiska operatorer. I följande exempel visas en åtgärd som är kapslad i en **allOf** **-åtgärd.**

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

Ett villkor utvärderar om ett **fält** eller **värde** accessor uppfyller vissa villkor. De villkor som stöds är:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

När du använder **gilla** -och **notLike** -villkoren anger du ett jokertecken `*` i värdet.
Värdet får inte ha fler än ett jokertecken `*`.

När du använder **matchnings** -och **notMatch** -villkor, anger du `#` för att matcha en siffra, `?` för en bokstav `.` matcha eventuella tecken och andra tecken för att matcha det faktiska tecknet.
**matchnings** -och **notMatch** är Skift läges känsliga. Skift läges känsliga alternativ är tillgängliga i **matchInsensitively** och **notMatchInsensitively**. Exempel finns i [Tillåt flera namn mönster](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fält

Villkor bildas med hjälp av fält. Ett fält matchar egenskaper i nytto lasten för resurs förfrågan och beskriver resursens tillstånd.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar resursens fullständiga namn. Det fullständiga namnet på en resurs är resurs namnet anpassningsprefix av eventuella överordnade resurs namn (till exempel "Server/databas").
- `kind`
- `type`
- `location`
  - Använd **Global** för resurser som är plats oberoende. Ett exempel finns i [samples-tillåtna platser](../samples/allowed-locations.md).
- `identity.type`
  - Returnerar typen av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) som är aktive rad på resursen.
- `tags`
- `tags['<tagName>']`
  - Den här klammerns syntax stöder taggnamn som har skiljetecken, till exempel bindestreck, punkter eller blank steg.
  - Där **\<tagName\>** är namnet på taggen som verifierar villkoret för.
  - Exempel: `tags['Acct.CostCenter']` där **acct. CostCenter** är namnet på taggen.
- `tags['''<tagName>''']`
  - Den här klammerns syntax stöder taggnamn som har apostrofer i den genom att använda dubbla apostrofer.
  - Där **"\<tagName\>"** är namnet på taggen som verifierar villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **"\<tagName\>"** är namnet på taggen.
- egenskaps Ali Aset – en lista finns i [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`och `tags[tag.with.dots]` är fortfarande acceptabla sätt att deklarera ett Tags-fält. De prioriterade uttrycken är dock de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parameter värde kan skickas till ett tagg-fält. Att skicka en parameter till ett taggnamn ökar flexibiliteten i princip definitionen under princip tilldelning.

I följande exempel används `concat` för att skapa ett tagg fälts uppslag för taggen som heter värdet för **TagName** -parametern. Om taggen inte finns **används Lägg till-resultatet för** att lägga till taggen med värdet för samma namngivna tagg uppsättning på den överordnade resurs gruppen granskade resurser med hjälp av `resourcegroup()` lookup-funktionen.

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

### <a name="value"></a>Värde

Villkor kan även skapas med hjälp av **värde**. **värde** kontrollerar villkor mot [parametrar](#parameters), [mall funktioner som stöds](#policy-functions)eller litteraler.
**värdet** kombineras med alla [villkor](#conditions)som stöds.

> [!WARNING]
> Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är en implicit **nekande**. Mer information finns i [undvika mall-haverier](#avoiding-template-failures).

#### <a name="value-examples"></a>Värde exempel

Den här princip regel exemplet använder **värdet** för att jämföra resultatet av funktionen `resourceGroup()` och egenskapen returnerat **namn** till ett **like** -villkor för `*netrg`. Regeln nekar en resurs som inte är av `Microsoft.Network/*` **typ** i en resurs grupp vars namn slutar på `*netrg`.

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

Den här princip regel exemplet använder **värdet** för att kontrol lera om resultatet av flera kapslade funktioner **är lika med** `true`. Regeln nekar en resurs som inte har minst tre taggar.

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

Användningen av _Template Functions_ i **Value** tillåter många komplexa kapslade funktioner. Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är en implicit **nekande**. Ett exempel på ett **värde** som inte fungerar i vissa scenarier:

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

Exempel princip regeln ovan använder [del sträng ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) för att jämföra de tre första tecken **namnen** med **ABC**. Om **namnet** är kortare än tre tecken resulterar `substring()`-funktionen i ett fel. Det här felet gör att principen blir en **neka** -påverkan.

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

Med den ändrade regel regeln `if()` kontrollerar längden på **namnet** innan ett `substring()` för ett värde får färre än tre tecken. Om **namnet** är för kort returneras värdet "inte börjar med ABC" i stället och jämförs med **ABC**. En resurs med ett kort namn som inte börjar med **ABC** kan fortfarande inte utföra princip regeln, men inte längre orsaka ett fel under utvärderingen.

### <a name="effect"></a>Verkan

Azure Policy stöder följande typer av påverkan:

- **Neka**: genererar en händelse i aktivitets loggen och Miss lyckas med begäran
- **Granskning**: genererar en varnings händelse i aktivitets loggen men misslyckade begäran
- **APPEND**: lägger till den definierade fält uppsättningen i begäran
- **AuditIfNotExists**: aktiverar granskning om en resurs inte finns
- **DeployIfNotExists**: distribuerar en resurs om den inte redan finns
- **Disabled**: utvärderar inte resurser för efterlevnad för princip regeln
- **EnforceRegoPolicy**: konfigurerar kontrollanten för öppen Policy Agent inspelare i Azure Kubernetes-tjänsten (för hands version)
- **Ändra**: lägger till, uppdaterar eller tar bort definierade taggar från en resurs

För **Lägg till**måste du ange följande information:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Värdet kan vara antingen en sträng eller ett JSON-format-objekt.

**AuditIfNotExists** och **DeployIfNotExists** utvärderar förekomsten av en relaterad resurs och tillämpar en regel. Om resursen inte matchar regeln implementeras resultatet. Du kan till exempel kräva att en Network Watcher har distribuerats för alla virtuella nätverk. Mer information finns i [granskningen om tillägget inte finns](../samples/audit-ext-not-exist.md) i exemplet.

**DeployIfNotExists** -funktionen kräver egenskapen **roleDefinitionId** i **informations** delen av princip regeln. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

På samma sätt **krävs** **roleDefinitionId** -egenskapen i **informations** delen av princip regeln för [Åtgärds uppgiften](../how-to/remediate-resources.md). **Ändra** kräver även en **Åtgärds** mat ris för att definiera vilka åtgärder som ska vidtas på resurs taggarna.

Fullständig information om varje effekt, utvärderings ordning, egenskaper och exempel finns i [förstå Azure policys effekter](effects.md).

### <a name="policy-functions"></a>Princip funktioner

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

Dessutom är funktionen `field` tillgänglig för princip regler. `field` används främst med **AuditIfNotExists** och **DeployIfNotExists** för att referera till fält på den resurs som utvärderas. Ett exempel på den här användningen kan visas i [DeployIfNotExists-exemplet](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Exempel på princip funktion

Den här princip regel exemplet använder funktionen `resourceGroup` resurs för att hämta egenskapen **Name** , kombinerat med funktionen `concat` matris och objekt för att skapa ett `like` villkor som tvingar resurs namnet att starta med resurs gruppens namn.

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

Du använder alias för egenskaper för att få åtkomst till vissa egenskaper för en resurs typ. Alias gör att du kan begränsa vilka värden eller villkor som tillåts för en egenskap på en resurs. Varje alias mappar till sökvägar i olika API-versioner för en specifik resurs typ. Under princip utvärderingen hämtar princip motorn egenskaps Sök vägen för API-versionen.

Listan över alias växer alltid. Använd någon av följande metoder för att ta reda på vilka alias som för närvarande stöds av Azure Policy:

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

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Förstå aliaset [*]

Flera av de tillgängliga aliasen har en version som visas som ett normalt namn och en annan som har **[\*]** kopplad till den. Exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Aliaset "normal" representerar fältet som ett enda värde. Det här fältet är för exakta matchnings scenarier när hela uppsättningen med värden måste vara exakt som definierad, inte mer eller mindre.

**[\*]** -aliaset gör det möjligt att jämföra mot värdet för varje element i matrisen och vissa egenskaper för varje element. Den här metoden gör det möjligt att jämföra element egenskaper för "if ingen", "om några", "eller" om alla "-scenarier. Med **ipRules [\*]** verifierar ett exempel att varje _åtgärd_ är _neka_, men inte bekymrar dig om hur många regler som finns eller vad IP- _värdet_ är. Den här exempel regeln söker efter eventuella matchningar av **ipRules [\*]. Value** till **10.0.4.1** och tillämpar bara **effectType** om den inte hittar minst en matchning:

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

Mer information finns i avsnittet [om att utvärdera [\*]-aliaset](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Initiativ

Med initiativ kan du gruppera flera relaterade princip definitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enda objekt. Du kan till exempel gruppera relaterade definitioner av princip definitioner till ett enda initiativ. I stället för att tilldela varje princip individuellt, tillämpar du initiativet.

I följande exempel visas hur du skapar ett initiativ för att hantera två Taggar: `costCenter` och `productName`. Den använder två inbyggda principer för att tillämpa standard tag gen svärdet.

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

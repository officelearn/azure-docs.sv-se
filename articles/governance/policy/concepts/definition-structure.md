---
title: Information om princip definitions strukturen
description: Beskriver hur princip definitioner används för att upprätta konventioner för Azure-resurser i din organisation.
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: 7502c1c9a2e125052abf71e50273fbd9bab15cd1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989883"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Resursen principdefinitioner används av Azure Policy för att etablera konventioner för resurser. Varje definition beskriver resurskompatibilitet och vad i kraft för att vidta när en resurs är inkompatibla.
Du kan styra kostnaderna genom att definiera konventioner och mer hantera enkelt dina resurser. Du kan till exempel ange att bara vissa typer av virtuella datorer är tillåtna. Eller så kan du kräva att alla resurser har en viss tagg. Principer ärvs av alla underordnade resurser. Om en princip tillämpas på en resursgrupp, kan den användas för alla resurser i resursgruppen.

Princip definitions schema finns här: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Du kan använda JSON för att skapa en principdefinition. Principdefinitionen innehåller element för:

- läge
- parameters
- Visningsnamn
- description
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

## <a name="mode"></a>Läge

**Läge** konfigureras beroende på om principen är riktad mot en Azure Resource Manager-egenskap eller en resurs leverantörs egenskap.

### <a name="resource-manager-modes"></a>Lägen i Resource Manager

Den **läge** avgör vilka typer av resurser kommer att utvärderas för en princip. De stödda lägena är:

- `all`: utvärdera resursgrupper och alla resurstyper
- `indexed`: endast utvärdera resurstyper som stöder taggar och plats

Vi rekommenderar att du ställer in **läge** till `all` i de flesta fall. Alla principdefinitioner som skapats via portalen användning i `all` läge. Om du använder PowerShell eller Azure CLI kan du ange den **läge** parametern manuellt. Om principdefinitionen inte innehåller en **läge** , den standardvärdet `all` i Azure PowerShell och till `null` i Azure CLI. En `null` läge är detsamma som att använda `indexed` att stödja bakåtkompatibilitet kompatibilitet.

`indexed` ska användas när du skapar principer som tillämpar taggar eller platser. Även om det inte krävs, förhindrar det att resurser som inte stöder taggar och platser visas som icke-kompatibla i resultatet av efterlevnaden. Undantaget är **resursgrupper**. Principer som framtvinga plats eller taggar på en resursgrupp bör ange **läge** till `all` och specifikt mål den `Microsoft.Resources/subscriptions/resourceGroups` typen. Ett exempel finns i [framtvinga grupp resurstaggar](../samples/enforce-tag-rg.md). En lista över resurser som stöder taggar finns i [tagga stöd för Azure-resurser](../../../azure-resource-manager/management/tag-support.md).

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a>läge för <a name="resource-provider-modes" />resurs leverantör (förhands granskning)

Följande resurs leverantörs lägen stöds för närvarande under för hands versionen:

- `Microsoft.ContainerService.Data` för hantering av regler för regler för åtkomst kontroll i [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md). Principer som använder detta resurs leverantörs läge **måste** använda [EnforceRegoPolicy](./effects.md#enforceregopolicy) -effekter.
- `Microsoft.Kubernetes.Data` för att hantera självhanterade Kubernetes-kluster i AKS-motorn på Azure.
  Principer som använder detta resurs leverantörs läge **måste** använda [EnforceOPAConstraint](./effects.md#enforceopaconstraint) -effekter.
- `Microsoft.KeyVault.Data` för att hantera valv och certifikat i [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Resurs leverantörs lägen stöder bara inbyggda princip definitioner och stöder inte initiativ i för hands versionen.

## <a name="parameters"></a>Parametrar

Parametrar underlätta hanteringen av principer genom att minska antalet principdefinitioner. Tänk på parametrar som fält i ett formulär – `name`, `address`, `city`, `state`. Dessa parametrar desamma alltid, men deras värden ändras baserat på enskilda fyller i formuläret.
Parametrar fungerar på samma sätt som när du skapar principer. Du kan återanvända principen för olika scenarier med hjälp av olika värden genom att lägga till parametrar i en principdefinition.

> [!NOTE]
> Parametrar kan läggas till i en befintlig och tilldelad definition. Den nya parametern måste innehålla egenskapen **DefaultValue** . Detta förhindrar att befintliga tilldelningar för principen eller initiativ indirekt görs ogiltig.

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

I princip regeln refererar du parametrar med följande syntax för `parameters` funktion:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till den **allowedLocations** -parameter som visades i [parameter egenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I `metadata`-egenskapen kan du använda **strongType** för att ange en lista över alternativ i Azure Portal som innehåller flera alternativ. Tillåtna värden för **strongType** nu:

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

> [!NOTE]
> Under skapandet eller uppdateringen av en princip definition definieras **ID**, **typ**och **namn** av egenskaper som är externa för JSON och är inte nödvändiga i JSON-filen. Hämtning av princip definitionen via SDK returnerar egenskaperna **ID**, **typ**och **namn** som en del av JSON, men var och en är skrivskyddad information som är relaterad till princip definitionen.

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

När du använder den **som** och **notLike** villkor du anger ett jokertecken `*` i värdet.
Värdet får inte innehålla fler än ett jokertecken `*`.

När du använder **matchnings** -och **notMatch** -villkor, anger du `#` för att matcha en siffra, `?` för en bokstav `.` matcha eventuella tecken och andra tecken för att matcha det faktiska tecknet. **Matchnings** -och **notMatch** är Skift läges känsliga, men alla andra villkor som utvärderar en _stringValue_ är Skift läges känsliga. Skift läges känsliga alternativ är tillgängliga i **matchInsensitively** och **notMatchInsensitively**. Exempel finns i [Tillåt flera namn mönster](../samples/allow-multiple-name-patterns.md).

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
  - Där **"\<tagName\>"** är namnet på taggen som verifierar villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **' My. apostrof. tag '** är namnet på taggen.
- Egenskapen alias – en lista i [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`och `tags[tag.with.dots]` är fortfarande acceptabla sätt att deklarera ett Tags-fält. De prioriterade uttrycken är dock de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parameter värde kan skickas till ett tagg-fält. Att skicka en parameter till ett taggnamn ökar flexibiliteten i princip definitionen under princip tilldelning.

I följande exempel används `concat` för att skapa ett tagg fälts uppslag för taggen som heter värdet för **TagName** -parametern. Om taggen inte finns används **ändra** -funktionen för att lägga till taggen med värdet för samma namngivna tagg uppsättning på den överordnade resurs gruppen granskade resurser med hjälp av `resourcegroup()` lookup-funktionen.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
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

Exempel princip regeln ovan använder [del sträng ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) för att jämföra de tre första tecken **namnen** med **ABC**. Om **namnet** är kortare än tre tecken resulterar `substring()`-funktionen i ett fel. Det här felet gör att principen blir en **neka** -påverkan.

Använd i stället funktionen [IF ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) för att kontrol lera om de tre första tecknen i **namn** är lika med **ABC** utan att ett **namn** som är kortare än tre tecken kan orsaka ett fel:

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

### <a name="count"></a>Antal

Villkor som räknar hur många medlemmar i en matris i resurs nytto lasten uppfyller ett villkors uttryck kan skapas med hjälp av **Count** -uttryck. Vanliga scenarier kontrollerar om "minst en av", ",", "alla" eller "ingen av" mat ris medlemmarna uppfyller villkoret. **Count** utvärderar varje [\[\*\] Ali](#understanding-the--alias) mat ris medlem för ett villkors uttryck och summerar de _sanna_ resultaten, som sedan jämförs med uttrycks operatorn.

Strukturen för **Count** -uttrycket är:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Följande egenskaper används med **Count**:

- **Count. Field** (required): innehåller sökvägen till matrisen och måste vara ett mat ris alias. Om matrisen saknas utvärderas uttrycket till _false_ utan att ta hänsyn till villkors uttrycket.
- **Count.** (valfritt): villkors uttrycket för att utvärdera varje [\[\*\] Ali Aset](#understanding-the--alias) mat ris medlem i **fältet Count.** Om den här egenskapen inte anges utvärderas alla mat ris medlemmar med sökvägen för Field till _True_. Alla [villkor](../concepts/definition-structure.md#conditions) kan användas i den här egenskapen.
  [Logiska operatorer](#logical-operators) kan användas i den här egenskapen för att skapa komplexa utvärderings krav.
- **\<villkor\>** (obligatoriskt): värdet jämförs med antalet objekt som uppfyller **antalet. Where** villkors uttryck. Ett numeriskt [villkor](../concepts/definition-structure.md#conditions) ska användas.

#### <a name="count-examples"></a>Antal exempel

Exempel 1: kontrol lera om en matris är tom

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exempel 2: kontrol lera att endast en mat ris medlem uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Exempel 3: kontrol lera om minst en mat ris medlem uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Exempel 4: kontrol lera att alla medlemmar i objekt mat ris uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Exempel 5: kontrol lera att alla sträng mat ris medlemmar uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exempel 6: Använd **fältet** i **värde** för att kontrol lera att alla mat ris medlemmar uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exempel 7: kontrol lera att minst en mat ris medlem matchar flera egenskaper i villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Verkan

Azure Policy stöder följande typer av påverkan:

- **Lägg till**: lägger till en definierad uppsättning fält på begäran
- **Granska**: genererar en varning-händelse i aktivitetsloggen men inte misslyckas begäran
- **AuditIfNotExists**: genererar en varnings händelse i aktivitets loggen om en relaterad resurs inte finns
- **Neka**: Generera en händelse i aktivitetsloggen och misslyckas begäran
- **DeployIfNotExists**: distribuerar en relaterad resurs om den inte redan finns
- **Inaktiverad**: inte utvärdera resurser för principregeln
- **EnforceOPAConstraint** (för hands version): konfigurerar hanterings styrenheten för öppna Policy Agent med Gatekeeper v3 för självhanterade Kubernetes-kluster på Azure (för hands version)
- **EnforceRegoPolicy** (för hands version): konfigurerar kontrollanten för att öppna princip agenter med Gatekeeper v2 i Azure Kubernetes-tjänsten
- **Ändra**: lägger till, uppdaterar eller tar bort definierade taggar från en resurs

Fullständig information om varje effekt, utvärderings ordning, egenskaper och exempel finns i [förstå Azure policys effekter](effects.md).

### <a name="policy-functions"></a>Princip fungerar

Alla [funktioner i Resource Manager-mallar](../../../azure-resource-manager/templates/template-functions.md) är tillgängliga för användning i en princip regel, förutom följande funktioner och användardefinierade funktioner:

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

- Azure Policy tillägg för Visual Studio Code (rekommenderas)

  Använd [Azure policy-tillägget för Visual Studio Code](../how-to/extension-for-vscode.md) för att visa och identifiera alias för resurs egenskaper.

  ![Azure Policy tillägg för Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Använd operatorn `project` om du vill visa ett **alias** för en resurs.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

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

Flera av de tillgängliga aliasen har en version som visas som ett "normal"-namn och en annan som har **\[\*\]** kopplade till den. Ett exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Aliaset "normal" representerar fältet som ett enda värde. Det här fältet är för exakta matchnings scenarier när hela uppsättningen med värden måste vara exakt som definierad, inte mer eller mindre.

Med aliaset **\[\*\]** kan du jämföra med värdet för varje element i matrisen och vissa egenskaper för varje element. Den här metoden gör det möjligt att jämföra element egenskaper för "if ingen", "om några", "eller" om alla "-scenarier. För mer komplexa scenarier använder du villkors uttrycket [Count](#count) . Med **ipRules\[\*\]** verifierar ett exempel att varje _åtgärd_ är _neka_, men inte bekymrar dig om hur många regler som finns eller vad IP- _värdet_ är.
Den här exempel regeln söker efter eventuella matchningar av **ipRules\[\*\]. Value** till **10.0.4.1** och tillämpar bara **effectType** om det inte finns minst en matchning:

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
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).

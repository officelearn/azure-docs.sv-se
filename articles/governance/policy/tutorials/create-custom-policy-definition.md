---
title: Skapa en anpassad principdefinition
description: Skapa en anpassad principdefinition för Azure Policy för att tillämpa anpassade affärsregler.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ddda2a8bf1fab4e4c48c647237617d8f705f0561
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112100"
---
# <a name="create-a-custom-policy-definition"></a>Skapa en anpassad principdefinition

Med en anpassad principdefinition kan kunderna definiera egna regler för att använda Azure. De här reglerna framtvingar ofta:

- Säkerhetsmetoder
- Kostnadshantering
- Organisationsspecifika regler (till exempel namngivning eller platser)

Oavsett affärsdrivande faktor för att skapa en anpassad princip är stegen desamma för att definiera en ny anpassad princip.

Innan du skapar en anpassad princip bör du titta bland [principexemplen](../samples/index.md) för att se om det redan finns en princip som passar dina behov.

Metoden för att skapa en anpassad princip följer de här stegen:

> [!div class="checklist"]
> - Identifiera dina affärskrav
> - Mappa varje krav till en Azure-resursegenskap
> - Mappa egenskapen till ett alias
> - Fastställa vilken effekt som ska användas
> - Skapa principdefinitionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="identify-requirements"></a>Identifiera krav

Det är viktigt att du förstår syftet med principen innan du skapar principdefinitionen. I den här självstudien använder vi ett vanligt företagssäkerhetskrav som målet för att illustrera stegen som ingår:

- Varje lagringskonto måste vara aktiverat för HTTPS
- Varje lagringskonto måste vara inaktiverat för HTTPS

Dina krav bör tydligt identifiera både resurstillståndet ”to be” och ”not to be”.

Vi har definierat förväntat tillstånd för resursen, men vi har ännu inte definierat vad vi vill ha gjort med icke-kompatibla resurser. Principen har stöd för ett antal [effekter](../concepts/effects.md). I den här självstudien ska vi definiera affärsbehov som att förhindra skapandet av resurser om de inte är kompatibla med affärsreglerna. För att nå detta mål använder vi effekten för att [neka](../concepts/effects.md#deny). Vi vill också ha alternativet för att inaktivera principen för specifika tilldelningar. Därför använder vi den [inaktiverade](../concepts/effects.md#disabled) effekten och skapar en [parameter](../concepts/definition-structure.md#parameters) i principdefinitionen.

## <a name="determine-resource-properties"></a>Fastställa resursegenskaper

Azure-resursen som ska granskas med principen är ett lagringskonto, baserat på affärsbehov.
Vi vet emellertid inte vilka egenskaper som ska användas i principdefinitionen. Principen utvärderas mot JSON-representation av resursen, så vi behöver förstå egenskaperna som är tillgängliga på den resursen.

Det finns många sätt att avgöra egenskaperna för en Azure-resurs. Vi ska titta på var och en för den här självstudien:

- Mallar för Resurshanteraren
  - Exportera befintlig resurs
  - Skapandeupplevelse
  - Snabbstartsmallar (GitHub)
  - Mallreferensdokument
- Azure Resource Explorer

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Det finns flera sätt att titta på en [Resource Manager-mall](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) som innehåller egenskapen som du vill hantera.

#### <a name="existing-resource-in-the-portal"></a>Befintlig resurs i portalen

Det enklaste sättet att hitta egenskaper är att titta på en befintlig resurs av samma typ. Resurser som redan har konfigurerats med inställningen som du vill framtvinga innehåller också värdet att jämföra med.
Titta på sidan **Automationsskript** (under **Inställningar**) i Azure-portalen för den specifika resursen.

![Sidan Automationsskript](../media/create-custom-policy-definition/automation-script.png)

Om du gör det för ett lagringskonto visas en mall som liknar det här exemplet:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Under **egenskaper** är ett värde med namnet **supportsHttpsTrafficOnly** inställt på **falskt**. Den här egenskapen verkar vara den vi letar efter. Resursens **typ** är **Microsoft.Storage/storageAccounts**. Med typen kan vi begränsa principen till endast resurser av den här typen.

#### <a name="create-a-resource-in-the-portal"></a>Skapa en resurs i portalen

Ett annat sätt via portalen är resursskapandeupplevelsen. När du skapar ett lagringskonto via portalen är ett alternativ under fliken **Avancerat** **Säkerhetsöverföring krävs**.
Den här egenskapen har alternativen _Inaktiverad_ och _Aktiverad_. Informationsikonen har ytterligare text som bekräftar att det är sannolikt att det här alternativet är egenskapen vi vill ha. Dock visar inte portalen egenskapens namn på den här skärmen.

På fliken **Granska + skapa** finns en länk längst ned på sidan för att **ladda ned en mall för automatisering**. Om du väljer länken öppnas den mall som skapar den resurs som vi har konfigurerat. I det här fallet kan vi se två viktiga uppgifter:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Den här informationen talar om egenskapstypen och bekräftar även att **supportsHttpsTrafficOnly** är egenskapen vi letar efter.

#### <a name="quickstart-templates-on-github"></a>Snabbstartsmallar på GitHub

[Azure-snabbstartsmallarna](https://github.com/Azure/azure-quickstart-templates) på GitHub har hundratals Resource Manager-mallar som skapats för olika resurser. Dessa mallar kan vara ett bra sätt att hitta resursegenskapen du letar efter. Vissa egenskaper kanske verkar vara det du letar efter, men styr något annat.

#### <a name="resource-reference-docs"></a>Resursreferensdokument

För att validera att **supportsHttpsTrafficOnly** är rätt egenskap kontrollerar du Resource Manager-mallreferensen för [lagringskontoresursen](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) på lagringsprovidern.
Egenskapsobjektet har en lista med giltiga parametrar. Om du väljer länken [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) visas en tabell över godkända egenskaper. **supportsHttpsTrafficOnly** finns och beskrivningen matchar vad vi är intresserade av för att uppfylla affärskraven.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Du kan även utforska dina Azure-resurser via [Azure Resource Explorer](https://resources.azure.com) (förhandsversion). Det här verktyget använder kontexten för din prenumeration, så du måste autentisera till webbplatsen med dina autentiseringsuppgifter för Azure. När autentiseringen är klar kan du söka genom providrar, prenumerationer, resursgrupper och resurser.

Leta upp en lagringskontoresurs och titta på egenskaperna. Vi kan se egenskapen **supportsHttpsTrafficOnly** även här. Om vi väljer fliken **Dokumentation** ser vi att egenskapsbeskrivningen matchar det vi påträffade i referensdokumenten tidigare.

## <a name="find-the-property-alias"></a>Hitta egenskapsalias

Vi har identifierat resursegenskapen, men vi behöver mappa egenskapen till ett [alias](../concepts/definition-structure.md#aliases).

Det finns några olika sätt att avgöra alias för en Azure-resurs. Vi ska titta på var och en för den här självstudien:

- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Azure CLI

I Azure CLI används `az provider`-kommandogruppen för att söka efter resursalias. Vi ska filtrera **Microsoft.Storage**-namnområdet utifrån den information som vi tidigare fått om Azure-resursen.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

I resultatet ser vi ett alias som stöds av lagringskonton med namnet **supportsHttpsTrafficOnly**. Den här förekomsten av det här aliaset innebär att vi kan skriva principen för att genomdriva våra affärskrav!

### <a name="azure-powershell"></a>Azure PowerShell

I Azure PowerShell används cmdleten `Get-AzPolicyAlias` för att söka efter resursalias.
Vi ska filtrera **Microsoft.Storage**-namnområdet utifrån den information som vi tidigare fått om Azure-resursen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Som i Azure CLI visar resultatet ett alias som stöds av lagringskonton med namnet **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) är en ny tjänst som finns i förhandsversion. Det gör att en annan metod kan hitta egenskaper för Azure-resurser. Här är en exempelfråga för att titta på ett enda lagringskonto med Resource Graph:

```Query
where type=~'microsoft.storage/storageaccounts' | limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Resultatet liknar vad vi ser i Resource Manager-mallar och via Azure Resource Explorer. Dock innehåller Azure Resource Graph-resultat även [aliasinformation](../concepts/definition-structure.md#aliases). Här är exempel på utdata från ett lagringskonto för alias:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Resource Graph (förhandsversion) kan användas via [Cloud Shell](https://shell.azure.com), vilket gör det till ett snabbt och enkelt sätt att utforska egenskaperna för dina resurser.

## <a name="determine-the-effect-to-use"></a>Fastställa vilken effekt som ska användas

Det är nästan lika viktigt att bestämma vad som ska göras med icke-kompatibla resurser som att bestämma vad som ska utvärderas i första hand. Varje möjligt svar på en icke-kompatibel resurs kallas för en [effekt](../concepts/effects.md). Effekten kontrollerar om den icke-kompatibla resursen loggas, blockeras, har bifogade data eller en distribution associerad till sig för att sätta tillbaka resursen i ett kompatibelt tillstånd.

I vårt exempel är Neka den effekt som vi vill ha, eftesom vi inte vill ha icke-kompatibla resurser som skapats i vår Azure-miljö. Granskning är ett bra första alternativ för en principeffekt för att avgöra vad effekten av en princip är innan du nekar. Ett sätt att underlätta ändring av effekt per tilldelning är att parameterisera effekten. Se [parametrarna](#parameters) nedan för information om hur.

## <a name="compose-the-definition"></a>Skapa definitionen

Nu har vi egenskapsinformation och alias för det vi planerar att hantera. Därefter ska vi skapa själva principregeln. Om du ännu inte är bekant med principspråket kan du titta på [principdefinitionsstrukturen](../concepts/definition-structure.md) för att se hur du ska strukturera principdefinitionen. Här är en tom mall för hur en principdefinition ska se ut:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadata

De första tre komponenterna är principmetadata. De här komponenterna är enkla att ange värden för eftersom vi vet vad vi skapar regeln för. [Läget](../concepts/definition-structure.md#mode) handlar främst om taggar och resursplats. Eftersom vi inte behöver begränsa utvärdering till resurser som har stöd för taggar använder vi _Alla_ som **läge**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametrar

Vi använde inte någon parameter för att ändra utvärderingen, men vi vill använda en parameter för att tillåta ändring av **effekten** för felsökning. Vi ska definiera en **effectType**-parameter och begränsa den till endast **Neka** och **Inaktiverad**. De här två alternativen matchar våra affärskrav. Det slutförda parameterblocket ser ut som i följande exempel:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Principregel

Skapandet av [principregeln](../concepts/definition-structure.md#policy-rule) är det sista steget i att skapa vår anpassade principdefinition. Vi har identifierat två instruktioner för testning:

- Lagringskontots **typ** är **Microsoft.Storage/storageAccounts**
- Att lagringskontot **supportsHttpsTrafficOnly** inte är **sant**

Eftersom vi behöver att båda dessa instruktionerna är sanna använder vi den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf**. Vi ska skicka parametern **effectType** för effekten i stället för att göra en statisk deklaration. Vår slutförda regel ser ut som i följande exempel:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Slutförd definition

När alla tre delar av principen har definierats är här vår slutförda definition:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Den slutförda definitionen kan användas för att skapa en ny princip. Portalen och varje SDK (Azure CLI, Azure PowerShell och REST-API) accepterar definitionen på olika sätt, så gå igenom kommandona för varje att verifiera korrekt användning. Tilldela den sedan, med den parameteriserade effekten, till lämpliga resurser för att hantera säkerheten för dina lagringskonton.

## <a name="review"></a>Granska

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> - Identifierade dina affärskrav
> - Mappade varje krav till en Azure-resursegenskap
> - Mappade egenskapen till ett alias
> - Fastställde vilken effekt som skulle användas
> - Skapade principdefinitionen

## <a name="next-steps"></a>Nästa steg

Använd din anpassade principdefinition för att skapa och tilldela en princip:

> [!div class="nextstepaction"]
> [Skapa och tilldela en principdefinition](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
---
title: Azure Resource Manager mallstruktur och syntax | Microsoft Docs
description: Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med deklarativ JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 4fa610f144277b73bb6d555d46e63a01c413e07e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Förstå struktur och syntaxen för Azure Resource Manager-mallar
Den här artikeln beskriver strukturen i en Azure Resource Manager-mall. Det innehåller olika avsnitt i en mall och egenskaper som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution. En stegvis självstudiekurs om hur du skapar en mall finns i [skapa din första Azure Resource Manager-mallen](resource-manager-create-first-template.md).

## <a name="template-format"></a>Mallformat
I sin enklaste strukturen innehåller en mall följande element:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| $schema |Ja |Plats för JSON-schemafilen som beskriver versionen av mallspråk. Använd den URL som visas i föregående exempel. |
| contentVersion |Ja |Version av mallen (till exempel 1.0.0.0). Du kan ange ett värde för det här elementet. Det här värdet kan användas för att se till att rätt mall används när du distribuerar resurser med hjälp av mallen. |
| parameters |Nej |Värden som tillhandahålls när distributionen körs för att anpassa resource distribution. |
| variabler |Nej |Värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck. |
| functions |Nej |Användardefinierade funktioner som är tillgängliga i mallen. |
| resurser |Ja |Resurstyper som distribuerats eller uppdateras i en resursgrupp. |
| utdata |Nej |Värden som returneras efter distributionen. |

Varje element innehåller egenskaper som du kan ange. I följande exempel innehåller den fullständiga syntaxen för en mall:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Den här artikeln beskriver avsnitt i mallen i detalj.

## <a name="syntax"></a>Syntax
Den grundläggande syntaxen i mallen är JSON. Dock utöka uttryck och funktioner i JSON-värden som är tillgängliga i mallen.  Uttryck skrivs i JSON-stränglitteraler vars första och sista tecknen är hakparenteserna: `[` och `]`respektive. Värdet för uttrycket utvärderas när mallen distribueras. Medan skrivs som en teckensträng kan resultat av utvärderingen av uttrycket vara av en annan JSON-typ, till exempel en matris eller ett heltal, beroende på det faktiska uttrycket.  Att ha en teckensträng som börjar med en hakparentes `[`, men inte har det tolkas som ett uttryck, lägga till en extra hakparentes för att starta strängen med `[[`.

Normalt använder du uttryck med funktioner för att utföra åtgärder för att konfigurera distributionen. Precis som i JavaScript-funktionsanrop som är formaterade som `functionName(arg1,arg2,arg3)`. Du kan referera egenskaper genom att använda operatorerna punkt och [index].

I följande exempel visas hur du använder flera funktioner när man skapar ett-värde:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

En fullständig lista över Mallfunktioner finns [Azure Resource Manager Mallfunktioner](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametrar
I avsnittet parametrar i mallen kan du ange vilka värden som du kan ange när du distribuerar resurserna. Dessa värden kan du anpassa distributionen med värden som är anpassade för en viss miljö (t.ex dev, test- och). Du behöver inte ange parametrarna i mallen, men utan parametrar mallen skulle distribuera alltid samma resurser med samma namn, platser och egenskaper.

I följande exempel visar en enkel parameterdefinition:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Information om hur du definierar parametrar finns [parametrar avsnitt i Azure Resource Manager-mallar](resource-manager-templates-parameters.md).

## <a name="variables"></a>Variabler
I avsnittet variables kan skapa du värden som kan användas i hela din mall. Du behöver inte definiera variabler, men de förenkla ofta din mall genom att minska komplexa uttryck.

I följande exempel visas ett enkelt variabeldefinitionen:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Information om hur du definierar variabler finns i [variabler avsnitt i Azure Resource Manager-mallar](resource-manager-templates-variables.md).

## <a name="functions"></a>Functions

Du kan skapa egna funktioner i din mall. Dessa funktioner är tillgängliga för användning i mallen. Normalt kan du definiera komplicerade uttryck som du inte vill upprepa i hela din mall. Du kan skapa användardefinierade funktioner från uttryck och [funktioner](resource-group-template-functions.md) som stöds i mallar.

När du definierar en user-funktionen, finns det vissa begränsningar:

* Funktionen kommer inte åt variabler.
* Funktionen kan inte använda den [referera funktionen](resource-group-template-functions-resource.md#reference).
* Parametrar för funktionen kan inte ha standardvärden.

Dina funktioner kräver ett namnutrymmesvärde för att undvika namngivningskonflikter med Mallfunktioner. I följande exempel visas en funktion som returnerar namnet på ett lagringskonto:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Du kan anropa en funktion med:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Resurser
I avsnittet resurser kan du definiera de resurser som distribueras eller uppdateras. Det här avsnittet får komplicerad, eftersom du måste förstå vilka typer som du distribuerar för att tillhandahålla rätt värden.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Mer information finns i [resurser avsnitt i Azure Resource Manager-mallar](resource-manager-templates-resources.md).

## <a name="outputs"></a>Utdata
I avsnittet utdata anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en resurs som är distribuerad.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Mer information finns i [matar ut Azure Resource Manager-mallar](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Mallen gränser

Begränsa storleken på din mall 1 MB och varje parameterfilen till 64 KB. Gränsen på 1 MB gäller sluttillstånd för mallen när den har utökats med iterativ resursdefinitionerna och värden för parametrar och variabler. 

Du är begränsad till:

* 256 parametrar
* 256 variabler
* 800 resurserna (inklusive antal kopior)
* 64 utdatavärden
* 24,576 tecken i ett malluttryck för

Du kan överskrida vissa mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner som du kan använda från i en mall finns [Azure Resource Manager mallen Functions](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas mellan flera resursgrupper. Mer information finns i [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).

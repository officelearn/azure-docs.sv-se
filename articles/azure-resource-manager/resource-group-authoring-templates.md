---
title: Azure Resource Manager-mall-strukturen och syntaxen | Microsoft Docs
description: Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2019
ms.author: tomfitz
ms.openlocfilehash: cdc48cf278fdd68bacf74c2d6d762c8d731949e0
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111579"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Förstå strukturen och syntaxen för Azure Resource Manager-mallar

Den här artikeln beskriver strukturen för en Azure Resource Manager-mall. Den anger de olika avsnitten i en mall och egenskaperna som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution. En stegvis självstudiekurs om hur du skapar en mall finns i [skapa din första Azure Resource Manager-mall](resource-manager-create-first-template.md).

## <a name="template-format"></a>Mallformat

I sin enklaste struktur har en mall följande element:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| $schema |Ja |Platsen för schemat JSON-fil som beskriver versionen av mallspråk.<br><br> För distribution av resursgrupper, använder du: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>För prenumerationsdistributioner av, använder du: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ja |Versionen av mallen (till exempel 1.0.0.0). Du kan ange ett värde för det här elementet. Använd det här värdet till dokumentet betydande förändringar i mallen. Det här värdet kan användas för att se till att rätt mall används när du distribuerar resurser med hjälp av mallen. |
| parameters |Nej |Värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen. |
| Variabler |Nej |Värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck. |
| functions |Nej |Användardefinierade funktioner som är tillgängliga i mallen. |
| resurser |Ja |Resurstyper som är distribuerade eller uppdateras i en resursgrupp. |
| utdata |Nej |Värden som returneras efter distributionen. |

Varje element har egenskaper som du kan ange. I följande exempel visas den fullständiga syntaxen för en mall:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Den här artikeln beskriver avsnitt i mallen i större detalj.

## <a name="syntax"></a>Syntax

Grundläggande syntaxen för mallen är JSON. Dock utöka uttryck och funktioner i JSON-värden som är tillgängliga i mallen.  Uttryck skrivs i JSON-stränglitteraler vars första och sista tecken börjar hakparenteserna: `[` och `]`respektive. Värdet för uttrycket utvärderas när mallen distribueras. Medan skrivs som en teckensträng, kan resultatet av utvärderingen av uttrycket vara av en annan JSON-typ, till exempel en matris eller ett heltal, beroende på faktiska uttrycket.  Ha en teckensträng som börjar med en hakparentes `[`, men inte har det tolkas som ett uttryck, lägga till en extra hakparentes för att starta strängen med `[[`.

Normalt kan använda du uttryck med functions för att utföra åtgärder för att konfigurera distributionen. Precis som i JavaScript, funktionsanrop som är formaterade som `functionName(arg1,arg2,arg3)`. Du referera till egenskaper med hjälp av punkt och [index] operatörer.

I följande exempel visas hur du använder flera funktioner när ett värde:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

En fullständig lista över Mallfunktioner finns [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametrar

I avsnittet parametrar i mallen kan du ange vilka värden som du kan ange när du distribuerar resurser. Dessa parametervärden kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö (till exempel utveckling, testning och produktion). Du behöver inte ange parametrar i mallen, men utan parametrar mallen distribuerar alltid samma resurser med samma namn, platser och egenskaper.

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

Information om hur du definierar parametrar finns i [Parameters-avsnittet av Azure Resource Manager-mallar](resource-manager-templates-parameters.md).

## <a name="variables"></a>Variabler

I avsnittet variables kan skapa du värden som kan användas i hela din mall. Du behöver inte definiera variabler, men de förenkla ofta din mall genom att minska komplexa uttryck.

I följande exempel visar en enkel variabeldefinitionen:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Information om hur du definierar variabler finns i [Variables-avsnittet av Azure Resource Manager-mallar](resource-manager-templates-variables.md).

## <a name="functions"></a>Functions

Du kan skapa egna funktioner i din mall. Dessa funktioner är tillgängliga för användning i din mall. Normalt kan definiera du komplicerat uttryck som du inte vill upprepa i hela din mall. Du kan skapa användardefinierade funktioner från uttryck och [functions](resource-group-template-functions.md) som stöds i mallar.

När du definierar en user-funktionen, finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som definieras i funktionen. När du använder den [parametrar funktionen](resource-group-template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda den [refererar till funktionen](resource-group-template-functions-resource.md#reference).
* Funktionens parametrar kan inte ha standardvärden.

Dina funktioner kräver en namnområdesvärdet för att undvika namnkonflikter med Mallfunktioner. I följande exempel visas en funktion som returnerar ett lagringskontonamn:

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

Du anropar en funktion med:

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
I resursavsnittet kan du definiera de resurser som är distribuerade eller uppdateras. Det här avsnittet kan bli komplicerade eftersom du måste förstå de typer som du distribuerar för att ge rätt värden.

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

Villkorligt inkludera eller exkludera en resurs under distributionen, använda den [elementet](resource-manager-templates-resources.md#condition). Mer information om resurser finns i [resursavsnittet i Azure Resource Manager-mallar](resource-manager-templates-resources.md).

## <a name="outputs"></a>Utdata
I Outputs-avsnittet anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en distribuerad resurs.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Mer information finns i [matar ut Azure Resource Manager-mallar](resource-manager-templates-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Kommentarer och metadata

Du har några alternativ för att lägga till kommentarer och metadata i mallen.

Du kan lägga till en `metadata` nästan var som helst i din mall. Resource Manager ignorerar objektet, men JSON-redigerare kan varna dig att egenskapen är inte giltig. Definiera egenskaper som du behöver i objektet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "comments": "This template was developed for demonstration purposes.",
        "author": "Example Name"
    },
```

För **parametrar**, lägga till en `metadata` objekt med en `description` egenskapen.

```json
"parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "User name for the Virtual Machine."
      }
    },
```

När du distribuerar mallen via portalen, används automatiskt texten som du anger i beskrivningen som ett tips för den parametern.

![Visa parametern tips](./media/resource-group-authoring-templates/show-parameter-tip.png)

För **resurser**, lägga till en `comments` element eller ett metadataobjekt. I följande exempel visar både ett element med kommentarer och ett metadataobjekt.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

För **matar ut**, lägga till ett metadataobjekt i värdet.

```json
"outputs": {
    "hostname": {
      "type": "string",
      "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
      "metadata": {
        "comments": "Return the fully qualified domain name"
      }
    },
```

Du kan inte lägga till ett metadataobjekt användardefinierade funktioner.

Du kan använda för infogade kommentarer `//` men den här syntaxen fungerar inte med alla verktyg. Du kan inte använda Azure CLI för att distribuera mallen med infogade kommentarer. Och du kan inte använda redigeraren för mall för att arbeta med mallar med infogade kommentarer. Om du lägger till den här typen av kommentar var noga med att de verktyg du använder supporten infogade JSON kommentarer.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
      "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
      "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

I VS Code kan du ange språkläge till JSON med kommentarer. Infogade kommentarer markeras som ogiltig. Att ändra läget:

1. Öppna val av språk läge (Ctrl + K M)

1. Välj **JSON med kommentarer**.

   ![Välj språkläge](./media/resource-group-authoring-templates/select-json-comments.png)

## <a name="template-limits"></a>Mall för gränser

Begränsa storleken på din mall till 1 MB och varje parameterfilen till 64 KB. Gränsen på 1 MB gäller sluttillstånd för mallen när det har utökats med iterativ resursdefinitioner och värden för variabler och parametrar. 

Du är också begränsad till:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal kopior)
* 64 utdatavärden
* 24,576 tecken i ett malluttryck

Du kan överskrida vissa begränsningar för mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](resource-manager-objects-as-parameters.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Rekommendationer om hur du skapar mallar finns i [Metodtips för Azure Resource Manager-mall](template-best-practices.md).
* Rekommendationer om hur du skapar Resource Manager-mallar som du kan använda i alla Azure-miljöer och Azure Stack finns i [utveckla Azure Resource Manager-mallar för molnet konsekvens](templates-cloud-consistency.md).

---
title: Azure Resource Manager-mall-strukturen och syntaxen | Microsoft Docs
description: Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
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
ms.date: 05/30/2018
ms.author: tomfitz
ms.openlocfilehash: 1b982bddc951e710ba3bfa5fe8621d6595b95a52
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405352"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Förstå strukturen och syntaxen för Azure Resource Manager-mallar
Den här artikeln beskriver strukturen för en Azure Resource Manager-mall. Den anger de olika avsnitten i en mall och egenskaperna som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution. En stegvis självstudiekurs om hur du skapar en mall finns i [skapa din första Azure Resource Manager-mall](resource-manager-create-first-template.md).

## <a name="quickstarts-and-tutorials"></a>Snabbstarter och självstudier

Använd följande snabbstarter och självstudier för att lära dig hur du utvecklar resource manager-mallar:

- Snabbstarter

  	|Titel|Beskrivning|
  	|------|-----|
  	|[Använda Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md)|Skapa en mall med hjälp av portalen och hur du redigerar och distribuera mallen.|
  	|[Använd Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)|Använd Visual Studio Code för att skapa och redigera mallar och hur du använder Azure Cloud shell för att distribuera mallar.|
  	|[Använd Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)|Använd Visual Studio för att skapa, redigera och distribuera mallar.|

- Självstudier

  	|Titel|Beskrivning|
  	|------|-----|
  	|[Använda mallreferensen](./resource-manager-tutorial-create-encrypted-storage-accounts.md)|Använda referensdokumentationen för mallar för att utveckla mallar. I självstudierna hitta schema för storage-konto och Använd informationen för att skapa ett krypterat lagringskonto.|
  	|[Skapa flera instanser](./resource-manager-tutorial-create-multiple-instances.md)|Skapa flera instanser av Azure-resurser. I självstudiekursen ska skapa du flera instanser av storage-konto.|
  	|[Ange distributionsordning för resursen](./resource-manager-tutorial-create-templates-with-dependent-resources.md)|Definiera resursberoenden. I självstudien skapar du ett virtuellt nätverk, en virtuell dator och beroende Azure-resurser. Du lär dig hur beroenden som har definierats.|
  	|[Använda villkor](./resource-manager-tutorial-use-conditions.md)|Distribuera resurser baserat på vissa parametervärden. I självstudien definierar du en mall för att skapa ett nytt lagringskonto eller Använd ett befintligt lagringskonto baserat på värdet för en parameter.|
  	|[Integrera nyckelvalv](./resource-manager-tutorial-use-key-vault.md)|Hämta hemligheter/lösenord från Azure Key Vault. I självstudien skapar du en virtuell dator.  Lösenord för virtuella datorn administratören hämtas från ett Key Vault.|
  	|[Skapa länkade mallar](./resource-manager-tutorial-create-linked-templates.md)|Modularize mallar och anropa andra mallar från en mall. I självstudien skapar du ett virtuellt nätverk, en virtuell dator och alla beroende resurser.  Det beroende lagringskontot har definierats i en länkad mall. |
  	|[Använda praxis för säker distribution](./deployment-manager-tutorial.md)|Använd Azure Deployment manager. |

## <a name="template-format"></a>Mallformat

I sin enklaste struktur har en mall följande element:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| $schema |Ja |Platsen för schemat JSON-fil som beskriver versionen av mallspråk. Använd den URL som visas i föregående exempel. |
| contentVersion |Ja |Versionen av mallen (till exempel 1.0.0.0). Du kan ange ett värde för det här elementet. Använd det här värdet till dokumentet betydande förändringar i mallen. Det här värdet kan användas för att se till att rätt mall används när du distribuerar resurser med hjälp av mallen. |
| parameters |Nej |Värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen. |
| Variabler |Nej |Värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck. |
| functions |Nej |Användardefinierade funktioner som är tillgängliga i mallen. |
| resurser |Ja |Resurstyper som är distribuerade eller uppdateras i en resursgrupp. |
| utdata |Nej |Värden som returneras efter distributionen. |

Varje element har egenskaper som du kan ange. I följande exempel visas den fullständiga syntaxen för en mall:

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
* Funktionen har inte åtkomst till mallparametrarna. Det vill säga den [parametrar funktionen](resource-group-template-functions-deployment.md#parameters) är begränsad till funktionsparametrar.
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

Mer information finns i [resursavsnittet i Azure Resource Manager-mallar](resource-manager-templates-resources.md).

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

## <a name="template-limits"></a>Mall för gränser

Begränsa storleken på din mall till 1 MB och varje parameterfilen till 64 KB. Gränsen på 1 MB gäller sluttillstånd för mallen när det har utökats med iterativ resursdefinitioner och värden för variabler och parametrar. 

Du är också begränsad till:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal kopior)
* 64 utdatavärden
* 24,576 tecken i ett malluttryck

Du kan överskrida vissa begränsningar för mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Rekommendationer om hur du skapar Resource Manager-mallar som du kan använda i globala Azure, nationella Azure-moln och Azure Stack finns på sidan om att [utveckla Azure Resource Manager-mallar för molnkonsekvens](templates-cloud-consistency.md).

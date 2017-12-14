---
title: Azure Resource Manager mallstruktur och syntax | Microsoft Docs
description: "Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med deklarativ JSON-syntax."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 73d3397ac6527a216eadd6d0d013c97b86c55e6b
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
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

## <a name="expressions-and-functions"></a>Uttryck och funktioner
Den grundläggande syntaxen i mallen är JSON. Dock utöka uttryck och funktioner i JSON-värden som är tillgängliga i mallen.  Uttryck skrivs i JSON-stränglitteraler vars första och sista tecknen är hakparenteserna: `[` och `]`respektive. Värdet för uttrycket utvärderas när mallen distribueras. Medan skrivs som en teckensträng kan resultat av utvärderingen av uttrycket vara av en annan JSON-typ, till exempel en matris eller ett heltal, beroende på det faktiska uttrycket.  Att ha en teckensträng som börjar med en hakparentes `[`, men inte har det tolkas som ett uttryck, lägga till en extra hakparentes för att starta strängen med `[[`.

Normalt använder du uttryck med funktioner för att utföra åtgärder för att konfigurera distributionen. Precis som i JavaScript-funktionsanrop som är formaterade som `functionName(arg1,arg2,arg3)`. Du kan referera egenskaper genom att använda operatorerna punkt och [index].

I följande exempel visas hur du använder flera funktioner vid värden:

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
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

## <a name="resources"></a>Resurser
I avsnittet resurser kan du definiera de resurser som distribueras eller uppdateras. Det här avsnittet får komplicerad, eftersom du måste förstå vilka typer som du distribuerar för att tillhandahålla rätt värden. Resurs-specifika värden (apiVersion, typ och egenskaper) som du måste ange finns [definiera resurser i Azure Resource Manager-mallar](/azure/templates/). 

Du kan definiera resurser med följande struktur:

```json
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
]
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| tillstånd | Nej | Booleskt värde som anger om resursen har distribuerats. |
| apiVersion |Ja |Version av REST-API för att använda för att skapa resursen. |
| typ |Ja |Typ av resursen. Det här värdet är en kombination av namnområde med resursprovidern och resurstypen (exempelvis **Microsoft.Storage/storageAccounts**). |
| namn |Ja |Namnet på resursen. Namnet måste följa URI komponenten begränsningar som definierats i RFC3986. Dessutom Azure-tjänster som exponerar resursnamnet att externa parter verifiera namnet så att den är inte ett försök att imitera en annan identitet. |
| location |Det varierar |Geo-platser som stöds av den angivna resursen. Du kan välja någon av de tillgängliga platserna, men oftast är det klokt att välja ett som är nära dina användare. Vanligtvis gör det också bra att placera resurser som samverkar med varandra i samma region. De flesta resurstyper kräver en plats, men vissa typer (till exempel en rolltilldelning) kräver inte en plats. Se [som resursplats i Azure Resource Manager-mallar](resource-manager-template-location.md). |
| tags |Nej |Taggar som är kopplade till resursen. Se [tagga resurser i Azure Resource Manager-mallar](resource-manager-template-tags.md). |
| Kommentarer |Nej |Anteckningar för dokumentation resurserna i mallen |
| kopiera |Nej |Om mer än en instans krävs antalet resurser för att skapa. Standardläget är parallell. Ange seriell läge när du inte vill att alla eller resurser som ska distribueras på samma gång. Mer information finns i [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nej |Resurser som måste distribueras innan den här resursen har distribuerats. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. Om resurserna inte är beroende av varandra kan distribueras de parallellt. Värdet kan vara en kommaavgränsad lista över en resurs namn eller resurs unika identifierare. Endast lista över resurser som distribueras i den här mallen. Resurser som inte har definierats i denna mall måste redan finnas. Undvik att lägga till onödiga beroenden som de långsamma distributionen och skapa Cirkelberoenden. Information om inställningen beroenden finns [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md). |
| properties |Nej |Resurs-specifika konfigurationsinställningar. Värdena för egenskaperna är samma värden som du anger i begärandetexten för REST API-åtgärd (PUT-metoden) att skapa resursen. Du kan också ange en kopia matris om du vill skapa flera instanser av en egenskap. Mer information finns i [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md). |
| resurser |Nej |Underordnade resurser som är beroende av resursen som definieras. Ange endast resurstyper som tillåts enligt schemat för den överordnade resursen. Den fullständigt kvalificerade typ av underordnade resursen innehåller resurstypen överordnade **Microsoft.Web/sites/extensions**. Beroende på den överordnade resursen är inte underförstådd. Du måste uttryckligen definiera sambandet. |

Avsnittet resurser innehåller en matris med resurserna som ska distribueras. Du kan också definiera en matris med underordnade resurser inom varje resurs. Resurser-avsnitt kan därför ha en struktur som:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Mer information om hur du definierar underordnade resurser finns [ange namn och typ för underordnade resursen i Resource Manager-mall](resource-manager-template-child-resource.md).

Den **villkoret** element anger om resursen har distribuerats. Värdet för det här elementet matchar true eller false. Till exempel vill ange om ett nytt lagringskonto har distribuerats, använder du:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Ett exempel på hur du använder en ny eller befintlig resurs finns [ny eller befintlig mall för villkoret](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Om du vill ange om en virtuell dator distribueras med ett lösenord eller SSH-nyckeln definierar två versioner av den virtuella datorn i din mall och använda **villkoret** skilja användning. Skicka en parameter som anger vilket scenario för att distribuera.

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

Ett exempel på med ett lösenord eller SSH-nyckel för att distribuera den virtuella datorn, se [användarnamn eller SSH villkoret mallen](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="outputs"></a>Utdata
I avsnittet utdata anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en resurs som är distribuerad.

I följande exempel visar strukturen för en definition av utdata:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| outputName |Ja |Namnet på värdet. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöd för samma datatyper som mall indataparametrar. |
| värde |Ja |Mallspråksuttrycket som utvärderas och returneras som utdata. |

I följande exempel visar ett värde som returneras i avsnittet utdata.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Mer information om hur du arbetar med utdata finns [dela tillstånd i Azure Resource Manager-mallar](best-practices-resource-manager-state.md).

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

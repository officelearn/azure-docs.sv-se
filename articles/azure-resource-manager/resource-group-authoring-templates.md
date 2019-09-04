---
title: Azure Resource Manager mallens struktur och syntax | Microsoft Docs
description: Beskriver strukturen och egenskaperna för Azure Resource Manager mallar med deklarativ JSON-syntax.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275125"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Förstå strukturen och syntaxen för Azure Resource Manager mallar

I den här artikeln beskrivs strukturen för en Azure Resource Manager-mall. Den visar de olika avsnitten i en mall och de egenskaper som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution.

Den här artikeln är avsedd för användare som har en viss välbekanthet med Resource Manager-mallar. Den innehåller detaljerad information om mallens struktur och syntax. Om du vill ha en introduktion till hur du skapar en mall, se [skapa din första Azure Resource Manager-mall](resource-manager-create-first-template.md).

## <a name="template-format"></a>Mallformat

I sin enklaste struktur har en mall följande element:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| $schema |Ja |Platsen för JSON-schemafilen som beskriver versionen av mallens språk.<br><br> För resurs grupps distributioner använder du:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>För prenumerations distributioner använder du:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ja |Version av mallen (till exempel 1.0.0.0). Du kan ange valfritt värde för det här elementet. Använd det här värdet om du vill dokumentera viktiga ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används. |
| apiProfile |Nej | En API-version som fungerar som en samling av API-versioner för resurs typer. Använd det här värdet för att undvika att behöva ange API-versioner för varje resurs i mallen. När du anger en API-profil version och inte anger en API-version för resurs typen, använder Resource Manager API-versionen för den resurs typ som definieras i profilen.<br><br>Egenskapen API Profile är särskilt användbar när du distribuerar en mall till olika miljöer, till exempel Azure Stack och globala Azure. Använd API Profile-versionen för att kontrol lera att din mall automatiskt använder versioner som stöds i båda miljöerna. En lista över aktuella API-profiler och de resurs-API-versioner som definierats i profilen finns i [API-profil](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Mer information finns i [spåra versioner med hjälp av API-profiler](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nej |Värden som anges när distributionen körs för att anpassa resurs distributionen. |
| [variables](#variables) |Nej |Värden som används som JSON-fragment i mallen för att förenkla mallarnas språk uttryck. |
| [funktionen](#functions) |Nej |Användardefinierade funktioner som är tillgängliga i mallen. |
| [resources](#resources) |Ja |Resurs typer som distribueras eller uppdateras i en resurs grupp eller prenumeration. |
| [outputs](#outputs) |Nej |Värden som returneras efter distribution. |

Varje element har egenskaper som du kan ange. I den här artikeln beskrivs mallens avsnitt mer detaljerat.

## <a name="parameters"></a>Parametrar

I avsnittet parametrar i mallen anger du vilka värden som du kan ange när du distribuerar resurserna. Med dessa parameter värden kan du anpassa distributionen genom att ange värden som är anpassade för en viss miljö (till exempel utveckling, testning och produktion). Du behöver inte ange parametrar i mallen, men utan parametrar som din mall alltid distribuerar samma resurser med samma namn, platser och egenskaper.

Du är begränsad till 256 parametrar i en mall. Du kan minska antalet parametrar genom att använda objekt som innehåller flera egenskaper, som du ser i den här artikeln.

### <a name="available-properties"></a>Tillgängliga egenskaper

Tillgängliga egenskaper för en parameter är:

```json
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
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| parameterName |Ja |Parameterns namn. Måste vara en giltig JavaScript-identifierare. |
| type |Ja |Typ av parameter värde. De tillåtna typerna och värdena är **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**och **array**. |
| defaultValue |Nej |Standardvärdet för parametern, om det inte finns något värde för parametern. |
| allowedValues |Nej |Matris med tillåtna värden för parametern för att kontrol lera att rätt värde har angetts. |
| minValue |Nej |Det minsta värdet för int-typ parametrar, detta värde är inkluderat. |
| maxValue |Nej |Det maximala värdet för int-typ parametrar, detta värde är inkluderat. |
| minLength |Nej |Den minsta längden för parametrar av typen sträng, säker sträng och mat ris typ, detta värde är inkluderat. |
| maxLength |Nej |Den maximala längden för parametrar av typen sträng, säker sträng och mat ris typ, detta värde är inkluderat. |
| description |Nej |Beskrivning av den parameter som visas för användarna via portalen. Mer information finns i [kommentarer i mallar](#comments). |

### <a name="define-and-use-a-parameter"></a>Definiera och använda en parameter

I följande exempel visas en enkel parameter definition. Den definierar parameterns namn och anger att det tar ett sträng värde. Parametern accepterar bara värden som passar för den avsedda användningen. Det anger ett standardvärde när inget värde anges under distributionen. Slutligen innehåller parametern en beskrivning av användningen.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

I mallen refererar du till värdet för parametern med följande syntax:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Mall funktioner med parametrar

När du anger standardvärdet för en parameter kan du använda de flesta mall-funktionerna. Du kan använda ett annat parameter värde för att bygga ett standardvärde. Följande mall visar hur funktionen används i standardvärdet:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Du kan inte använda `reference` funktionen i avsnittet parametrar. Parametrarna utvärderas före distributionen så `reference` att funktionen inte kan hämta körnings status för en resurs. 

### <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara lättare att organisera relaterade värden genom att skicka dem som ett objekt. Den här metoden minskar också antalet parametrar i mallen.

Definiera parametern i mallen och ange ett JSON-objekt i stället för ett enda värde under distributionen. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Referera sedan till parameterns egenskaper med hjälp av punkt operatorn.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Parameter exempel mallar

Dessa exempel mallar demonstrerar några scenarier för att använda parametrar. Distribuera dem för att testa hur parametrar hanteras i olika scenarier.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder mall funktioner när du definierar standardvärden för parametrar. Mallen distribuerar inga resurser. Den skapar parameter värden och returnerar dessa värden. |
|[parameter objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar hur man använder ett objekt för en parameter. Mallen distribuerar inga resurser. Den skapar parameter värden och returnerar dessa värden. |

## <a name="variables"></a>Variabler

I avsnittet variabler skapar du värden som kan användas i hela mallen. Du behöver inte definiera variabler, men de fören klar ofta mallen genom att minska komplexa uttryck.

### <a name="available-definitions"></a>Tillgängliga definitioner

I följande exempel visas tillgängliga alternativ för att definiera en variabel:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Information om hur du `copy` använder för att skapa flera värden för en variabel finns i [variabel iteration](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definiera och använda en variabel

I följande exempel visas en variabel definition. Det skapar ett sträng värde för ett lagrings konto namn. Den använder flera mallar för att hämta ett parameter värde och sammanfogar dem till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Du använder variabeln när du definierar resursen.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variabler för konfiguration

Du kan använda komplexa JSON-typer för att definiera relaterade värden för en miljö.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

I parametrar skapar du ett värde som anger vilka konfigurations värden som ska användas.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Du hämtar de aktuella inställningarna med:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Variabel exempel mallar

Dessa exempel mallar demonstrerar några scenarier för att använda variabler. Distribuera dem för att testa hur variabler hanteras i olika scenarier. 

|Mall  |Beskrivning  |
|---------|---------|
| [variabel definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inga resurser. Den skapar variabel värden och returnerar dessa värden. |
| [konfigurations variabel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar användningen av en variabel som definierar konfigurations värden. Mallen distribuerar inga resurser. Den skapar variabel värden och returnerar dessa värden. |
| [nätverks säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Skapar en matris i rätt format för att tilldela säkerhets regler till en nätverks säkerhets grupp. |


## <a name="functions"></a>Funktioner

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i din mall. Vanligt vis definierar du ett komplext uttryck som du inte vill upprepa i hela mallen. Du kan skapa användardefinierade funktioner från uttryck och [funktioner](resource-group-template-functions.md) som stöds i mallar.

När du definierar en användar funktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som har definierats i funktionen. När du använder [funktionen parametrar](resource-group-template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda funktionen [Reference](resource-group-template-functions-resource.md#reference).
* Parametrar för funktionen kan inte ha standardvärden.

Dina funktioner kräver ett namn områdes värde för att undvika namngivnings konflikter med Template functions. I följande exempel visas en funktion som returnerar ett lagrings konto namn:

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

Du anropar funktionen med:

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
I avsnittet resurser definierar du de resurser som distribueras eller uppdateras.

### <a name="available-properties"></a>Tillgängliga egenskaper

Du definierar resurser med följande struktur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| condition | Nej | Booleskt värde som anger om resursen ska tillhandahållas under distributionen. När `true`skapas resursen under distributionen. När `false`hoppas resursen över för den här distributionen. Se [villkorlig distribution](conditional-resource-deployment.md). |
| apiVersion |Ja |Den version av REST API som ska användas för att skapa resursen. Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). |
| type |Ja |Typ av resurs. Det här värdet är en kombination av resurs leverantörens namn område och resurs typ (till exempel **Microsoft. Storage/storageAccounts**). Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). För en underordnad resurs är formatet för typen beroende av om den är kapslad i den överordnade resursen eller definieras utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| name |Ja |Namnet på resursen. Namnet måste följa de URI-komponentparametrar som definierats i RFC3986. Dessutom verifierar Azure-tjänster som visar resurs namnet till utomstående parter namnet för att kontrol lera att det inte är ett försök att använda en annan identitet. För en underordnad resurs är formatet på namnet beroende av om det är kapslat i den överordnade resursen eller definierats utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| location |Varierar |Geo-platser som stöds för den angivna resursen. Du kan välja någon av de tillgängliga platserna, men vanligt vis är det bra att välja en som är nära dina användare. Vanligt vis är det också bra att placera resurser som interagerar med varandra i samma region. De flesta resurs typer kräver en plats, men vissa typer (till exempel en roll tilldelning) kräver ingen plats. Se [Ange resurs plats](resource-location.md) |
| taggar |Nej |Taggar som är associerade med resursen. Använd taggar för att logiskt organisera resurser i din prenumeration. |
| Comment |Nej |Dina anteckningar om att dokumentera resurserna i mallen. Mer information finns i [kommentarer i mallar](resource-group-authoring-templates.md#comments). |
| kopiera |Nej |Om fler än en instans behövs, antalet resurser som ska skapas. Standard läget är parallellt. Ange serie läge när du inte vill att alla eller resurserna ska distribueras samtidigt. Mer information finns i [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nej |Resurser som måste distribueras innan den här resursen distribueras. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. När resurserna inte är beroende av varandra distribueras de parallellt. Värdet kan vara en kommaavgränsad lista över resurs namn eller resurs unika identifierare. Endast List resurser som har distribuerats i den här mallen. Resurser som inte har definierats i den här mallen måste redan finnas. Undvik att lägga till onödiga beroenden eftersom de kan minska distributionen och skapa cirkulära beroenden. Anvisningar om hur du ställer in beroenden finns i [definiera beroenden i Azure Resource Manager mallar](resource-group-define-dependencies.md). |
| properties |Nej |Resurs-/regionsspecifika konfigurations inställningar. Värdena för egenskaperna är desamma som de värden som du anger i begär ande texten för åtgärden REST API (metoden sätt) för att skapa resursen. Du kan också ange en kopierad matris för att skapa flera instanser av en egenskap. Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). |
| sku | Nej | Vissa resurser tillåter värden som definierar SKU: n som ska distribueras. Du kan till exempel ange typen av redundans för ett lagrings konto. |
| type | Nej | Vissa resurser tillåter ett värde som definierar vilken typ av resurs du distribuerar. Du kan till exempel ange vilken typ av Cosmos DB som ska skapas. |
| projektplan | Nej | Vissa resurser tillåter värden som definierar den plan som ska distribueras. Du kan till exempel ange Marketplace-avbildningen för en virtuell dator. | 
| resurser |Nej |Underordnade resurser som är beroende av den resurs som definieras. Ange endast resurs typer som tillåts av schemat för den överordnade resursen. Beroendet av den överordnade resursen är inte underförstådd. Du måste uttryckligen definiera det beroendet. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |

### <a name="resource-names"></a>Resurs namn

I allmänhet arbetar du med tre typer av resurs namn i Resource Manager:

* Resurs namn som måste vara unika.
* Resurs namn som inte måste vara unika, men som du väljer att ange ett namn som kan hjälpa dig att identifiera resursen.
* Resurs namn som kan vara generiska.

Ange ett **unikt resurs namn** för alla resurs typer som har en slut punkt för data åtkomst. Några vanliga resurs typer som kräver ett unikt namn är:

* Azure Storage<sup>1</sup> 
* Funktionen Web Apps i Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> lagrings konto namn måste också vara gemener, 24 tecken eller mindre och får inte innehålla några bindestreck.

När du anger namnet kan du antingen skapa ett unikt namn manuellt eller använda funktionen [uniqueString ()](resource-group-template-functions-string.md#uniquestring) för att generera ett namn. Du kanske också vill lägga till ett prefix eller suffix till **uniqueString** -resultatet. Genom att ändra det unika namnet kan du lättare identifiera resurs typen från namnet. Du kan till exempel skapa ett unikt namn för ett lagrings konto med hjälp av följande variabel:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

För vissa resurs typer kanske du vill ange ett **namn för identifiering**, men namnet behöver inte vara unikt. Ange ett namn som beskriver användningen eller egenskaperna för dessa resurs typer.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

För resurs typer som du främst har åtkomst till via en annan resurs kan du använda ett **generiskt namn** som är hårdkodat i mallen. Du kan till exempel ange ett standard, generiskt namn för brand Väggs regler på en SQL-Server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>outputs

I Outputs-avsnittet anger du värden som returneras från distributionen. Normalt returnerar du värden från resurser som har distribuerats.

### <a name="available-properties"></a>Tillgängliga egenskaper

I följande exempel visar strukturen för en utdata-definition:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| outputName |Ja |Namnet på värdet. Måste vara en giltig JavaScript-identifierare. |
| condition |Nej | Booleskt värde som anger om det här värdet returneras. När `true`är värdet inkluderat i utdata för distributionen. När `false`ignoreras värdet för utdata för den här distributionen. Om inget värde anges är `true`standardvärdet. |
| type |Ja |Typ av utdatavärde. Utdatavärden stöder samma datatyper som mall indataparametrar. Om du anger **SecureString** för utdatatypen visas inte värdet i distributions historiken och kan inte hämtas från en annan mall. Om du vill använda ett hemligt värde i fler än en mall lagrar du hemligheten i en Key Vault och refererar till hemligheten i parameter filen. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](resource-manager-keyvault-parameter.md). |
| value |Ja |Mallspråksuttrycket som utvärderas och returneras som utdatavärde. |

### <a name="define-and-use-output-values"></a>Definiera och Använd utdatavärden

I följande exempel visas hur du skickar tillbaka resurs-ID för en offentlig IP-adress:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

I nästa exempel visas hur du villkorligt returnerar resurs-ID: t för en offentlig IP-adress baserat på om en ny har distribuerats:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Ett enkelt exempel på villkorliga utdata finns i [mallen för villkorsstyrda utdata](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Efter distributionen kan hämta du värdet med skript. Om du använder PowerShell använder du:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Om du använder Azure CLI använder du:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Du kan hämta värdet från en länkad mall med hjälp av den [referens](resource-group-template-functions-resource.md#reference) funktion. För att få utdatavärde från en länkad mall kan hämta egenskapens värde med syntax som: `"[reference('deploymentName').outputs.propertyName.value]"`.

När du hämtar en egenskap för utdata från en länkad mall, får inte egenskapsnamnet innehålla ett bindestreck.

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte använda den `reference` funktion i avsnittet utdata i en [kapslade mallen](resource-group-linked-templates.md#link-or-nest-a-template). Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

### <a name="output-example-templates"></a>Exempel på utdata-mallar

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inte några resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Lastbalanserare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Kommentarer och metadata

Du har några alternativ för att lägga till kommentarer och metadata till din mall.

Du kan lägga till `metadata` ett objekt nästan var som helst i mallen. Resource Manager ignorerar objektet, men JSON-redigeraren kan varna dig om att egenskapen inte är giltig. I objektet definierar du de egenskaper du behöver.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

För **parametrar**lägger du till `metadata` ett objekt med `description` en egenskap.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

När du distribuerar mallen via portalen används den text som du anger i beskrivningen automatiskt som ett tips för den parametern.

![Visa parameter tips](./media/resource-group-authoring-templates/show-parameter-tip.png)

För **resurser**lägger du till `comments` ett-element eller ett metadataobjekt. I följande exempel visas både ett kommentars element och ett metadataobjekt.

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

För **utdata**lägger du till ett metadataobjekt till utdata-värdet.

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

Det går inte att lägga till ett metadataobjekt i användardefinierade funktioner.

För infogade kommentarer kan du använda `//` men den här syntaxen fungerar inte med alla verktyg. Du kan inte använda Azure CLI för att distribuera mallen med infogade kommentarer. Och du kan inte använda redigeraren för Portal mal len för att arbeta med mallar med infogade kommentarer. Om du lägger till den här typen av kommentar måste du se till att de verktyg du använder stöder infogade JSON-kommentarer.

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

I VS Code kan du ange språk läget till JSON med kommentarer. De infogade kommentarerna markeras inte längre som ogiltiga. Ändra läget:

1. Öppna val av språk läge (Ctrl + K M)

1. Välj **JSON med kommentarer**.

   ![Välj språk läge](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Information om hur du kombinerar flera mallar under distributionen finns i [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Rekommendationer om hur du skapar mallar finns i [metod tips för Azure Resource Manager mallar](template-best-practices.md).
* Rekommendationer för att skapa Resource Manager-mallar som du kan använda i alla Azure-miljöer och Azure Stack finns i [utveckla Azure Resource Manager mallar för moln konsekvens](templates-cloud-consistency.md).

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
ms.date: 03/04/2019
ms.author: tomfitz
ms.openlocfilehash: f67741417c6d31c4adf1d063aac3bd3ccc310fde
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440258"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Förstå strukturen och syntaxen för Azure Resource Manager-mallar

Den här artikeln beskriver strukturen för en Azure Resource Manager-mall. Den anger de olika avsnitten i en mall och egenskaperna som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution.

Den här artikeln är avsedd för användare som har bekant med Resource Manager-mallar. Den innehåller detaljerad information om strukturen och syntaxen för mallen. Om du vill att en introduktion till hur du skapar en mall finns i [skapa din första Azure Resource Manager-mall](resource-manager-create-first-template.md).

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
| $schema |Ja |Platsen för schemat JSON-fil som beskriver versionen av mallspråk.<br><br> För distribution av resursgrupper, använder du: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>För prenumerationsdistributioner av, använder du: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Ja |Versionen av mallen (till exempel 1.0.0.0). Du kan ange ett värde för det här elementet. Använd det här värdet till dokumentet betydande förändringar i mallen. Det här värdet kan användas för att se till att rätt mall används när du distribuerar resurser med hjälp av mallen. |
| apiProfile |Nej | En API-version som fungerar som en uppsättning API-versioner för resurstyper. Använd det här värdet för att undvika att behöva ange API-versioner för varje resurs i mallen. När du anger en profil för API-version och inte anger en API-version för resurstypen använder Resource Manager API-versionen från profilen för den resurstypen. Mer information finns i [spåra versioner med hjälp av API-profiler](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nej |Värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen. |
| [Variabler](#variables) |Nej |Värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck. |
| [Funktioner](#functions) |Nej |Användardefinierade funktioner som är tillgängliga i mallen. |
| [Resurser](#resources) |Ja |Resurstyper som är distribuerade eller uppdateras i en resursgrupp eller prenumeration. |
| [utdata](#outputs) |Nej |Värden som returneras efter distributionen. |

Varje element har egenskaper som du kan ange. Den här artikeln beskriver avsnitt i mallen i större detalj.

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

Du är begränsad till 256 parametrar i en mall. Du kan minska antalet parametrar av objekt som innehåller flera egenskaper som visas i den här artikeln.

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
| parameterName |Ja |Namnet på parametern. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av parametervärdet. Tillåtna typer och värden är **sträng**, **securestring**, **int**, **bool**, **objekt**, **secureObject**, och **matris**. |
| Standardvärde |Nej |Standardvärdet för parametern, om inget värde har angetts för parametern. |
| allowedValues |Nej |Matris med tillåtna värden för parametern för att se till att rätt värde har angetts. |
| minValue |Nej |Det minsta värdet för parametrar av typen int det här värdet är inkluderande. |
| maxValue |Nej |Det maximala värdet för parametrar av typen int det här värdet är inkluderande. |
| minLength |Nej |Den minsta längden för string, säker sträng och matris typparametrar det här värdet är inkluderande. |
| maxLength |Nej |Den maximala längden för string, säker sträng och matris typparametrar det här värdet är inkluderande. |
| beskrivning |Nej |Beskrivning av den parameter som visas för användarna via portalen. Mer information finns i [kommentarer i mallar](#comments). |

### <a name="define-and-use-a-parameter"></a>Definiera och Använd en parameter

I följande exempel visar en enkel parameterdefinition. Den definierar namnet på parametern och anger att det tar ett strängvärde. Parametern accepterar endast värden som passar för att fungera. Den anger ett standardvärde om inget värde anges under distributionen. Slutligen innehåller parametern en beskrivning av dess användning.

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

I mallen referera du till värdet för parametern med följande syntax:

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

### <a name="template-functions-with-parameters"></a>Mallfunktioner med parametrar

När du anger standardvärdet för en parameter, kan du använda de flesta Mallfunktioner. Du kan använda ett annat parametervärde för att skapa ett standardvärde. Följande mall visar hur du använder funktioner i standardvärdet:

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

Du kan inte använda den `reference` funktionen i parameteravsnittet. Parametrar utvärderas före distributionen så `reference` funktionen kan inte hämta runtime-tillståndet för en resurs. 

### <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara enklare att organisera relaterade värden genom att skicka dem i som ett-objekt. Den här metoden minskar också antalet parametrar i mallen.

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

Sedan referera subegenskaper för parametern genom att använda punktoperatorn.

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

### <a name="parameter-example-templates"></a>Parametern exempel på mallar

Dessa exempel på mallar visar några scenarier för att använda parametrar. Distribuera dem för att testa hur parametrar ska hanteras i olika scenarier.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder Mallfunktioner när du definierar standardvärden för parametrar. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |
|[Parametern-objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar hur du använder ett objekt för en parameter. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |

## <a name="variables"></a>Variabler

I avsnittet variables kan skapa du värden som kan användas i hela din mall. Du behöver inte definiera variabler, men de förenkla ofta din mall genom att minska komplexa uttryck.

### <a name="available-definitions"></a>Tillgängliga definitioner

I följande exempel visas de tillgängliga alternativen för att definiera en variabel:

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

Information om hur du använder `copy` för att skapa flera värden för en variabel, se [variabeln iteration](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definiera och använda en variabel

I följande exempel visas en variabeldefinitionen. Ett strängvärde för namnet på ett lagringskonto skapas. Den använder flera Mallfunktioner för att hämta ett parametervärde och sammanfogar den till en unik sträng.

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

### <a name="configuration-variables"></a>Av konfigurationsvariabler

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

I parametrar skapar du ett värde som anger vilken konfiguration som värden som ska användas.

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

Du kan hämta de aktuella inställningarna med:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Exempel på åtgärdsvariabler mallar

Dessa exempel på mallar visar några scenarier för att använda variabler. Distribuera dem för att testa hur variabler ska hanteras i olika scenarier. 

|Mall  |Beskrivning  |
|---------|---------|
| [variabeln definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inte några resurser. Den skapar variabelvärdena och returnerar dessa värden. |
| [konfiguration av variabel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar hur du använder en variabel som definierar konfigurationsvärden. Mallen distribuerar inte några resurser. Den skapar variabelvärdena och returnerar dessa värden. |
| [Nätverkssäkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameterfilen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Skapar en matris i rätt format för att tilldela säkerhetsregler till en nätverkssäkerhetsgrupp. |


## <a name="functions"></a>Functions

Du kan skapa egna funktioner i din mall. Dessa funktioner är tillgängliga för användning i din mall. Normalt kan definiera du komplicerat uttryck som du inte vill upprepa i hela din mall. Du kan skapa användardefinierade funktioner från uttryck och [functions](resource-group-template-functions.md) som stöds i mallar.

När du definierar en user-funktionen, finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som definieras i funktionen. När du använder den [parametrar funktionen](resource-group-template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrar för funktionen.
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
I resursavsnittet kan du definiera de resurser som är distribuerade eller uppdateras.

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
| villkor | Nej | Booleskt värde som anger om resursen ska etableras under den här distributionen. När `true`, där resursen skapas under distributionen. När `false`, resursen är hoppades över för den här distributionen. |
| apiVersion |Ja |Version av REST-API för att använda för att skapa resursen. Information om tillgängliga värden finns [mallreferensen](/azure/templates/). |
| typ |Ja |Typ av resursen. Det här värdet är en kombination av namnområde med resursprovidern och resurstypen (till exempel **Microsoft.Storage/storageAccounts**). Information om tillgängliga värden finns [mallreferensen](/azure/templates/). |
| namn |Ja |Resursens namn. Namnet måste följa URI-komponent begränsningar som definierats i RFC3986. Dessutom är Azure-tjänster som exponerar resursnamnet externa parter Kontrollera namnet och kontrollera att det inte ett försök att imitera en annan identitet. |
| location |Varierar |Geo-platser som stöds för den angivna resursen. Du kan välja någon av de tillgängliga platserna, men vanligtvis det vara bra att välja ett som är nära användarna. Vanligtvis är det också vara bra att placera resurser som interagerar med varandra i samma region. De flesta typer av resurser kräver en plats, men vissa typer (till exempel en rolltilldelning) kräver inte en plats. |
| tags |Nej |Taggar som är kopplade till resursen. Lägga till taggar för att organisera resurser logiskt i din prenumeration. |
| kommentarer |Nej |Dina anteckningar för att dokumentera resurserna i mallen. Mer information finns i [kommentarer i mallar](resource-group-authoring-templates.md#comments). |
| kopiera |Nej |Om fler än en instans, hur många resurser för att skapa. Standardläget är parallell. Ange seriell läge när du inte vill att alla eller resurserna som ska distribueras på samma gång. Mer information finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nej |Resurser som måste distribueras innan den här resursen har distribuerats. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. När resurserna inte är beroende av varandra, är de distribueras parallellt. Värdet kan vara en kommaavgränsad lista över en resurs namn eller resurs unika identifierare. Endast lista över resurser som distribueras i den här mallen. Resurser som inte har definierats i den här mallen måste redan finnas. Undvik att lägga till onödiga beroenden som de kan sakta distributionen och skapa cirkulärt tjänstberoende. Anvisningar för inställningen beroenden finns i [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md). |
| properties |Nej |Resurs-specifika konfigurationsinställningar. Värdena för egenskaperna är samma som de värden som du anger i begärandetexten för REST API-åtgärd (PUT-metoden) att skapa resursen. Du kan också ange en kopia matris för att skapa flera instanser av en egenskap. Information om tillgängliga värden finns [mallreferensen](/azure/templates/). |
| sku | Nej | Vissa resurser kan värden som definierar SKU för att distribuera. Du kan till exempel ange typen av redundans för ett lagringskonto. |
| typ | Nej | Vissa resurser kan ett värde som definierar typ av resurs som du distribuerar. Du kan till exempel ange vilken typ av Cosmos DB för att skapa. |
| plan | Nej | Vissa resurser kan värden som definierar planerar att distribuera. Du kan till exempel ange marketplace-avbildning för en virtuell dator. | 
| resurser |Nej |Underordnade resurser som är beroende av resursen som definieras. Ange endast resurstyper som tillåts av schemat för den överordnade resursen. Den fullständigt kvalificerade typ av den underordnade resursen omfattar resurstyp överordnade som **Microsoft.Web/sites/extensions**. Beroende på den överordnade resursen är inte underförstådd. Du måste uttryckligen definiera det beroendet. |

### <a name="condition"></a>Tillstånd

När du måste bestämma under distributionen om du vill skapa en resurs eller inte, använda den `condition` element. Värdet för det här elementet matchas till true eller false. När värdet är true, skapas resursen. När värdet är false är inte resursen skapas. Värdet kan bara tillämpas på hela resursen.

Normalt använder du det här värdet när du vill skapa en ny resurs eller Använd en befintlig. Till exempel vill ange om ett nytt lagringskonto har distribuerats eller ett befintligt lagringskonto används, använder du:

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

För en komplett exempel-mall som använder den `condition` element, se [virtuell dator med ett nytt eller befintligt virtuellt nätverk, lagring och offentlig IP-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

### <a name="resource-names"></a>Resursnamn

I allmänhet bör arbeta du med tre typer av resursnamn i Resource Manager:

* Resursnamn som måste vara unika.
* Resursnamn som inte behöver vara unikt, men du kan du välja att ange ett namn som hjälper dig att identifiera resursen.
* Resursnamn som kan vara allmän.

Ange en **unika resursnamn** för någon resurstyp som har en slutpunkt för åtkomst av data. Vissa vanliga typer av resurser som kräver ett unikt namn är:

* Azure Storage<sup>1</sup> 
* Funktionen Web Apps i Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> lagringskontonamn måste också vara gemener, 24 tecken eller mindre, och inte har någon bindestreck.

När du anger namnet, du kan manuellt skapa ett unikt namn eller använda den [uniqueString()](resource-group-template-functions-string.md#uniquestring) funktionen för att skapa ett namn. Du kanske också vill lägga till ett prefix eller suffix till den **uniqueString** resultatet. Ändra det unika namnet kan du enkelt identifiera resurstyp från namn. Du kan till exempel generera ett unikt namn för ett lagringskonto med hjälp av följande variabel:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

För vissa typer av resurser, kanske du vill ange en **namn för att identifiera**, men namnet behöver inte vara unika. Ange ett namn som beskriver den användning eller egenskaper för dessa typer av resurser.

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

För resurstyperna som du främst åtkomst via en annan resurs, kan du använda en **allmänt namn** som är hårdkodad i mallen. Du kan till exempel ange ett standard, allmän namn på brandväggsregler på en SQLServer:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Resursplats

När du distribuerar en mall måste du ange en plats för varje resurs. Olika resurstyper stöds på olika platser. För att få platser som stöds för en resurstyp kan se [Azure resursproviders och resurstyper](resource-manager-supported-services.md).

Använd en parameter för att ange plats för resurser och ange standardvärdet till `resourceGroup().location`.

I följande exempel visas ett lagringskonto som har distribuerats till en plats som anges som en parameter:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Underordnade resurser

Du kan också definiera en uppsättning underordnade resurser inom vissa typer av resurser. Underordnade resurser finns resurser som finns bara inom ramen för en annan resurs. Till exempel kan inte en SQL-databas finnas utan en SQLServer så att databasen är en underordnad till servern. Du kan definiera databasen i definitionen för servern.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

När kapslade, vilken anges till `databases` men dess fullständiga resurstypen är `Microsoft.Sql/servers/databases`. Du inte anger `Microsoft.Sql/servers/` eftersom det antas från den överordnade resurstypen. Namnet på underordnade resursen är inställd `exampledatabase` men det fullständiga namnet innehåller namnet på överordnade. Du inte anger `exampleserver` eftersom det antas från den överordnade resursen.

Formatet för den underordnade resurstypen är: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formatet på namnet på underordnade resursen är: `{parent-resource-name}/{child-resource-name}`

Men du behöver definiera databasen på servern. Du kan definiera den underordnade resursen på den översta nivån. Du kan använda den här metoden om den överordnade resursen inte är distribuerat i samma mall, eller om vill använda `copy` att skapa fler än en underordnad resurs. Med den här metoden måste du ange fullständig resurstypen och inkludera namnet på överordnade resursen i namnet på underordnade resursen.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

När en fullständiga referens till en resurs, inte bara en sammanfogning av två den för att kombinera segment från typ och namn. Använd i stället en sekvens med efter namnområdet, *typnamn/* par från minst specifika att mest specifika:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` stämmer `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` stämmer inte

## <a name="outputs"></a>Utdata

I Outputs-avsnittet anger du värden som returneras från distributionen. Normalt kan returnera du värden från resurser som har distribuerats.

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
| villkor |Nej | Booleskt värde som anger om utdata detta värde returneras. När `true`, värdet ingår i utdata för distributionen. När `false`, hoppas över värdet för den här distributionen. Om inget värde anges är standardvärdet `true`. |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöder samma datatyper som mall indataparametrar. |
| värde |Ja |Mallspråksuttrycket som utvärderas och returneras som utdatavärde. |

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

I nästa exempel visas hur du returnerar villkorligt resurs-ID för en offentlig IP-adress baserat på om en ny något har distribuerats:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Ett enkelt exempel på villkorlig utdata Se [villkorlig utdata mallen](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

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

I följande exempel visas hur du ställer in IP-adressen för en belastningsutjämnare genom att hämta ett värde från en länkad mall.

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

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Rekommendationer om hur du skapar mallar finns i [Metodtips för Azure Resource Manager-mall](template-best-practices.md).
* Rekommendationer om hur du skapar Resource Manager-mallar som du kan använda i alla Azure-miljöer och Azure Stack finns i [utveckla Azure Resource Manager-mallar för molnet konsekvens](templates-cloud-consistency.md).

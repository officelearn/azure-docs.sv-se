---
title: Mallstruktur och syntax
description: Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med den deklarativa JSON-syntaxen.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4e8334e4ddfaee52c5d1aa68fb8689fcde0a6cbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459998"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Förstå strukturen och syntaxen för ARM-mallar

I den här artikeln beskrivs strukturen för en ARM-mall (Azure Resource Manager). Den visar de olika avsnitten i en mall och de egenskaper som är tillgängliga i dessa avsnitt.

Den här artikeln är avsedd för användare som har viss förtrogenhet med ARM-mallar. Den innehåller detaljerad information om mallens struktur. En steg-för-steg-självstudiekurs som vägleder dig genom hur du skapar en mall finns i [Självstudiekurs: Skapa och distribuera din första Azure Resource Manager-mall](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Mallformat

I sin enklaste struktur har en mall följande element:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| $schema |Ja |Plats för JSON-schemafilen som beskriver versionen av mallspråket. Versionsnumret du använder beror på distributionens omfattning och din JSON-redigerare.<br><br>Om du använder [VS-kod med tillägget Azure Resource Manager-verktyg](use-vs-code-to-create-template.md)använder du den senaste versionen för resursgruppsdistributioner:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Andra redigerare (inklusive Visual Studio) kanske inte kan bearbeta schemat. För dessa redaktörer, använd:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Använd för prenumerationsdistributioner:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Använd:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Använd:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Ja |Version av mallen (till exempel 1.0.0.0). Du kan ange vilket värde som helst för det här elementet. Använd det här värdet om du vill dokumentera betydande ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används. |
| apiProfile |Inga | En API-version som fungerar som en samling API-versioner för resurstyper. Använd det här värdet om du vill undvika att behöva ange API-versioner för varje resurs i mallen. När du anger en API-profilversion och inte anger en API-version för resurstypen använder Resource Manager API-versionen för den resurstyp som definieras i profilen.<br><br>EGENSKAPEN API-profil är särskilt användbar när du distribuerar en mall till olika miljöer, till exempel Azure Stack och global Azure. Använd API-profilversionen för att se till att mallen automatiskt använder versioner som stöds i båda miljöerna. En lista över de aktuella API-profilversionerna och de RESURS-API-versioner som definierats i profilen finns i [API-profil](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Mer information finns i [Spåra versioner med API-profiler](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [Parametrar](#parameters) |Inga |Värden som anges när distributionen körs för att anpassa resursdistributionen. |
| [Variabler](#variables) |Inga |Värden som används som JSON-fragment i mallen för att förenkla mallspråkuttryck. |
| [Funktioner](#functions) |Inga |Användardefinierade funktioner som är tillgängliga i mallen. |
| [Resurser](#resources) |Ja |Resurstyper som distribueras eller uppdateras i en resursgrupp eller prenumeration. |
| [Utgångar](#outputs) |Inga |Värden som returneras efter distributionen. |

Varje element har egenskaper som du kan ange. I den här artikeln beskrivs avsnitten i mallen mer i detalj.

## <a name="parameters"></a>Parametrar

I avsnittet parametrar i mallen anger du vilka värden du kan ange när du distribuerar resurserna. Du är begränsad till 256 parametrar i en mall. Du kan minska antalet parametrar genom att använda objekt som innehåller flera egenskaper.

De tillgängliga egenskaperna för en parameter är:

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
| parameter-namn |Ja |Parameterns namn. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av parametervärde. De tillåtna typerna och värdena är **sträng**, **securestring**, **int**, **bool**, **object**, **secureObject**och **array**. Se [Datatyper](#data-types). |
| Standardvärde |Inga |Standardvärde för parametern, om inget värde anges för parametern. |
| allowedValues |Inga |Matris med tillåtna värden för parametern för att se till att rätt värde anges. |
| minVärde |Inga |Det minsta värdet för int-typparametrar, det här värdet är inklusive. |
| Maxvalue |Inga |Det maximala värdet för int typ parametrar, är detta värde inklusive. |
| Minlength |Inga |Den minsta längden för parametrar för sträng, säker sträng och matristyp, det här värdet är inklusive. |
| Maxlength |Inga |Den maximala längden för parametrar för sträng, säker sträng och matristyp, det här värdet är inklusive. |
| description |Inga |Beskrivning av parametern som visas för användare via portalen. Mer information finns [i Kommentarer i mallar](#comments). |

Exempel på hur du använder parametrar finns [i Parametrar i Azure Resource Manager-mallar](template-parameters.md).

### <a name="data-types"></a>Datatyper

För heltal som skickas som infogade parametrar kan intervallet med värden begränsas av SDK- eller kommandoradsverktyget som du använder för distribution. När du till exempel använder PowerShell för att distribuera en mall kan heltalstyper variera från -2147483648 till 2147483647. Om du vill undvika den här begränsningen anger du stora heltalsvärden i en [parameterfil](parameter-files.md). Resurstyper tillämpar sina egna gränser för heltalsegenskaper.

Omger inte värdet med citattecken när du anger booleska värden och heltalsvärden i mallen. Start- och slutsträngvärden med dubbla citattecken.

Objekt börjar med ett vänsterparentes och slutar med ett högerparentes. Matriser börjar med en vänster parentes och slutar med en höger parentes.

Säkra strängar och säkra objekt kan inte läsas efter resursdistributionen.

Exempel på formateringsdatatyper finns i [Format för parametertyp](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variabler

I variabelavsnittet konstruerar du värden som kan användas i hela mallen. Du behöver inte definiera variabler, men de förenklar ofta mallen genom att minska komplexa uttryck.

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

Information om `copy` hur du använder för att skapa flera värden för en variabel finns i [Variabeliteration](copy-variables.md).

Exempel på hur du använder variabler finns [i Variabler i Azure Resource Manager-mallen](template-variables.md).

## <a name="functions"></a>Funktioner

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i mallen. Vanligtvis definierar du komplicerade uttryck som du inte vill upprepa i hela mallen. Du skapar användardefinierade funktioner från uttryck och [funktioner](template-functions.md) som stöds i mallar.

När du definierar en användarfunktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som definieras i funktionen. När du använder [parameterfunktionen](template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda [referensfunktionen](template-functions-resource.md#reference).
* Parametrar för funktionen kan inte ha standardvärden.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
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
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| namnområde |Ja |Namnområde för de anpassade funktionerna. Används för att undvika att namnge konflikter med mallfunktioner. |
| funktionsnamn |Ja |Namnet på den anpassade funktionen. När du anropar funktionen kombinerar du funktionsnamnet med namnområdet. Om du till exempel vill anropa en funktion med namnet `"[contoso.uniqueName()]"`uniqueName i namnområdet contoso använder du . |
| parameter-namn |Inga |Namnet på den parameter som ska användas i den anpassade funktionen. |
| parameter-värde |Inga |Typ av parametervärde. De tillåtna typerna och värdena är **sträng**, **securestring**, **int**, **bool**, **object**, **secureObject**och **array**. |
| utdatatyp |Ja |Typ av utdatavärde. Utdatavärden stöder samma typer som funktionsinmatningsparametrar. |
| utdatavärde |Ja |Mallspråksuttryck som utvärderas och returneras från funktionen. |

Exempel på hur du använder anpassade funktioner finns [i Användardefinierade funktioner i Azure Resource Manager-mallen](template-user-defined-functions.md).

## <a name="resources"></a>Resurser

I avsnittet resurser definierar du de resurser som distribueras eller uppdateras.

Du definierar resurser med följande struktur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
| Villkor | Inga | Booleskt värde som anger om resursen kommer att etableras under den här distributionen. När `true`skapas resursen under distributionen. När `false`hoppas resursen över för den här distributionen. Se [villkoret](conditional-resource-deployment.md). |
| typ |Ja |Typ av resurs. Det här värdet är en kombination av resursproviderns namnområde och resurstypen (till exempel **Microsoft.Storage/storageAccounts**). Information om vilka värden som är tillgängliga finns i [mallreferensen](/azure/templates/). För en underordnad resurs beror typens format på om den är kapslad inom den överordnade resursen eller definieras utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| apiVersion |Ja |Version av REST API som ska användas för att skapa resursen. Information om vilka värden som är tillgängliga finns i [mallreferensen](/azure/templates/). |
| namn |Ja |Namn på resursen. Namnet måste följa URI-komponentbegränsningar som definieras i RFC3986. Azure-tjänster som visar resursnamnet för externa parter validerar namnet för att se till att det inte är ett försök att förfalska en annan identitet. För en underordnad resurs beror namnets format på om det är kapslat inom den överordnade resursen eller definieras utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| Kommentarer |Inga |Anteckningarna för att dokumentera resurserna i mallen. Mer information finns [i Kommentarer i mallar](template-syntax.md#comments). |
| location |Varierar |Geo-platser för den angivna resursen som stöds. Du kan välja någon av de tillgängliga platserna, men vanligtvis är det vettigt att välja en som är nära dina användare. Vanligtvis är det också meningsfullt att placera resurser som interagerar med varandra i samma region. De flesta resurstyper kräver en plats, men vissa typer (till exempel en rolltilldelning) kräver ingen plats. Se [Ange resursplats](resource-location.md). |
| dependsOn |Inga |Resurser som måste distribueras innan den här resursen distribueras. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. När resurserna inte är beroende av varandra distribueras de parallellt. Värdet kan vara en kommaavgränsad lista över ett resursnamn eller unika resursidentifierare. Endast lista resurser som distribueras i den här mallen. Resurser som inte har definierats i den här mallen måste redan finnas. Undvik att lägga till onödiga beroenden eftersom de kan göra distributionen långsammare och skapa cirkulära beroenden. Information om hur du anger beroenden finns [i Definiera beroenden i Azure Resource Manager-mallar](define-resource-dependency.md). |
| tags |Inga |Taggar som är associerade med resursen. Använd taggar för att logiskt ordna resurser i din prenumeration. |
| sku | Inga | Vissa resurser tillåter värden som definierar SKU att distribuera. Du kan till exempel ange vilken typ av redundans för ett lagringskonto. |
| Typ | Inga | Vissa resurser tillåter ett värde som definierar vilken typ av resurs du distribuerar. Du kan till exempel ange vilken typ av Cosmos DB som ska skapas. |
| Kopiera |Inga |Om mer än en instans behövs, antalet resurser att skapa. Standardläget är parallellt. Ange serieläge när du inte vill att alla eller resurserna ska distribuera samtidigt. Mer information finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md). |
| planera | Inga | Vissa resurser tillåter värden som definierar planen att distribuera. Du kan till exempel ange marketplace-avbildningen för en virtuell dator. |
| properties |Inga |Resursspecifika konfigurationsinställningar. Värdena för egenskaperna är desamma som de värden som du anger i begärandetexten för REST API-åtgärden (PUT-metoden) för att skapa resursen. Du kan också ange en kopiatray för att skapa flera instanser av en egenskap. Information om vilka värden som är tillgängliga finns i [mallreferensen](/azure/templates/). |
| resources |Inga |Underordnade resurser som är beroende av den resurs som definieras. Ange endast resurstyper som tillåts av schemat för den överordnade resursen. Beroende av den överordnade resursen är inte underförstått. Du måste uttryckligen definiera det beroendet. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |

## <a name="outputs"></a>Utdata

I avsnittet Utdata anger du värden som returneras från distributionen. Vanligtvis returnerar du värden från resurser som har distribuerats.

I följande exempel visas strukturen för en utdatadefinition:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| utdatanamn |Ja |Namnet på utdatavärdet. Måste vara en giltig JavaScript-identifierare. |
| Villkor |Inga | Booleskt värde som anger om det här utdatavärdet returneras. När `true`inkluderas värdet i utdata för distributionen. När `false`hoppas utdatavärdet över för den här distributionen. När det inte anges är `true`standardvärdet . |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöder samma typer som mallindataparametrar. Om du anger **säker information** för utdatatypen visas inte värdet i distributionshistoriken och kan inte hämtas från en annan mall. Om du vill använda ett hemligt värde i mer än en mall lagrar du hemligheten i ett Key Vault och refererar till hemligheten i parameterfilen. Mer information finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](key-vault-parameter.md). |
| värde |Inga |Mallspråksuttryck som utvärderas och returneras som utdatavärde. Ange antingen **värde** eller **kopia**. |
| Kopiera |Inga | Används för att returnera mer än ett värde för en utdata. Ange **värde** eller **kopia**. Mer information finns [i Utdataiterationer i Azure Resource Manager-mallar](copy-outputs.md). |

Exempel på hur du använder utdata finns [i Utdata i Azure Resource Manager-mallen](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Kommentarer och metadata

Du har några alternativ för att lägga till kommentarer och metadata i mallen.

### <a name="comments"></a>Kommentarer

För infogade kommentarer kan `//` du `/* ... */` använda antingen eller men den här syntaxen fungerar inte med alla verktyg. Du kan inte använda portalmallredigeraren för att arbeta med mallar med infogade kommentarer. Om du lägger till den här kommentarsstilen kontrollerar du att de verktyg du använder stöder inline JSON-kommentarer.

> [!NOTE]
> Om du vill distribuera mallar med kommentarer `--handle-extended-json-format` med hjälp av Azure CLI måste du använda växeln.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

I Visual Studio-kod kan [Azure Resource Manager Tools-tillägget](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) automatiskt identifiera Resource Manager-mallen och ändra språkläget därefter. Om du ser **Azure Resource Manager-mallen** längst ned till höger i VS-kod kan du använda infogade kommentarer. Infogade kommentarer markeras inte längre som ogiltiga.

![Mallläge för Visual Studio Code Azure Resource Manager](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

Du kan `metadata` lägga till ett objekt nästan var som helst i mallen. Resource Manager ignorerar objektet, men din JSON-redigerare kan varna dig om att egenskapen inte är giltig. Definiera de egenskaper du behöver i objektet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

För **parametrar**lägger du till ett `metadata` objekt med en `description` egenskap.

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

![Visa parametertips](./media/template-syntax/show-parameter-tip.png)

För **resurser**lägger du till ett `comments` element eller ett metadataobjekt. I följande exempel visas både ett kommentarselement och ett metadataobjekt.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

För **utdata**lägger du till ett metadataobjekt i utdatavärdet.

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

Du kan inte lägga till ett metadataobjekt i användardefinierade funktioner.

## <a name="multi-line-strings"></a>Strängar med flera rader

Du kan dela upp en sträng i flera rader. Se till exempel platsegenskapen och en av kommentarerna i följande JSON-exempel.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Om du vill distribuera mallar med strängar med flera `--handle-extended-json-format` rader med hjälp av Azure CLI måste du använda växeln.

## <a name="next-steps"></a>Nästa steg

* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från en mall finns i [Azure Resource Manager Template Functions](template-functions.md).
* Information om hur du kombinerar flera mallar under distributionen finns i [Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Rekommendationer om hur du skapar mallar finns i [metodtips för Azure Resource Manager-mall .](template-best-practices.md)
* Rekommendationer om hur du skapar Resource Manager-mallar som du kan använda i alla Azure-miljöer och Azure Stack finns i [Utveckla Azure Resource Manager-mallar för molnkonsekvens](templates-cloud-consistency.md).

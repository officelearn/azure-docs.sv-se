---
title: Länka mallar för distribution
description: Beskriver hur du använder länkade mallar i en Azure Resource Manager-mall för att skapa en modulär malllösning. Visar hur du skickar parametervärden, anger en parameterfil och dynamiskt skapade webbadresser.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131915"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Använda länkade och nästlade mallar vid distribution av Azure-resurser

Om du vill distribuera komplexa lösningar kan du dela upp mallen i många relaterade mallar och sedan distribuera dem tillsammans via en huvudmall. De relaterade mallarna kan vara separata filer eller mallsyntax som är inbäddad i huvudmallen. I den här artikeln används den **länkade termen mall** för att referera till en separat mallfil som refereras via en länk från huvudmallen. Den använda termen **kapslad mall** för att referera till inbäddad mallsyntax i huvudmallen.

För små till medelstora lösningar är en enda mall lättare att förstå och underhålla. Du kan se alla resurser och värden i en enda fil. I avancerade scenarier kan du med länkade mallar dela upp lösningen i riktade komponenter. Du kan enkelt återanvända dessa mallar för andra scenarier.

En självstudiekurs finns i [Självstudiekurs: skapa länkade Azure Resource Manager-mallar](template-tutorial-create-linked-templates.md).

> [!NOTE]
> För länkade eller kapslade mallar kan du bara använda [inkrementellt distributionsläge.](deployment-modes.md)
>

## <a name="nested-template"></a>Kapslad mall

Om du vill kapsla en mall lägger du till en [distributionsresurs](/azure/templates/microsoft.resources/deployments) i huvudmallen. Ange **template** mallsyntaxen i mallegenskapen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

I följande exempel distribueras ett lagringskonto via en kapslad mall.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Uttrycksutvärderingsscope i kapslade mallar

När du använder en kapslad mall kan du ange om malluttryck ska utvärderas inom ramen för den överordnade mallen eller den kapslade mallen. Scopet avgör hur parametrar, variabler och funktioner som [resourceGroup](template-functions-resource.md#resourcegroup) och [prenumeration](template-functions-resource.md#subscription) löses.

Du kan ange `expressionEvaluationOptions` omfattningen via egenskapen. Egenskapen `expressionEvaluationOptions` är som standard `outer`inställd på , vilket innebär att den använder det överordnade mallomfånget. Ange värdet `inner` till att låta uttryck utvärderas inom omfånget för den kapslade mallen.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

Följande mall visar hur malluttryck löses enligt omfånget. Den innehåller en `exampleVar` variabel med namnet som definieras i både den överordnade mallen och den kapslade mallen. Värdet för variabeln returneras.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Värdet på `exampleVar` ändringarna beroende på `scope` egenskapens värde i `expressionEvaluationOptions`. I följande tabell visas resultaten för båda scopea.

| `expressionEvaluationOptions` `scope` | Resultat |
| ----- | ------ |
| Inre | från kapslad mall |
| yttre (eller standard) | från den överordnade mallen |

I följande exempel distribueras en SQL-server och en nyckelvalvshemlighet som ska användas för lösenordet hämtas. Scopet är `inner` inställt på eftersom det dynamiskt skapar `adminPassword.reference.keyVault` nyckelvalvs-ID (se i de yttre mallarna) `parameters`och skickar det som en parameter till den kapslade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> När scopet `outer`är inställt på kan `reference` du inte använda funktionen i utdataavsnittet i en kapslad mall för en resurs som du har distribuerat i den kapslade mallen. Om du vill returnera värdena för en distribuerad resurs i en kapslad mall använder `inner` du antingen scope eller konverterar den kapslade mallen till en länkad mall.

## <a name="linked-template"></a>Länkad mall

Om du vill länka en mall lägger du till en [distributionsresurs](/azure/templates/microsoft.resources/deployments) i huvudmallen. Ange URI för mallen som ska inkluderas i egenskapen **templateLink.** I följande exempel länkar du till en mall som distribuerar ett nytt lagringskonto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

När du refererar till en länkad mall får värdet för `uri` inte vara en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Du måste ange ett URI-värde som kan hämtas som **http** eller **https**. 

> [!NOTE]
>
> Du kan referera till mallar med hjälp av parametrar som i `_artifactsLocation` slutändan matchas till något som använder **http** eller **https,** till exempel med hjälp av parametern så här:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



Resurshanteraren måste kunna komma åt mallen. Ett alternativ är att placera den länkade mallen i ett lagringskonto och använda URI för det objektet.

### <a name="parameters-for-linked-template"></a>Parametrar för länkad mall

Du kan ange parametrarna för den länkade mallen antingen i en extern fil eller i infogad fil. När du tillhandahåller en extern parameterfil använder du egenskapen **parametersLink:**

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

Använd egenskapen **parametrar** om du vill skicka parametervärden.

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2018-05-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
    }
   }
  }
]
```

Du kan inte använda både infogade parametrar och en länk till en parameterfil. Distributionen misslyckas med `parametersLink` ett `parameters` fel när båda och anges.

## `contentVersion`

Du behöver inte tillhandahålla `contentVersion` egendomen `templateLink` eller `parametersLink` boendet. Om du inte anger `contentVersion`en distribueras den aktuella versionen av mallen. Om du anger ett värde för innehållsversionen måste den matcha versionen i den länkade mallen. Annars misslyckas distributionen med ett fel.

## <a name="using-variables-to-link-templates"></a>Använda variabler för att länka mallar

De tidigare exemplen visade hårdkodade URL-värden för malllänkarna. Den här metoden kan fungera för en enkel mall, men det fungerar inte bra för en stor uppsättning modulära mallar. I stället kan du skapa en statisk variabel som lagrar en bas-URL för huvudmallen och sedan dynamiskt skapa webbadresser för de länkade mallarna från den bas-URL:en. Fördelen med den här metoden är att du enkelt kan flytta eller dela mallen eftersom du bara behöver ändra den statiska variabeln i huvudmallen. Huvudmallen skickar rätt URI:er i hela den neddelade mallen.

I följande exempel visas hur du använder en bas-URL för att skapa två webbadresser för länkade mallar **(sharedTemplateUrl** och **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Du kan också använda [deployment()](template-functions-deployment.md#deployment) för att hämta bas-URL:en för den aktuella mallen och använda den för att hämta URL:en för andra mallar på samma plats. Den här metoden är användbar om mallplatsen ändras eller om du vill undvika att url:er kodar hårt i mallfilen. Egenskapen templateLink returneras endast när du länkar till en fjärrmall med en URL. Om du använder en lokal mall är den egenskapen inte tillgänglig.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

I slutändan skulle du `uri` använda variabeln i egenskapen för en `templateLink` egenskap.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Använda kopia

Om du vill skapa flera instanser av en resurs med en kapslad mall lägger du till kopieringselementet på nivån för **Microsoft.Resources/deployments-resursen.** Om omfånget är inre kan du lägga till kopian i den kapslade mallen.

Följande exempelmall visar hur du använder kopia med en kapslad mall.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="get-values-from-linked-template"></a>Hämta värden från den länkade mallen

Om du vill hämta ett utdatavärde från en `"[reference('deploymentName').outputs.propertyName.value]"`länkad mall hämtar du egenskapsvärdet med syntax som: .

När du hämtar en utdataegenskap från en länkad mall får egenskapsnamnet inte innehålla ett streck.

Följande exempel visar hur du refererar till en länkad mall och hämtar ett utdatavärde. Den länkade mallen returnerar ett enkelt meddelande.  Först den länkade mallen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

Huvudmallen distribuerar den länkade mallen och hämtar det returnerade värdet. Observera att den refererar till distributionsresursen efter namn och använder namnet på egenskapen som returneras av den länkade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

Precis som med andra resurstyper kan du ange beroenden mellan den länkade mallen och andra resurser. När andra resurser kräver ett utdatavärde från den länkade mallen kontrollerar du att den länkade mallen distribueras före dem. När den länkade mallen är beroende av andra resurser kontrollerar du också att andra resurser distribueras före den länkade mallen.

I följande exempel visas en mall som distribuerar en offentlig IP-adress och returnerar resurs-ID för Azure-resursen för den offentliga IP:en:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

Om du vill använda den offentliga IP-adressen från föregående mall när du distribuerar en `Microsoft.Resources/deployments` belastningsutjämnare länkar du till mallen och deklarerar ett beroende av resursen. Den offentliga IP-adressen på belastningsutjämnaren anges till utdatavärdet från den länkade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                // this is where the output value from linkedTemplate is used
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      // This is where the dependency is declared
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>Distributionshistorik

Resource Manager bearbetar varje mall som en separat distribution i distributionshistoriken. En huvudmall med tre länkade eller kapslade mallar visas i distributionshistoriken som:

![Distributionshistorik](./media/linked-templates/deployment-history.png)

Du kan använda dessa separata poster i historiken för att hämta utdatavärden efter distributionen. I följande mall skapas en offentlig IP-adress och IP-adressen matas ut:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Följande mall länkar till föregående mall. Det skapar tre offentliga IP-adresser.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Efter distributionen kan du hämta utdatavärdena med följande PowerShell-skript:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Eller Azure CLI-skript i ett Bash-skal:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Skydda en extern mall

Även om den länkade mallen måste vara externt tillgänglig behöver den inte vara allmänt tillgänglig för allmänheten. Du kan lägga till mallen i ett privat lagringskonto som endast är tillgängligt för lagringskontots ägare. Sedan skapar du en SAS-token (Shared Access Signature) för att aktivera åtkomst under distributionen. Du lägger till den SAS-token i URI för den länkade mallen. Även om token skickas in som en säker sträng loggas URI:n för den länkade mallen, inklusive SAS-token, i distributionsåtgärderna. Om du vill begränsa exponeringen anger du ett förfallodatum för token.

Parameterfilen kan också begränsas till åtkomst via en SAS-token.

För närvarande kan du inte länka till en mall i ett lagringskonto som finns bakom en [Azure Storage-brandvägg](../../storage/common/storage-network-security.md).

I följande exempel visas hur du skickar en SAS-token när du länkar till en mall:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

I PowerShell får du en token för behållaren och distribuerar mallarna med följande kommandon. Observera att **parametern containerSasToken** har definierats i mallen. Det är inte en parameter i budet **New-AzResourceGroupDeployment.**

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

För Azure CLI i ett Bash-skal får du en token för behållaren och distribuerar mallarna med följande kod:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar vanliga användningsområden för länkade mallar.

|Huvudmall  |Länkad mall |Beskrivning  |
|---------|---------| ---------|
|[Hej världen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Returnerar sträng från länkad mall. |
|[Belastningsutjämnare med offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Returnerar offentlig IP-adress från länkad mall och anger det värdet i belastningsutjämnaren. |
|[Flera IP-adresser](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Skapar flera offentliga IP-adresser i länkad mall.  |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa länkade Azure Resource Manager-mallar](template-tutorial-create-linked-templates.md).
* Mer information om hur du definierar distributionsordern för dina resurser finns [i Definiera beroenden i Azure Resource Manager-mallar](define-resource-dependency.md).
* Mer information om hur du definierar en resurs men skapar många instanser av den finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Steg om hur du konfigurerar en mall i ett lagringskonto och genererar en SAS-token finns [i Distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md) eller Distribuera resurser med Resource [Manager-mallar och Azure CLI](deploy-cli.md).

---
title: Länka mallar för distribution
description: 'Beskriver hur du använder länkade mallar i en Azure Resource Manager-mall för att skapa en modulbaserad mall-lösning. Visar hur du skickar parametrar värden, anger du en parameterfil och dynamiskt skapade URL: er.'
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 5d01aa8df6eba9b4e68937434dffae315a445cc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477452"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Med hjälp av länkade och kapslade mallar när du distribuerar Azure-resurser

Om du vill distribuera komplexa lösningar kan du dela upp mallen i flera relaterade mallar och sedan distribuera dem tillsammans via en huvud-mall. De relaterade mallarna kan vara separata filer eller mall-syntax som är inbäddad i huvud mal len. Den här artikeln använder den **länkade termen mall** för att referera till en separat mallfil som är länkad till från huvud mal len. Den använder termen **kapslad mall** för att referera till inbäddad mall-syntax i huvud mal len.

För små till medelstora lösningar är lättare att förstå och hantera en enda mall. Du kan se alla resurser och värden i en enda fil. Med länkade mallar i avancerade scenarier kan du dela upp lösningen i riktade komponenter. Du kan enkelt återanvända dessa mallar för andra scenarier.

En självstudiekurs finns i [självstudie: Skapa länkade Azure Resource Manager-mallar](template-tutorial-create-linked-templates.md).

> [!NOTE]
> För länkade eller kapslade mallar kan du bara använda [stegvis](deployment-modes.md) distributions läge.
>

## <a name="nested-template"></a>Kapslad mall

Om du vill kapsla en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I egenskapen **mall** anger du mallens syntax.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
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

I följande exempel distribueras ett lagrings konto via en kapslad mall.

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
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
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
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

### <a name="scope-for-expressions-in-nested-templates"></a>Omfång för uttryck i kapslade mallar

När du använder en kapslad mall kan du ange om mall uttryck ska utvärderas inom omfånget för den överordnade mallen eller den kapslade mallen. Omfånget avgör hur parametrar, variabler och funktioner som [resourceGroup](template-functions-resource.md#resourcegroup) och [prenumerationen](template-functions-resource.md#subscription) löses.

Du ställer in omfånget via `expressionEvaluationOptions`-egenskapen. Som standard är egenskapen `expressionEvaluationOptions` inställd på `outer`, vilket innebär att den använder den överordnade mallens omfattning. Ställ in värdet på `inner` till omfångs uttryck i den kapslade mallen.

```json
{
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
    ...
```

Följande mall visar hur mall uttryck matchas enligt omfånget. Den innehåller en variabel med namnet `exampleVar` som definieras i både den överordnade mallen och den kapslade mallen. Den returnerar variabelns värde.

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
            "type": "Microsoft.Resources/deployments",
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

Värdet för variabeln ändras baserat på omfånget. I följande tabell visas resultaten för båda omfattningarna.

| Omfång | Resultat |
| ----- | ------ |
| innersta | från kapslad mall |
| yttre (eller standard) | från överordnad mall |

I följande exempel distribueras en SQL-Server och en nyckel valvs hemlighet som används för lösen ordet hämtas. Omfånget anges till `inner` eftersom det dynamiskt skapar Key Vault-ID: t och skickar det som en parameter till den kapslade mallen.

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
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
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
                            "name": "[variables('sqlServerName')]",
                            "type": "Microsoft.Sql/servers",
                            "apiVersion": "2018-06-01-preview",
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
> När omfånget är inställt på `outer`kan du inte använda funktionen `reference` i avsnittet utdata i en kapslad mall för en resurs som du har distribuerat i den kapslade mallen. Om du vill returnera värdena för en distribuerad resurs i en kapslad mall kan du antingen använda ett inre område eller konvertera den kapslade mallen till en länkad mall.

## <a name="linked-template"></a>Länkad mall

Om du vill länka en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I egenskapen **templateLink** anger du URI för den mall som ska tas med. Följande exempel länkar till en mall som distribuerar ett nytt lagrings konto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

Du kan inte ange en lokal fil eller en fil som endast är tillgängliga i det lokala nätverket. Du kan endast ange ett URI-värde som innehåller antingen **http** eller **https**. Resource Manager måste kunna komma åt mallen. Ett alternativ är att placera din länkade mall i ett lagringskonto och använda URI: N för objektet.

Du inte behöver ange den `contentVersion` -egenskapen för mallen eller parametrar. Om du inte anger ett värde för innehållsversion, distribueras den aktuella versionen av mallen. Om du anger ett värde för innehållsversion måste måste den matcha versionen i länkade mallen. annars misslyckas distributionen med ett fel.

### <a name="parameters-for-linked-template"></a>Parametrar för länkad mall

Du kan ange parametrar för den länkade mallen antingen i en extern fil eller infogad. När du anger en extern parameter fil använder du egenskapen **parametersLink** :

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

Om du vill skicka parameter värden infogade använder **du parameter** egenskapen.

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

Du kan inte använda både infogade parametrar och en länk till en parameterfil. Distributionen misslyckas med ett fel när båda `parametersLink` och `parameters` har angetts.

## <a name="using-copy"></a>Använda kopiera

Om du vill skapa flera instanser av en resurs med en kapslad mall lägger du till kopiera-elementet på nivån i resursen **Microsoft. Resources/distributions** . Eller, om omfånget är Inner, kan du lägga till kopian i den kapslade mallen.

I följande exempel mall visas hur du använder kopiera med en kapslad mall.

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
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
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

## <a name="using-variables-to-link-templates"></a>Använda variabler för att länka mallar

I föregående exempel visade hårdkodade URL-värden för mall-länkar. Den här metoden fungerar för en enkel mall, men det fungerar inte bra när du arbetar med ett stort antal modulära mallar. I stället kan du skapa en statisk variabel som lagrar en bas-URL för den huvudsakliga mallen och dynamiskt skapa URL: er för de länkade mallarna från den grundläggande URL: en. Fördelen med den här metoden är att du enkelt kan flytta eller Förgrena mallen eftersom du behöver bara ändra den statiska variabeln i huvudsakliga mallen. Den huvudsakliga mallen skickar rätt URI: er i uppdelade mallen.

I följande exempel visas hur du skapar två URL: er för länkade mallar med hjälp av en bas-URL (**sharedTemplateUrl** och **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Du kan också använda [deployment()](template-functions-deployment.md#deployment) att hämta den grundläggande Webbadressen för den aktuella mallen och använda den för att hämta URL för andra mallar på samma plats. Den här metoden är användbar om ändringarna mall platsen eller om du vill undvika hårt kodning URL: er i mallfilen. Egenskapen templateLink returneras bara när du länkar till en fjärransluten mall med en URL. Om du använder en lokal mall är egenskapen inte tillgängligt.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Hämta värden från den länkade mallen

För att få utdatavärde från en länkad mall kan hämta egenskapens värde med syntax som: `"[reference('deploymentName').outputs.propertyName.value]"`.

När du hämtar en egenskap för utdata från en länkad mall, får inte egenskapsnamnet innehålla ett bindestreck.

I följande exempel visar hur du refererar till en länkad mall och hämta utdatavärde. Länkad mall returnerar ett enkelt meddelande.

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

Den huvudsakliga mallen distribuerar länkad mall och hämtar värdet som returneras. Observera att den refererar till resursen distribution efter namn och namnet på egenskapen som returneras av den länkade mallen används.

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

Du kan ange beroenden mellan länkade mallen och andra resurser som andra typer av resurser. När andra resurser kräver ett värde för utdata från den länkade mallen kontrollerar du att den länkade mallen har distribuerats före dem. Eller, när den länkade mallen är beroende av andra resurser, se till att andra resurser distribueras innan länkad mall.

I följande exempel visas en mall som distribuerar en offentlig IP-adress och returnerar resurs-ID:

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

Länka till mallen för att använda offentliga IP-adress från den föregående mallen när du distribuerar en belastningsutjämnare, och Lägg till ett beroende på resursen för distribution. Offentliga IP-adress i belastningsutjämnaren har tilldelats värdet från den länkade mallen.

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

Resource Manager bearbetar varje mall som en separat distribution i distributionshistoriken. En huvud-mall med tre länkade eller kapslade mallar visas i distributions historiken som:

![Distributionshistorik](./media/linked-templates/deployment-history.png)

Du kan använda de här separata posterna i historiken för att hämta utdatavärden efter distributionen. Följande mall skapas en offentlig IP-adress och matar ut IP-adress:

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

Följande mall länkar till föregående mall. Den skapar tre offentliga IP-adresser.

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

Efter distributionen kan hämta du utdatavärden med följande PowerShell-skript:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Eller Azure CLI-skript i ett Bash-gränssnitt:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Skydda en extern mall

Även om den länkade mallen måste vara externt tillgänglig, behöver det inte att vara allmänt tillgängligt för allmänheten. Du kan lägga till mallen för ett privat storage-konto som är tillgänglig för endast lagringskontoägaren. Sedan kan skapa du en token för delad åtkomst (signatur) för att möjliggöra åtkomst under distributionen. Du kan lägga till den SAS-token till URI: N för den länkade mallen. Även om token skickas som en säker sträng, loggas URI för länkade mallen, inklusive SAS-token i distributionsåtgärder. Ange en giltighetstid för token för att begränsa exponering.

Parameterfilen kan också vara begränsad till åtkomst via en SAS-token.

För närvarande kan du inte länka till en mall i ett lagrings konto som ligger bakom en [Azure Storage brand vägg](../../storage/common/storage-network-security.md).

I följande exempel visas hur du skickar en SAS-token när du länkar till en mall:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
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

I PowerShell kan du hämta en token för behållaren och distribuera mallar med följande kommandon. Observera att den **containerSasToken** parametern har definierats i mallen. Det är inte en parameter i kommandot **New-AzResourceGroupDeployment** .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

För Azure CLI i Bash-gränssnittet, hämta en token för behållaren och distribuera mallar med följande kod:

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
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga användningsområden för länkade mallar.

|Huvudmall  |Länkad mall |Beskrivning  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Returnerar en sträng från länkad mall. |
|[Belastningsutjämnare med offentliga IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Returnerar den offentliga IP-adress från länkad mall och ställer in värdet i belastningsutjämnaren. |
|[Flera IP-adresser](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Skapar flera offentliga IP-adresser i länkad mall.  |

## <a name="next-steps"></a>Nästa steg

* Om du vill gå igenom en självstudiekurs, se [självstudie: Skapa länkade Azure Resource Manager-mallar](template-tutorial-create-linked-templates.md).
* Läs om hur du definierar distributionsordning för dina resurser i [definiera beroenden i Azure Resource Manager-mallar](define-resource-dependency.md).
* Läs hur du definierar en resurs men skapa flera instanser av den i [och skapa flera instanser av resurser i Azure Resource Manager](create-multiple-instances.md).
* Stegvisa instruktioner för hur du konfigurerar en mall i ett lagringskonto och generera en SAS-token finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md) eller [distribuera resurser med Resource Manager-mallar och Azure CLI](deploy-cli.md).

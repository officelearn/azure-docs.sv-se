---
title: Länka mallar för Azure-distribution | Microsoft Docs
description: 'Beskriver hur du använder länkade mallar i en Azure Resource Manager-mall för att skapa en modulbaserad mall-lösning. Visar hur du skickar parametrar värden, anger du en parameterfil och dynamiskt skapade URL: er.'
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2019
ms.author: tomfitz
ms.openlocfilehash: d4ecccf8787e369b9a3270eab2d01a01ce7ae0c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58174315"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Med hjälp av länkade och kapslade mallar när du distribuerar Azure-resurser

För att distribuera din lösning, kan du använda samma mall eller en Huvudmall med många relaterade mappar. Relaterade mallen kan vara en separat fil som är länkad till från den huvudsakliga mallen eller en mall som är kapslade i den huvudsakliga mallen.

För små till medelstora lösningar är lättare att förstå och hantera en enda mall. Du kan se alla resurser och värden i en enda fil. För avancerade scenarier länkade mallar gör det möjligt att bryta ned lösningen till riktade komponenter och återanvända mallar.

När du använder länkade mallar kan skapa du en Huvudmall som tar emot parametervärdena under distributionen. Den huvudsakliga mallen innehåller alla länkade mallar och skickar värden till dessa mallar efter behov.

En självstudiekurs finns i [självstudie: Skapa länkade Azure Resource Manager-mallar](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> För länkade eller kapslade mallar, kan du bara använda [stegvis](deployment-modes.md) distributionsläget.
>

## <a name="link-or-nest-a-template"></a>Länka eller kapsla en mall

Om du vill länka till en annan mall, lägger du till en **distributioner** resurs till mallen för huvudsakliga.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Egenskaper som du anger för distribution av resursen varierar beroende på om du länkar till en extern mall eller kapsla en mall för infogad i mallen huvudsakliga.

### <a name="nested-template"></a>Kapslad mall

Om du vill kapsla mallen inom den huvudsakliga mallen använder den **mall** egenskap och ange mallens syntax.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-07-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Du kan inte använda parametrar eller variabler som definieras i den kapslade mallen för kapslade mallar. Du kan använda parametrar och variabler från den huvudsakliga mallen. I föregående exempel `[variables('storageName')]` hämtar ett värde från den huvudsakliga mallen, inte den kapslade mallen. Den här begränsningen gäller inte för externa mallar.
>
> För två resurser som definierats i en mall för kapslade och en resurs som beror på den andra, värdet för beroendet är helt enkelt namnet på den beroende resursen:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Du kan inte använda den `reference` funktion i avsnittet utdata i en kapslad mall. Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

Den kapslade mallen kräver den [samma egenskaper](resource-group-authoring-templates.md) som en standardmall.

### <a name="external-template-and-external-parameters"></a>Externa mall och externa parametrar

Om du vill länka till en extern mall och parameterfilen **templateLink** och **parametersLink**. När du länkar till en mall, måste Resource Manager-tjänsten kunna komma åt den. Du kan inte ange en lokal fil eller en fil som endast är tillgängliga i det lokala nätverket. Du kan endast ange ett URI-värde som innehåller antingen **http** eller **https**. Ett alternativ är att placera din länkade mall i ett lagringskonto och använda URI: N för objektet.

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

Du inte behöver ange den `contentVersion` -egenskapen för mallen eller parametrar. Om du inte anger ett värde för innehållsversion, distribueras den aktuella versionen av mallen. Om du anger ett värde för innehållsversion måste måste den matcha versionen i länkade mallen. annars misslyckas distributionen med ett fel.

### <a name="external-template-and-inline-parameters"></a>Externa mallen och infogade parametrar

Eller så kan du ange parametern-infogade. Du kan inte använda både infogade parametrar och en länk till en parameterfil. Distributionen misslyckas med ett fel när båda `parametersLink` och `parameters` har angetts.

Om du vill skicka ett värde från den huvudsakliga mallen till länkad mall, använda **parametrar**.

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

Du kan också använda [deployment()](resource-group-template-functions-deployment.md#deployment) att hämta den grundläggande Webbadressen för den aktuella mallen och använda den för att hämta URL för andra mallar på samma plats. Den här metoden är användbar om ändringarna mall platsen eller om du vill undvika hårt kodning URL: er i mallfilen. Egenskapen templateLink returneras bara när du länkar till en fjärransluten mall med en URL. Om du använder en lokal mall är egenskapen inte tillgängligt.

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

Du kan ange beroenden mellan länkade mallen och andra resurser som andra typer av resurser. När andra resurser kräver utdatavärde från länkade mallen, se därför till länkad mall har distribuerats före. Eller, när den länkade mallen är beroende av andra resurser, se till att andra resurser distribueras innan länkad mall.

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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Länkade och kapslade mallar i distributionshistoriken

Resource Manager bearbetar varje mall som en separat distribution i distributionshistoriken. Därför visas en huvudsakliga mall med tre länkade eller kapslade mallar i distributionshistoriken som:

![Distributionshistorik](./media/resource-group-linked-templates/deployment-history.png)

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

I PowerShell kan du hämta en token för behållaren och distribuera mallar med följande kommandon. Observera att den **containerSasToken** parametern har definierats i mallen. Det är inte en parameter i den **New AzResourceGroupDeployment** kommando.

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

* Om du vill gå igenom en självstudiekurs, se [självstudie: Skapa länkade Azure Resource Manager-mallar](./resource-manager-tutorial-create-linked-templates.md).
* Läs om hur du definierar distributionsordning för dina resurser i [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).
* Läs hur du definierar en resurs men skapa flera instanser av den i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Stegvisa instruktioner för hur du konfigurerar en mall i ett lagringskonto och generera en SAS-token finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md) eller [distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md).

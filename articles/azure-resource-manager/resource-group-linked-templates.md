---
title: Länka mallar för Azure-distribution | Microsoft Docs
description: 'Beskriver hur du kan använda länkade mallar i en Azure Resource Manager-mall för att skapa en mall för modulär lösning. Visar hur skicka parametrar värden genom att ange en parameterfil och dynamiskt skapade URL: er.'
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2018
ms.author: tomfitz
ms.openlocfilehash: e0c0c3f51b455983dbe3f937917ed090e51d0005
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Använder länkade och kapslade mallar när du distribuerar Azure-resurser

Om du vill distribuera lösningar kan du använda en mall eller en Huvudmall med flera relaterade mallar. Relaterade mallen kan vara en separat fil som är länkad till från den huvudsakliga mallen, eller en mall som är kapslad i den huvudsakliga mallen.

För små till medelstora lösningar är en mall lättare att förstå och hantera. Du kan se alla resurser och värden i en enda fil. För avancerade scenarier länkade mallar kan du bryta ned lösningen till riktade komponenter och återanvända mallar.

När använder länkad mall, skapar du en Huvudmall som tar emot parametervärden under distributionen. Den huvudsakliga mallen innehåller alla länkade mallar och skickar värden till dessa mallar efter behov.

## <a name="link-or-nest-a-template"></a>Länka eller kapsla en mall

Om du vill länka till en annan mall, lägger du till en **distributioner** resurs den huvudsakliga mallen.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

De egenskaper som du anger för resursen distribution variera beroende på om du länkar till en extern mall eller kapsla en infogad mall i huvudsakliga mallen.

### <a name="nested-template"></a>Kapslad mall

Om du vill kapsla mallen i huvudsakliga mallen använder den **mallen** egenskap och ange mallens syntax.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
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
> Du kan inte använda parametrar och variabler som definieras i den kapslade mallen för kapslade mallar. Du kan använda parametrar och variabler från den huvudsakliga mallen. I föregående exempel `[variables('storageName')]` hämtar ett värde från den huvudsakliga mallen, inte den kapslade mallen. Den här begränsningen gäller inte för externa mallar.
>
> Du kan inte använda den `reference` funktionen i avsnittet utdata i en kapslad mall. Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

### <a name="external-template-and-external-parameters"></a>Externa mall och externa parametrar

Om du vill länka till en extern mall och parameterfilen **templateLink** och **parametersLink**. När du länkar till en mall för måste Resource Manager-tjänsten kunna komma åt den. Du kan inte ange en lokal fil eller en fil som endast är tillgängliga i det lokala nätverket. Du kan endast ange ett URI-värde som innehåller antingen **http** eller **https**. Ett alternativ är att placera den länka mallen i ett lagringskonto och Använd URI för objektet.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

### <a name="external-template-and-inline-parameters"></a>Externa mallen och infogade parametrar

Eller så kan du ange parametern infogad. Om du vill skicka ett värde från den huvudsakliga mallen till den länkade mallen, Använd **parametrar**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
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

I föregående exempel visade hårdkodade URL-värden för mallen länkar. Den här metoden kan fungera för en enkel mall men det fungerar inte bra när du arbetar med en stor mängd modulära mallar. I stället kan du skapa en statisk variabel som lagrar en bas-URL för den huvudsakliga mallen och dynamiskt skapa URL: er för de länkade mallarna från den grundläggande Webbadressen. Fördelen med den här metoden är att du enkelt kan flytta eller duplicera mallen eftersom du behöver ändra den statiska variabeln i huvudsakliga mallen. Den huvudsakliga mallen skickar rätt URI: er i hela uppdelade mallen.

I följande exempel visas hur du skapar två webbadresserna för länkade mallar med hjälp av en bas-URL (**sharedTemplateUrl** och **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Du kan också använda [deployment()](resource-group-template-functions-deployment.md#deployment) att hämta en bas-URL för den aktuella mallen och använda den för att hämta URL för andra mallar på samma plats. Den här metoden är användbart om din mallplats ändras (kanske på grund av versioning) eller om du vill undvika hård kodning URL: er i mallfilen. Egenskapen templateLink returneras bara när du länkar till en fjärransluten mall med en URL. Om du använder en mall för lokala är egenskapen inte tillgänglig.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Hämta värden från länkade mall

Hämta egenskapens värde med syntax som om du vill ha ett utdatavärde från en länkad mall: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Följande exempel visar hur du refererar till en länkad mall och hämta ett utdatavärde. Den länkade mallen returnerar ett enkelt meddelande.

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

Den huvudsakliga mallen distribuerar mallen länkade och hämtar värdet som returneras. Observera att den refererar till resursen distribution av namn och namnet på egenskapen som returnerades av länkade mallen används.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
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

Du kan ange beroenden mellan länkade mallen och andra resurser som andra typer av resurser. Därför när andra resurser kräver ett utdatavärde från den länka mallen, kontrollera länkade mallen distribueras före datakällorna. Eller när den länkade mallen är beroende av andra resurser, se till andra resurser har distribuerats innan länkade mallen.

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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

Om du vill använda offentliga IP-adress från den föregående mallen när du distribuerar en belastningsutjämnare, länkar till mallen och lägga till ett beroende på resursen distribution. Den offentliga IP-adressen på belastningsutjämnaren anges till värdet från länkade mallen.

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
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
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
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

Hanteraren för filserverresurser bearbetar varje mall som en separat distribution i distributionshistoriken. Därför visas en Huvudmall med tre länkade eller kapslade mallar i distributionshistoriken som:

![Distributionshistorik](./media/resource-group-linked-templates/deployment-history.png)

Du kan använda dessa separat poster i historiken för att hämta utdatavärden efter distributionen. Följande mall skapas en offentlig IP-adress och matar ut IP-adressen:

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
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
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

Följande mall länkar till den föregående mallen. Den skapar tre offentliga IP-adresser.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Efter distributionen kan hämta du utdatavärden med följande PowerShell-skript:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Eller Azure CLI-skript:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Skydda en extern mall

Även om mallen länkade måste finnas externt, behöver det inte vara allmänt tillgänglig för allmänheten. Du kan lägga till mallen till en privat storage-konto som är tillgänglig för endast lagringskontoägaren. Sedan kan skapa du en delad åtkomsttoken signatur (SAS) för att möjliggöra åtkomst under distributionen. Du lägger till den SAS-token URI för den länkade mallen. Även om token som skickas som en säker sträng, loggas URI för länkade mallen, inklusive SAS-token i distributionsåtgärder. Ange en giltighetstid för token för att begränsa exponeringen.

Parameterfilen kan också vara begränsad åtkomst genom en SAS-token.

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
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
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

I PowerShell, hämta en token för behållaren och distribuera mallar med följande kommandon. Observera att den **containerSasToken** parametern har definierats i mallen. Det är inte en parameter i den **ny AzureRmResourceGroupDeployment** kommando.

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

I Azure CLI, hämta en token för behållaren och distribuera mallar med följande kod:

```azurecli
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

## <a name="example-templates"></a>Exempel mallar

I följande exempel visas vanliga användningsområden för länkade mallar.

|Huvudmall  |Länkad mall |Beskrivning  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Returnerar en sträng från länkade mallen. |
|[Belastningsutjämnare med den offentliga IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Returnerar den offentliga IP-adress från länkade mallen och anger värdet i belastningsutjämnaren. |
|[Flera IP-adresser](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Skapar flera offentliga IP-adresser i länkade mallen.  |

## <a name="next-steps"></a>Nästa steg

* Läs om hur du definierar distributionsordningen för dina resurser i [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).
* Information om hur du definierar en resurs men skapa många instanser av det, se [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Stegvisa instruktioner för hur du konfigurerar en mall i ett lagringskonto och generera en SAS-token finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md) eller [distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md).
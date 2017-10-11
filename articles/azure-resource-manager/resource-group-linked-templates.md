---
title: "Länka mallar för Azure-distribution | Microsoft Docs"
description: "Beskriver hur du kan använda länkade mallar i en Azure Resource Manager-mall för att skapa en mall för modulär lösning. Visar hur skicka parametrar värden genom att ange en parameterfil och dynamiskt skapade URL: er."
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
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Använda länkade mallar när du distribuerar Azure-resurser
Från inom en Azure Resource Manager-mall, kan du länka till en annan mall, vilket gör det möjligt att dela upp din distribution till en uppsättning riktade, mallar för specifika ändamål. Precis som med decomposing ett program i flera klasser som koden har uppdelning fördelar vad gäller testning, återanvändning och läsbarhet.  

Du kan skicka parametrar från en Huvudmall till en länkad mall och parametrarna direkt kan mappa till parametrar eller variabler som exponeras av anropa mallen. Den länkade mallen kan också skicka en variabel utdata tillbaka till källmallen, aktiverar du en dubbelriktad datautbyte mellan mallar.

## <a name="linking-to-a-template"></a>Länka till en mall
Du kan skapa en länk mellan två mallar genom att lägga till en distributionsresurs i mallen pekar på den länkade mallen. Du ställer in den **templateLink** egenskapen till URI: N för den länkade mallen. Du kan ange parametervärden för mallen länkade direkt i din mall eller en parameterfil. I följande exempel används den **parametrar** att ange ett parametervärde direkt.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Du kan ange beroenden mellan länkade mallen och andra resurser som andra typer av resurser. Därför när andra resurser kräver ett utdatavärde från den länka mallen kan kan du kontrollera länkade mallen distribueras före datakällorna. Eller när länkade mallen är beroende av andra resurser, kan du se till andra resurser har distribuerats innan länkade mallen. Du kan hämta ett värde från en länkad mall med följande syntax:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

Resource Manager-tjänsten måste kunna få åtkomst till den länkade mallen. Du kan inte ange en lokal fil eller en fil som endast är tillgängliga i det lokala nätverket för den länka mallen. Du kan endast ange ett URI-värde som innehåller antingen **http** eller **https**. Ett alternativ är att placera den länka mallen i ett lagringskonto och Använd URI för objektet, så som visas i följande exempel:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Även om mallen länkade måste finnas externt, behöver det inte vara allmänt tillgänglig för allmänheten. Du kan lägga till mallen till en privat storage-konto som är tillgänglig för endast lagringskontoägaren. Sedan kan skapa du en delad åtkomsttoken signatur (SAS) för att möjliggöra åtkomst under distributionen. Du lägger till den SAS-token URI för den länkade mallen. Stegvisa instruktioner för hur du konfigurerar en mall i ett lagringskonto och generera en SAS-token finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md) eller [distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md). 

I följande exempel visas en överordnad mapp som länkar till en annan mall. Den länkade mallen används med en SAS-token som skickas som en parameter.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Även om token som skickas som en säker sträng, loggas URI för länkade mallen, inklusive SAS-token i distributionsåtgärder. Ange en giltighetstid för token för att begränsa exponeringen.

Resource Manager hanterar varje länkade mall som en separat distribution. I distributionshistoriken för resursgruppen ser du separata distributioner för den överordnade och de kapslade mallar.

![distributionshistorik](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Länka till en parameterfil
I nästa exempel används den **parametersLink** egenskapen att länka till en parameterfil.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

URI-värdet för den länka parameterfilen får inte vara en lokal fil och måste innehålla antingen **http** eller **https**. Parameterfilen kan också vara begränsad åtkomst genom en SAS-token.

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

Du kan också använda [deployment()](resource-group-template-functions-deployment.md#deployment) att hämta en bas-URL för den aktuella mallen och använda den för att hämta URL för andra mallar på samma plats. Den här metoden är användbart om din mallplats ändras (kanske på grund av versioning) eller om du vill undvika hård kodning URL: er i mallfilen. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Fullständigt exempel
Följande exempel mallar visar en förenklad placering av länkade mallar att illustrera flera av begrepp i den här artikeln. Det förutsätts att mallarna har lagts till behållaren i ett lagringskonto med offentlig åtkomst är inaktiverat. Den länkade mallen skickar ett värde tillbaka till den huvudsakliga mallen i den **matar ut** avsnitt.

Den **parent.json** filen består av:

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
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

Den **helloworld.json** filen består av:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

I PowerShell, hämta en token för behållaren och distribuera mallar med:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

I Azure CLI 2.0, hämta en token för behållaren och distribuera mallar med följande kod:

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

## <a name="next-steps"></a>Nästa steg
* Läs om hur du definierar distributionsordningen för dina resurser i [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md)
* Information om hur du definierar en resurs men skapa många instanser av det, se [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md)


---
title: Skapa resurs leverantör
description: Beskriver hur du skapar en resurs leverantör och distribuerar dess anpassade resurs typer.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75649871"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Snabb start: skapa en anpassad Provider och distribuera anpassade resurser

I den här snabb starten skapar du en egen resurs leverantör och distribuerar anpassade resurs typer för den resurs leverantören. Mer information om anpassade providers finns i [Översikt över Azure Custom providers Preview](overview.md).

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här snabb starten måste du anropa REST-åtgärder. Det finns [olika sätt att skicka rest-begäranden](/rest/api/azure/). Om du inte redan har ett verktyg för REST-åtgärder installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett kommando rads verktyg med öppen källkod som gör det enklare att anropa Azure Resource Manager API.

## <a name="deploy-custom-provider"></a>Distribuera anpassad Provider

Om du vill konfigurera den anpassade providern distribuerar du en [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) till din Azure-prenumeration.

När du har distribuerat mallen har prenumerationen följande resurser:

* Funktionsapp med åtgärder för resurserna och åtgärderna.
* Lagrings konto för att lagra användare som skapats via den anpassade providern.
* Anpassad provider som definierar anpassade resurs typer och åtgärder. Den använder Function-programmets slut punkt för att skicka begär Anden.
* Anpassad resurs från den anpassade providern.

Använd följande för att distribuera den anpassade providern med PowerShell:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Du kan också distribuera lösningen med följande knapp:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Visa anpassad Provider och resurs

Den anpassade providern är en dold resurs typ i portalen. Om du vill bekräfta att resurs leverantören har distribuerats navigerar du till resurs gruppen. Välj alternativet för att **Visa dolda typer**.

![Visa dolda resurs typer](./media/create-custom-provider/show-hidden.png)

Om du vill se den anpassade resurs typ som du har distribuerat använder du åtgärden Hämta i resurs typen.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Med ARMClient använder du:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Du får svaret:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Anrops åtgärd

Den anpassade providern har också en åtgärd med namnet **ping**. Den kod som bearbetar begäran implementeras i Function-appen. Ping-åtgärden svarar med en hälsning.

Om du vill skicka en ping-begäran använder du POST-åtgärden på den anpassade providern.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Med ARMClient använder du:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Du får svaret:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Skapa resurs typ

Om du vill skapa en anpassad resurs typ kan du distribuera resursen i en mall. Den här metoden visas i mallen som du distribuerade i den här snabb starten. Du kan också skicka en skicka-begäran för resurs typen.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Med ARMClient använder du:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Du får svaret:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Nästa steg

En introduktion till anpassade providers finns i [Översikt över Azure Custom providers Preview](overview.md).

---
title: Skapa resursprovidern med förhandsversionen av Azure anpassade Providers
description: Beskriver hur du skapar en resursprovider och distribuera dess egna resurstyper.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: e4196cf59537be5194ceb510a1b7b066c97de19a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410225"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Självstudier: Skapa anpassad provider och distribuera anpassade resurser

I de här självstudierna skapar du en egen resource provider och distribuera anpassade resurstyper för den resursprovidern. Läs mer om anpassade providers, [anpassade Providers förhandsversion av Azure-översikt](custom-providers-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här självstudien måste du anropa REST-åtgärder. Det finns [flera olika sätt att skicka REST-begäranden](/rest/api/azure/). Om du inte redan har ett verktyg för REST-åtgärder, installera [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Distribuera anpassad provider

Om du vill konfigurera den anpassade providern distribuera en [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) på Azure-prenumerationen.

När du har distribuerat mallen har din prenumeration följande resurser:

* Funktionsapp med åtgärder för resurser och åtgärder.
* Storage-konto för att lagra användare som har skapats via den anpassade providern.
* Anpassad Provider som definierar de anpassade resurstyperna och åtgärderna. Funktionen app slutpunkten används för att skicka begäran.
* Anpassad resurs från den anpassade providern.

Om du vill distribuera den anpassade providern med PowerShell använder du:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Alternativt kan du distribuera lösningen med knappen följande:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Visa anpassad provider och resurs

Den anpassade providern är en dold resurstyp i portalen. Kontrollera att resursprovidern har distribuerats genom att navigera till resursgruppen. Välj alternativet för att **Visa dolda typer**.

![Visa dolda resurstyper](./media/create-custom-providers/show-hidden.png)

Använd GET-åtgärden för din resurstyp av om du vill se den anpassa resurstyp som du har distribuerat.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Med ARMClient, använder du:

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

## <a name="call-action"></a>Anropa åtgärden

Den anpassade providern har också en åtgärd med namnet **ping**. Den kod som bearbetar begäran har implementerats i funktionsappen. Ping-åtgärd svarar den med en hälsning.

Använd POST-åtgärden på den anpassade providern för att skicka en ping-begäran.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Med ARMClient, använder du:

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

## <a name="create-resource-type"></a>Skapa resurstyp

Du kan distribuera resursen i en mall för att skapa anpassade resurstypen. Den här metoden illustreras i mallen som du distribuerade i den här självstudien. Du kan också skicka en PUT-begäran för resurstypen.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Med ARMClient, använder du:

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

En introduktion till anpassade providrar finns i [anpassade Providers förhandsversion av Azure-översikt](custom-providers-overview.md).

---
title: Skapa resursprovider
description: Beskriver hur du skapar en resursprovider och distribuerar dess anpassade resurstyper.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649871"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Snabbstart: Skapa anpassad provider och distribuera anpassade resurser

I den här snabbstarten skapar du en egen resursleverantör och distribuerar anpassade resurstyper för den resursleverantören. Mer information om anpassade leverantörer finns i [översikt över förhandsversionen av Azure Custom Providers](overview.md).

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här snabbstarten måste du anropa REST-åtgärder. Det finns [olika sätt att skicka REST-begäranden](/rest/api/azure/). Om du inte redan har ett verktyg för REST-åtgärder installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett kommandoradsverktyg med öppen källkod som förenklar anstiftan till Azure Resource Manager-API:et.

## <a name="deploy-custom-provider"></a>Distribuera anpassad provider

Om du vill konfigurera den anpassade providern distribuerar du en [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) till din Azure-prenumeration.

När du har distribuerat mallen har prenumerationen följande resurser:

* Funktionsapp med åtgärder för resurser och åtgärder.
* Lagringskonto för lagring av användare som skapas via den anpassade leverantören.
* Anpassad provider som definierar anpassade resurstyper och åtgärder. Den använder slutpunkten för funktionsappen för att skicka begäranden.
* Anpassad resurs från den anpassade leverantören.

Om du vill distribuera den anpassade providern med PowerShell använder du:

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

## <a name="view-custom-provider-and-resource"></a>Visa anpassad leverantör och resurs

I portalen är den anpassade providern en dold resurstyp. Om du vill bekräfta att resursprovidern har distribuerats navigerar du till resursgruppen. Välj alternativet **Visa dolda typer**.

![Visa dolda resurstyper](./media/create-custom-provider/show-hidden.png)

Om du vill visa den anpassade resurstyp som du har distribuerat använder du åtgärden GET på resurstypen.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Använd med ARMClient:

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

## <a name="call-action"></a>Ring åtgärd

Din anpassade leverantör har också en åtgärd som heter **ping**. Koden som bearbetar begäran implementeras i funktionsappen. Ping-åtgärden svarar med en hälsning.

Om du vill skicka en ping-begäran använder du POST-åtgärden på din anpassade leverantör.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Använd med ARMClient:

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

Om du vill skapa den anpassade resurstypen kan du distribuera resursen i en mall. Den här metoden visas i mallen som du distribuerade i den här snabbstarten. Du kan också skicka en PUT-begäran för resurstypen.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Använd med ARMClient:

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

En introduktion till anpassade leverantörer finns i [översikt över förhandsversionen av Azure Custom Providers](overview.md).

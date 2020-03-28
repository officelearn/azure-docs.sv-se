---
title: Självstudiekurs - lägg till variabel i mallen
description: Lägg till variabler i Azure Resource Manager-mallen för att förenkla syntaxen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7fa6c36b604d0e96b2192ceb6c5585afcade080b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371720"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Självstudiekurs: Lägga till variabler i ARM-mallen

I den här självstudien får du lära dig hur du lägger till en variabel i din AZURE Resource Manager-mall (ARM). Variabler förenklar mallarna genom att du kan skriva ett uttryck en gång och återanvända det i hela mallen. Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om funktioner,](template-tutorial-add-functions.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Parametern för lagringskontonamnet är svår att använda eftersom du måste ange ett unikt namn. Om du har slutfört tidigare tutorials i den här serien, är du förmodligen trött på att gissa ett unikt namn. Du löser problemet genom att lägga till en variabel som skapar ett unikt namn för lagringskontot.

## <a name="use-variable"></a>Använd variabel

I följande exempel markeras ändringarna för att lägga till en variabel i mallen som skapar ett unikt lagringskontonamn. Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Observera att den innehåller en variabel med namnet **uniqueStorageName**. Den här variabeln använder fyra funktioner för att konstruera ett strängvärde.

Du är redan bekant med [parametrarna](template-functions-deployment.md#parameters) funktionen, så vi kommer inte att undersöka det.

Du är också bekant med [resursgruppsfunktionen.](template-functions-resource.md#resourcegroup) I det här fallet får du **id-egenskapen** i stället för platsegenskapen, som visas i föregående självstudie. **location** Egenskapen **id** returnerar resursgruppens fullständiga identifierare, inklusive prenumerations-ID och resursgruppsnamn.

Funktionen [uniqueString](template-functions-string.md#uniquestring) skapar ett hash-värde med 13 tecken. Det returnerade värdet bestäms av de parametrar du skickar in. För den här självstudien använder du resursgrupp-ID:et som indata för hash-värdet. Det innebär att du kan distribuera den här mallen till olika resursgrupper och få ett annat unikt strängvärde. Du får dock samma värde om du distribuerar till samma resursgrupp.

[Concat-funktionen](template-functions-string.md#concat) tar värden och kombinerar dem. För den här variabeln tar den strängen från parametern och strängen från funktionen uniqueString och kombinerar dem till en sträng.

Med parametern **storagePrefix** kan du skicka in ett prefix som hjälper dig att identifiera lagringskonton. Du kan skapa en egen namngivningskonvention som gör det enklare att identifiera lagringskonton efter distribution från en lång lista med resurser.

Observera slutligen att lagringsnamnet nu är inställt på variabeln i stället för en parameter.

## <a name="deploy-template"></a>Distribuera mallen

Nu distribuerar vi mallen. Det är enklare att distribuera den här mallen än de tidigare mallarna eftersom du bara anger prefixet för lagringsnamnet.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Du ser att en lagringskontoresurs har distribuerats. Namnet på lagringskontot är **lagra** plus en sträng med slumpmässiga tecken.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en variabel som skapar ett unikt namn för ett lagringskonto. I nästa självstudiekurs returnerar du ett värde från det distribuerade lagringskontot.

> [!div class="nextstepaction"]
> [Lägga till utdata](template-tutorial-add-outputs.md)

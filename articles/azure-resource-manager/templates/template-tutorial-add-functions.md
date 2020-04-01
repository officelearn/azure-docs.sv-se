---
title: Självstudiekurs - lägg till mallfunktioner
description: Lägg till mallfunktioner i Azure Resource Manager-mallen för att konstruera värden.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e4984b286bf031b66272919a487d09a90f972ce0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410966"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Självstudiekurs: Lägga till mallfunktioner i ARM-mallen

I den här självstudien får du lära dig hur du lägger till [mallfunktioner](template-functions.md) i din AZURE Resource Manager-mall (ARM). Du använder funktioner för att dynamiskt konstruera värden. Utöver dessa mallfunktioner som tillhandahålls av systemet kan du också skapa [användardefinierade funktioner](./template-user-defined-functions.md). Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om parametrar,](template-tutorial-add-parameters.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Lagringskontots placering är hårdkodad till **östra USA**. Du kan dock behöva distribuera lagringskontot till andra regioner. Du står återigen inför ett problem med mallen som saknar flexibilitet. Du kan lägga till en parameter för plats, men det skulle vara bra om dess standardvärde var mer meningsfullt än bara ett hårdkodat värde.

## <a name="use-function"></a>Använd funktion

Om du har slutfört föregående självstudiekurs i den här serien har du redan använt en funktion. När du lade till **"[parameters('storageName')]"** använde du [parameterfunktionen.](template-functions-deployment.md#parameters) Hakparenteserna anger att syntaxen inom parenteserna är ett [malluttryck](template-expressions.md). Resurshanteraren matchar syntaxen i stället för att behandla den som ett litteralt värde.

Funktioner ger flexibilitet i mallen genom att dynamiskt hämta värden under distributionen. I den här självstudien använder du en funktion för att hämta platsen för den resursgrupp som du använder för distribution.

I följande exempel markeras ändringarna för att lägga till en parameter som kallas **plats**.  Parameterstandardvärdet anropar [funktionen resourceGroup.](template-functions-resource.md#resourcegroup) Den här funktionen returnerar ett objekt med information om resursgruppen som används för distribution. En av egenskaperna för objektet är en platsegenskap. När du använder standardvärdet har lagringskontoplatsen samma plats som resursgruppen. Resurserna i en resursgrupp behöver inte dela samma plats. Du kan också ange en annan plats när det behövs.

Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Distribuera mallen

I de tidigare självstudierna skapade du ett lagringskonto i östra USA, men resursgruppen skapades i centrala USA. För den här självstudien skapas ditt lagringskonto i samma region som resursgruppen. Använd standardvärdet för plats, så att du inte behöver ange det parametervärdet. Du måste ange ett nytt namn för lagringskontot eftersom du skapar ett lagringskonto på en annan plats. Använd till exempel **store2** som prefix i stället för **store1**.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Du ser att en lagringskontoresurs har distribuerats och har samma plats som resursgruppen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en funktion när du definierade standardvärdet för en parameter. I den här självstudieserien fortsätter du att använda funktioner. I slutet av serien ska du lägga till funktioner i varje avsnitt i mallen.

> [!div class="nextstepaction"]
> [Lägga till variabler](template-tutorial-add-variables.md)

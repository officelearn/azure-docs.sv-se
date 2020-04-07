---
title: Självstudiekurs - lägga till utdata i mallen
description: Lägg till utdata i azure Resource Manager-mallen för att förenkla syntaxen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2ee1a2c7037bde68b7858b57a03c78bd2016ff1c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743547"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Självstudiekurs: Lägga till utdata i arm-mallen

I den här självstudien får du lära dig hur du returnerar ett värde från din AZURE Resource Manager-mall (ARM). Du använder utdata när du behöver ett värde från en distribuerad resurs. Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om variabler,](template-tutorial-add-variables.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Den distribuerar ett lagringskonto, men returnerar ingen information om lagringskontot. Du kan behöva samla in egenskaper från en ny resurs så att de är tillgängliga senare som referens.

## <a name="add-outputs"></a>Lägga till utdata

Du kan använda utdata för att returnera värden från mallen. Det kan till exempel vara bra att hämta slutpunkterna för ditt nya lagringskonto.

I följande exempel markeras ändringen i mallen för att lägga till ett utdatavärde. Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Det finns några viktiga punkter att notera om utdatavärdet du har lagt till.

Typen av returnerat värde är inställd på **objekt,** vilket innebär att det returnerar ett JSON-objekt.

Den använder [referensfunktionen](template-functions-resource.md#reference) för att hämta körningstillståndet för lagringskontot. Om du vill hämta körningstillståndet för en resurs skickar du in en resurs namn eller ID. I det här fallet använder du samma variabel som du använde för att skapa namnet på lagringskontot.

Slutligen returneras egenskapen **primaryEndpoints** från lagringskontot

## <a name="deploy-template"></a>Distribuera mallen

Du är redo att distribuera mallen och titta på det returnerade värdet.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

I utdata för distributionskommandot visas bara ett objekt som liknar följande exempel om utdata är i JSON-format:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="review-your-work"></a>Granska ditt arbete

Du har gjort mycket i de senaste sex tutorials. Låt oss ta en stund att se över vad du har gjort. Du har skapat en mall med parametrar som är lätta att tillhandahålla. Mallen kan återanvändas i olika miljöer eftersom den möjliggör anpassning och dynamiskt skapar nödvändiga värden. Den returnerar också information om lagringskontot som du kan använda i skriptet.

Nu ska vi titta på resursgruppen och distributionshistoriken.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Beroende på vilka steg du gjorde bör du ha minst ett och kanske flera lagringskonton i resursgruppen.
1. Du bör också ha flera lyckade distributioner listade i historiken. Välj den länken.

   ![Välj distributioner](./media/template-tutorial-add-outputs/select-deployments.png)

1. Du ser alla dina distributioner i historiken. Välj distributionen som kallas **addoutputs**.

   ![Visa distributionshistorik](./media/template-tutorial-add-outputs/show-history.png)

1. Du kan granska indata.

   ![Visa indata](./media/template-tutorial-add-outputs/show-inputs.png)

1. Du kan granska utdata.

   ![Visa utdata](./media/template-tutorial-add-outputs/show-outputs.png)

1. Du kan granska mallen.

   ![Visa mall](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till ett returvärde i mallen. I nästa självstudiekurs får du lära dig hur du exporterar en mall och använder delar av den exporterade mallen i mallen.

> [!div class="nextstepaction"]
> [Använda exporterad mall](template-tutorial-export-template.md)

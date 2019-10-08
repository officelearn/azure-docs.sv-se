---
title: Självstudie – Lägg till utdata i Azure Resource Manager mall
description: Lägg till utdata i Azure Resource Manager-mallen för att förenkla syntaxen.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 458833372d5bd03a04e4df7d6e915cddb4bb05c7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001534"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Självstudier: Lägga till utdata i Resource Manager-mallen

I den här självstudien får du lära dig hur du returnerar ett värde från mallen. Du använder utdata när du behöver ett värde från en distribuerad resurs. Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om variabler](template-tutorial-add-variables.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Granska mallen

I slutet av den föregående själv studie kursen hade mallen följande JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

Den distribuerar ett lagrings konto, men returnerar inte någon information om lagrings kontot. Du kan behöva avbilda egenskaper från en ny resurs så att de är tillgängliga senare för referens.

## <a name="add-outputs"></a>Lägg till utdata

Du kan använda utdata för att returnera värden från mallen. Det kan till exempel vara användbart att få slut punkter för ditt nya lagrings konto.

I följande exempel visas ändringarna i mallen för att lägga till ett utmatnings värde. Kopiera hela filen och ersätt din mall med dess innehåll.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

Det finns några viktiga saker att notera om det utdata som du har lagt till.

Det returnerade värdets typ är inställt på **objekt**, vilket innebär att det returnerar ett JSON-objekt.

Funktionen [Reference](resource-group-template-functions-resource.md#reference) används för att hämta lagrings kontots körnings tillstånd. Om du vill hämta körnings status för en resurs skickar du namnet eller ID: t för en resurs. I det här fallet använder du samma variabel som du använde för att skapa namnet på lagrings kontot.

Slutligen returnerar den egenskapen **blobar** från lagrings kontot

## <a name="deploy-the-template"></a>Distribuera mallen

Du är redo att distribuera mallen och titta på det returnerade värdet.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

I utdata för kommandot distribution ser du ett objekt som liknar:

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

## <a name="review-your-work"></a>Granska ditt arbete

Du har gjort en stor del av de senaste sex självstudierna. Vi tar en stund att granska vad du har gjort. Du har skapat en mall med parametrar som är enkla att tillhandahålla. Mallen kan återanvändas i olika miljöer eftersom den tillåter anpassning och dynamiskt skapar nödvändiga värden. Den returnerar också information om det lagrings konto som du kan använda i skriptet.

Nu ska vi titta på resurs gruppen och distributions historiken.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **resurs grupper**på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Beroende på vilka steg du gjorde bör du ha minst ett och kanske flera lagrings konton i resurs gruppen.
1. Du bör också ha flera lyckade distributioner som listas i historiken. Välj länken.

   ![Välj distributioner](./media/template-tutorial-add-outputs/select-deployments.png)

1. Du ser alla dina distributioner i historiken. Välj distributionen som kallas **addoutputs**.

   ![Visa distributions historik](./media/template-tutorial-add-outputs/show-history.png)

1. Du kan granska indata.

   ![Visa indata](./media/template-tutorial-add-outputs/show-inputs.png)

1. Du kan granska utdata.

   ![Visa utdata](./media/template-tutorial-add-outputs/show-outputs.png)

1. Du kan granska mallen.

   ![Visa mall](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till ett retur värde i mallen. I nästa självstudie får du lära dig hur du exporterar en mall och använder delar av den exporterade mallen i mallen.

> [!div class="nextstepaction"]
> [Använd exporterad mall](template-tutorial-export-template.md)

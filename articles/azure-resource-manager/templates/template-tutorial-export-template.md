---
title: Självstudiekurs - Exportera mall från Azure-portalen
description: Lär dig hur du använder en exporterad mall för att slutföra mallutvecklingen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f95efbaedc2718c968062c47427ab7765756bde7
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408557"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Självstudiekurs: Använda exporterad mall från Azure-portalen

I den här självstudieserien har du skapat en mall för att distribuera ett Azure-lagringskonto. I de följande två självstudierna lägger du till en *appserviceplan* och en *webbplats*. I stället för att skapa mallar från grunden lär du dig hur du exporterar mallar från Azure-portalen och hur du använder exempelmallar från [Azure Quickstart-mallarna](https://azure.microsoft.com/resources/templates/). Du kan anpassa mallarna för din användning. Den här självstudien fokuserar på att exportera mallar och anpassa resultatet för mallen. Det tar cirka **14 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om utdata,](template-tutorial-add-outputs.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Den här mallen fungerar bra för distribution av lagringskonton, men du kanske vill lägga till mer resurser i den. Du kan exportera en mall från en befintlig resurs för att snabbt få JSON för den resursen.

## <a name="create-app-service-plan"></a>Skapa apptjänstplan

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs**.
1. I **Sök på Marketplace**anger du App **Service-planen**och väljer sedan **App Service-plan**.  Välj inte **App Service-plan (klassisk)**
1. Välj **Skapa**.
1. Skriv:

    - **Prenumeration:** välj din Azure-prenumeration.
    - **Resursgrupp:** Välj **Skapa nytt** och ange sedan ett namn. Ange ett annat resursgruppsnamn än det du har använt i den här självstudieserien.
    - **Namn**: Ange ett namn på apptjänstplanen.
    - **Operativsystem:** välj **Linux**.
    - **Region**: välj en Azure-plats. Välj till exempel **USA, centrala**.
    - **Prisnivå:** för att spara kostnader, ändra SKU till **Basic B1** (under Utveckling/Test).

    ![Exportmallsportal för resurshanterarens mall](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Välj **Granska och skapa**.
1. Välj **Skapa**. Det tar en stund att skapa resursen.

## <a name="export-template"></a>Exportera mall

1. Välj **Gå till resurs**.

    ![Gå till resurs](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Välj **exportera mall**.

    ![Exportera mallen Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funktionen exportera mall tar den aktuella tillståndet för en resurs och genererar en mall för att distribuera den. Att exportera en mall kan vara ett bra sätt att snabbt få den JSON du behöver för att distribuera en resurs.

1. Kopiera definitionen av **Microsoft.Web/serverfarms** och parameterdefinitionen till mallen.

    ![Exporterad mall för resurshanterarens mall](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Vanligtvis är den exporterade mallen mer utförlig än du kanske vill när du skapar en mall. SKU-objektet i den exporterade mallen har till exempel fem egenskaper. Den här mallen fungerar, **name** men du kan bara använda namnegenskapen. Du kan börja med den exporterade mallen och sedan ändra den som du vill för att passa dina behov.

## <a name="revise-existing-template"></a>Ändra befintlig mall

Den exporterade mallen ger dig det mesta av det JSON du behöver, men du måste anpassa den för mallen. Var särskilt uppmärksam på skillnader i parametrar och variabler mellan mallen och den exporterade mallen. Exportprocessen känner inte till de parametrar och variabler som du redan har definierat i mallen.

I följande exempel beskrivs tilläggen till mallen. Den innehåller den exporterade koden plus några ändringar. Först ändras namnet på parametern så att den matchar namnkonventionen. För det andra används din platsparameter för platsen för apptjänstplanen. För det tredje tas **namnet** bort i **egenskapsobjektet** eftersom det här värdet är redundant med namnegenskapen på resursnivå. **name**

Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en mall.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Resursgruppen innehåller ett lagringskonto och en App Service-plan.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du lärde dig hur du exporterar en mall från Azure-portalen och hur du använder den exporterade mallen för mallutvecklingen. Du kan också använda Azure Quickstart-mallarna för att förenkla mallutvecklingen.

> [!div class="nextstepaction"]
> [Använda Snabbstartsmallar för Azure](template-tutorial-quickstart-template.md)

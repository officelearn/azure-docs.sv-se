---
title: Självstudiekurs - Använda snabbstartsmallar
description: Lär dig hur du använder Azure Quickstart-mallar för att slutföra mallutvecklingen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4b82e02ecc009e587b89d1fd151fd13f75a4bcf8
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408514"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Självstudiekurs: Använda Snabbstartsmallar för Azure

[Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/) är en databas med community-bidragade mallar. Du kan använda exempelmallarna i mallutvecklingen. I den här självstudien hittar du en definition av webbplatsresurser och lägger till den i din egen mall. Det tar cirka **12 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om exporterade mallar,](template-tutorial-export-template.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

I slutet av föregående självstudie hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Den här mallen fungerar för distribution av lagringskonton och apptjänstplaner, men du kanske vill lägga till en webbplats i den. Du kan använda färdiga mallar för att snabbt upptäcka den JSON som krävs för att distribuera en resurs.

## <a name="find-template"></a>Sök mall

1. Öppna [Snabbstartsmallar för Azure](https://azure.microsoft.com/resources/templates/)
1. I **Sök**anger du **distribuera linux-webbapp**.
1. Välj den med titeln **Distribuera en grundläggande Linux-webbapp**. Om du har problem med att hitta den, här är den [direkta länken](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Välj **Bläddra på GitHub**.
1. Välj **azuredeploy.json**.
1. Granska mallen. Leta särskilt efter `Microsoft.Web/sites` resursen.

    ![Snabbstartswebbplats för Resource Manager-mall](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Ändra befintlig mall

Sammanfoga snabbstartsmallen med den befintliga mallen:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Webbappnamnet måste vara unikt i Azure. För att förhindra att dubbletter av namn har **webAppPortalName-variabeln** uppdaterats från **"webAppPortalName": "[concat(parameters('webAppName"),'-webapp')]"** till **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"**.

Lägg till ett kommatecken `Microsoft.Web/serverfarms` i slutet av definitionen `Microsoft.Web/sites` för att skilja resursdefinitionen från definitionen.

Det finns ett par viktiga funktioner att notera i den här nya resursen.

Du kommer att märka att det har ett element med namnet **dependsOn** som är inställt på apptjänstplanen. Den här inställningen krävs eftersom apptjänstplanen måste finnas innan webbappen skapas. Elementet **dependsOn** talar om för Resource Manager hur resurserna ska beställas för distribution.

Egenskapen **serverFarmId** använder [funktionen resourceId.](template-functions-resource.md#resourceid) Den här funktionen hämtar den unika identifieraren för en resurs. I det här fallet får den unika identifieraren för apptjänstplanen. Webbappen är associerad med en specifik apptjänstplan.

## <a name="deploy-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en mall.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder en snabbstartsmall för mallutvecklingen. I nästa självstudiekurs lägger du till taggar i resurserna.

> [!div class="nextstepaction"]
> [Lägga till taggar](template-tutorial-add-tags.md)

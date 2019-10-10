---
title: Självstudie – Använd Azures snabb starts mallar | Microsoft Docs
description: Lär dig hur du använder Azures snabb starts mallar för att slutföra din mall utveckling.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a29d86d105579dda7c12b885e2977406f7b598a4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001480"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Självstudier: Använda Azure snabb starts mallar

[Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/) är en lagrings plats för community-mallar. Du kan använda exempel mallarna i din mall utveckling. I den här självstudien hittar du en resurs definition för webbplatsen och lägger till den i din egen mall. Det tar ungefär **12 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om exporterade mallar](template-tutorial-export-template.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Granska mallen

I slutet av den föregående själv studie kursen hade mallen följande JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Den här mallen används för att distribuera lagrings konton och App Service-planer, men du kanske vill lägga till en webbplats i den. Du kan använda färdiga mallar för att snabbt identifiera den JSON som krävs för att distribuera en resurs.

## <a name="find-a-template"></a>Hitta en mall

1. Öppna [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/)
1. I **Sök**anger du **distribuera Linux-webbapp**.
1. Välj den som innehåller rubriken **distribuera en grundläggande Linux-webbapp**. Om du har problem med att hitta det här är den [direkta länken](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Välj **Bläddra på GitHub**.
1. Välj **azuredeploy. JSON**.
1. Granska mallen. Leta särskilt efter resursen `Microsoft.Web/sites`.

    ![Snabb starts webbplats för Resource Manager-mall](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>Ändra den befintliga mallen

Slå samman snabb starts mal len med den befintliga mallen:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

WebApp-namnet måste vara unikt i Azure. För att förhindra dubblettnamn har **webAppPortalName** -variabeln uppdaterats från **"webAppPortalName": "[concat (parameters (" webAppName "),"-webapp ")]"** till **"webAppPortalName": "[concat (parameters (" webAppName "). uniqueString (resourceGroup (). ID)]** .

Lägg till ett kommatecken i slutet av `Microsoft.Web/serverfarms`-definitionen för att avgränsa resurs definitionen från `Microsoft.Web/sites`-definitionen.

Det finns några viktiga funktioner att notera i den här nya resursen.

Observera att det har ett element med namnet **dependsOn** som är inställt på App Service-planen. Den här inställningen är obligatorisk eftersom det måste finnas en app service-plan innan webbappen skapas. **DependsOn** -elementet visar Resource Manager så att du kan beställa resurser för distribution.

Egenskapen **serverFarmId** använder [resourceId](resource-group-template-functions-resource.md#resourceid) -funktionen. Den här funktionen hämtar den unika identifieraren för en resurs. I det här fallet får den unika identifieraren för App Service-planen. Webbappen är associerad med en speciell App Service-plan.

## <a name="deploy-the-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en mall.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder en snabb starts mall för mall utvecklingen. I nästa självstudie lägger du till taggar till resurserna.

> [!div class="nextstepaction"]
> [Lägg till taggar](template-tutorial-add-tags.md)

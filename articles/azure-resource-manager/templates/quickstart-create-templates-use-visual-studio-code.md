---
title: Skapa mall - Visual Studio-kod
description: Använd Visual Studio Code och Azure Resource Manager-malltillägget för att arbeta med Resource Manager-mallar.
author: neilpeterson
ms.date: 03/27/2019
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 4b1ecbf3a1f6083261e87537e20d52e755b77424
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640638"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Snabbstart: Skapa Azure Resource Manager-mallar med Visual Studio-kod

Azure Resource Manager-verktygen för Visual Studio-kod ger språkstöd, resurskodavsnitt och automatisk komplettering av resurser. De här verktygen hjälper till att skapa och validera Azure Resource Manager-mallar. I den här snabbstarten använder du tillägget för att skapa en Azure Resource Manager-mall från grunden. När du gör det uppstår tilläggsfunktionerna, till exempel ARM-mallavsnitt, validering, kompletteringar och parameterfilstöd.

För att slutföra den här snabbstarten behöver du [Visual Studio-kod](https://code.visualstudio.com/)med [tillägget Azure Resource Manager-verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) installerat. Du behöver också antingen [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller [Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0) installerad och autentiserad.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-arm-template"></a>Skapa en ARM-mall

Skapa och öppna med Visual Studio Kod en ny fil med namnet *azuredeploy.json*. Ange `arm` kodredigeraren, som initierar Azure Resource Manager-kodavsnitt för byggnadsställningar i en ARM-mall.

Välj `arm!` det här om du vill skapa en mall som omfattas av en Azure-resursgruppsdistribution.

![Avbildning som visar Azure Resource Manager-byggnadsställningar](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Det här kodavsnittet skapar de grundläggande byggstenarna för en ARM-mall.

![Bild som visar en helt byggnadsställning ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Observera att språkläget för Visual Studio-kod har ändrats från *JSON* till *Azure Resource Manager-mall*. Tillägget innehåller en språkserver som är specifik för ARM-mallar som tillhandahåller ARM-mallspecifik validering, slutförande och andra språktjänster.

![Bild som visar Azure Resource Manager som språkläge för Visual Studio-kod](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Lägga till en Azure-resurs

Tillägget innehåller utdrag för många Azure-resurser. Dessa utdrag kan användas för att enkelt lägga till resurser i malldistributionen.

Placera markören i **mallresursblocket,** skriv in `storage`och markera utdraget för *vapenlagring.*

![Bild som visar en resurs som läggs till i ARM-mallen](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Den här åtgärden lägger till en lagringsresurs i mallen.

![Avbildning som visar en Azure Storage-resurs i en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/5.png)

**Tabbtangenten** kan användas för att tabb igenom konfigurerbara egenskaper på lagringskontot.

![Bild som visar hur tabbtangenten kan användas för att navigera genom resurskonfigurationen](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Slutförande och validering

En av de mest kraftfulla funktionerna i tillägget är dess integrering med Azure-scheman. Azure-scheman ger tillägget funktioner för validering och resursmedveten slutförande. Nu ska vi ändra lagringskontot så att validering och slutförande visas. 

Uppdatera först lagringskontosdyt till `megaStorage`ett ogiltigt värde, till exempel . Observera att den här åtgärden `megaStorage` ger en varning som anger att det inte är ett giltigt värde.

![Bild som visar en ogiltig lagringskonfiguration](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Om du vill använda `megaStorage`slutförandefunktionerna tar du bort , placerar markören inuti de dubbla citaten och trycker på `ctrl`  +  `space`. Den här åtgärden visar en slutförandelista med giltiga värden.

![Bild som visar automatisk komplettering av tillägg](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Lägga till mallparametrar

Nu skapa och använda en parameter för att ange lagringskontots namn.

Placera markören i parameterblocket, lägg `par`till en vagnretur, skriv och välj sedan kodavsnittet. `arm-param-value` Den här åtgärden lägger till en allmän parameter i mallen.

![Bild som visar en parameter som läggs till i ARM-mallen](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Uppdatera namnet på parametern till `storageAccountName` `Storage Account Name`och beskrivningen till .

![Bild som visar den slutförda parametern i en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Azure storage-kontonamn har en minsta längd på 3 tecken och högst 24. Lägg `minLength` till `maxLength` både och till parametern och ange lämpliga värden.

![Bild som visar minLength och maxLength som läggs till i en ARM-mallparameter](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Uppdatera nu namnegenskapen för att använda parametern på lagringsresursen. Om du vill göra det tar du bort det aktuella namnet. Ange en dubbel offert `[`och en inledande hakparentes , som skapar en lista över ARM-mallfunktioner. Välj *parametrar* i listan. 

![Bild som visar automatisk komplettering när parametrar används i ARM-mallresurser](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Om du anger `'` ett enda citat inuti de runda hakparenteserna visas en lista över alla parametrar som definierats i mallen, i det här fallet *storageAccountName*. Välj parametern.

![Bild som visar slutförd parameter i en ARM-mallresurs](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Skapa en parameterfil

Med en ARM-mallparameterfil kan du lagra miljöspecifika parametervärden och skicka in dessa värden som en grupp vid distributionen. Du kan till exempel ha en parameterfil med värden som är specifika för en testmiljö och en annan för en produktionsmiljö.

Tillägget gör det enkelt att skapa en parameterfil från dina befintliga mallar. För att göra det högerklicka på mallen i `Select/Create Parameter File`kodredigeraren och välj .

![Bild som visar högerklicksprocessen för att skapa en parameterfil från en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/14.png)

`New`  >  Välj `All Parameters` > Välj ett namn och en plats för parameterfilen.

![Bild som visar dialogrutan namn och spara filer när du skapar en parameterfil från en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Den här åtgärden skapar en ny parameterfil och mappar den med mallen som den skapades från. Du kan se och ändra den aktuella mappningen av mall-/parameterfil i statusfältet Visual Studio-kod medan mallen är markerad.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nu när parameterfilen har mappats till mallen validerar tillägget både mallen och parameterfilen tillsammans. Om du vill visa den här valideringen `storageAccountName` i praktiken lägger du till ett tvåteckensvärde i parametern i parameterfilen och sparar filen.

![Bild som visar en ogiltig mall på grund av parameterfilproblem](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Navigera tillbaka till ARM-mallen och observera att ett fel har höjts som anger att värdet inte uppfyller parametervillkoren.

![Bild som visar en giltig ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Uppdatera värdet till något lämpligt, spara filen och navigera tillbaka till mallen. Observera att felet på parametern har lösts.

## <a name="deploy-the-template"></a>Distribuera mallen

Öppna den integrerade Visual Studio-kodterminalen med hjälp av tangentkombinationen `ctrl`  +  ```` ` ```` och använd antingen Azure CLI- eller Azure PowerShell-modulen för att distribuera mallen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs använder du Azure CLI- eller Azure PowerShell-modulen för att ta bort snabbstartresursgruppen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)

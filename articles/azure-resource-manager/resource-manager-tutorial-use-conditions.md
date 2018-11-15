---
title: Använda villkor i Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du distribuerar Azure-resurser baserat på villkor.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 64891991a96395b712cb99850874d974f8451ce6
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612839"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Självstudie: Använda villkor i Azure Resource Manager-mallar

Lär dig hur du distribuerar Azure-resurser baserat på villkor.

I självstudien [Ange resursdistributionsordning](./resource-manager-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator, ett virtuellt nätverk och några andra beroende resurser, däribland ett lagringskonto. I stället för att skapa ett nytt lagringskonto varje gång låter du användarna välja mellan att skapa ett nytt lagringskonto eller att använda ett befintligt. För att uppnå det här målet definierar du en extra parameter. Om värdet för parametern är ”new” (nytt) skapas ett nytt lagringskonto.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Ändra mallen
> * Distribuera mallen
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med [verktygstillägget för Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Det finns fem resurser som definieras av mallen:

    * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
5. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="modify-the-template"></a>Ändra mallen

Gör två ändringar av den befintliga mallen:

* Lägg till namnparameter för lagringskonto. Användare kan ange antingen ett nytt lagringskontonamn eller ett befintligt lagringskontonamn.
* Lägg till en ny parameter med namnet **newOrExisting**. Distributionen använder den här parametern för att avgöra var ett nytt lagringskonto ska skapas eller om ett befintligt lagringskonto ska användas.

Här följer proceduren för att göra ändringarna:

1. Öppna **azuredeploy.json** i Visual Studio Code.
2. Ersätt **variables('storageAccountName')** med **parameters('storageAccountName')** i hela mallen.  Det finns tre utseenden på **variables('storageAccountName')**.
3. Ta bort följande variabeldefinition:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Lägg till följande två parametrar i mallen:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string", 
      "allowedValues": [
        "new", 
        "existing"
      ]
    },
    ```
    Den uppdaterade parameterdefinitionen ser ut så här:

    ![Resource Manager-användningsvillkor](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Lägg till följande rad i början av lagringskontots definition.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Villkoret kontrollerar värdet för parametern med namnet **newOrExisting**. Om parametervärdet är **new** (nytt) skapar distributionen lagringskontot.

    Den uppdaterade lagringskontodefinitionen ser ut så här:

    ![Resource Manager-användningsvillkor](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Uppdatera **storageUri** med följande värde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Den här ändringen är nödvändig när du använder ett befintligt lagringskonto under en annan resursgrupp.

7. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) för att distribuera mallen.

När du distribuerar mallen med hjälp av Azure PowerShell måste du ange en extra parameter. För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Se [Förutsättningar](#prerequisites).

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> Distributionen misslyckas om **newOrExisting** är **new** (nytt) men lagringskontonamnet som anges redan finns.

Försök göra en annan distribution med **newOrExisting** inställt på ”existing” (befintligt) och ange ett befintligt lagringskonto. Information om hur du skapar ett lagringskonto i förväg finns i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklade du en mall som gör att användarna kan välja mellan att skapa ett nytt lagringskonto och att använda ett befintligt lagringskonto. Om du vill lära dig hur du hämtar hemligheter från Azure Key Vault och använder hemligheterna som lösenord i malldistributionen läser du:

> [!div class="nextstepaction"]
> [Integrera Key Vault i malldistribution](./resource-manager-tutorial-use-key-vault.md)

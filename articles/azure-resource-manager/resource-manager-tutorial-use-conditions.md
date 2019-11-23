---
title: Use condition in templates
description: Lär dig hur du distribuerar Azure-resurser baserat på villkor. Shows how to either deploy a new resource or use an existing resource.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7aecc25daed8f73e903b181d782e53c0ea5acd74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325338"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Självstudie: Använda villkor i Azure Resource Manager-mallar

Lär dig hur du distribuerar Azure-resurser baserat på villkor.

I självstudien [Ange resursdistributionsordning](./resource-manager-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator, ett virtuellt nätverk och några andra beroende resurser, däribland ett lagringskonto. I stället för att skapa ett nytt lagringskonto varje gång låter du användarna välja mellan att skapa ett nytt lagringskonto eller att använda ett befintligt. För att uppnå det här målet definierar du en extra parameter. Om värdet för parametern är ”new” (nytt) skapas ett nytt lagringskonto. Otherwise, an existing storage account with the name provided is used.

![Resource Manager template use condition diagram](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Ändra mallen
> * Distribuera mallen
> * Rensa resurser

This tutorial only covers a basic scenario of using conditions. Mer information finns här:

* [Template file structure: Condition](conditional-resource-deployment.md).
* [Conditionally deploy a resource in an Azure Resource Manager template](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Template function: If](./resource-group-template-functions-logical.md#if).
* [Comparison functions for Azure Resource Manager templates](./resource-group-template-functions-comparison.md)

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
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

     Det är bra att få lite grundläggande förståelse av mallen innan den anpassas.
5. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="modify-the-template"></a>Ändra mallen

Gör två ändringar av den befintliga mallen:

* Lägg till namnparameter för lagringskonto. Användare kan ange antingen ett nytt lagringskontonamn eller ett befintligt lagringskontonamn.
* Lägg till en ny parameter med namnet **newOrExisting**. The deployment uses this parameter to determine whether to create a new storage account or use an existing storage account.

Här följer proceduren för att göra ändringarna:

1. Öppna **azuredeploy.json** i Visual Studio Code.
2. Replace the three **variables('storageAccountName')** with **parameters('storageAccountName')** in the whole template.
3. Ta bort följande variabeldefinition:

    ![Resource Manager template use condition diagram](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

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
6. Update the **storageUri** property of the virtual machine resource definition with the following value:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Den här ändringen är nödvändig när du använder ett befintligt lagringskonto under en annan resursgrupp.

7. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Follow the instructions in [Deploy the template](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) to open the Cloud shell and upload the revised template, and then run the following PowerShell script to deploy the template.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> Distributionen misslyckas om **newOrExisting** är **new** (nytt) men lagringskontonamnet som anges redan finns.

Try making another deployment with **newOrExisting** set to "existing" and specify an existing storage account. Information om hur du skapar ett lagringskonto i förväg finns i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. To delete the resource group, select **Try it** to open the Cloud shell. To paste the PowerShell script, right-click the shell pane, and then select **Paste**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklade du en mall som gör att användarna kan välja mellan att skapa ett nytt lagringskonto och att använda ett befintligt lagringskonto. Om du vill lära dig hur du hämtar hemligheter från Azure Key Vault och använder hemligheterna som lösenord i malldistributionen läser du:

> [!div class="nextstepaction"]
> [Integrera Key Vault i malldistribution](./resource-manager-tutorial-use-key-vault.md)

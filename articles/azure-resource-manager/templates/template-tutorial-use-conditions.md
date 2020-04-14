---
title: Använd villkor i mallar
description: Lär dig hur du distribuerar Azure-resurser baserat på villkor. Visar hur du antingen distribuerar en ny resurs eller använder en befintlig resurs.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8f51c65489efeed1fa18e70bd75e7370a9e59903
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260669"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Självstudiekurs: Använd villkor i ARM-mallar

Lär dig hur du distribuerar Azure-resurser baserat på villkor i en ARM-mall (Azure Resource Manager).

I självstudien [Ange resursdistributionsordning](./template-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator, ett virtuellt nätverk och några andra beroende resurser, däribland ett lagringskonto. I stället för att skapa ett nytt lagringskonto varje gång låter du användarna välja mellan att skapa ett nytt lagringskonto eller att använda ett befintligt. För att uppnå det här målet definierar du en extra parameter. Om värdet för parametern är ”new” (nytt) skapas ett nytt lagringskonto. Annars används ett befintligt lagringskonto med det angivna namnet.

![Villkorsdiagram för resurshanteraren använder villkor](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Ändra mallen
> * Distribuera mallen
> * Rensa resurser

Den här självstudien täcker bara ett grundläggande scenario med att använda villkor. Mer information finns i:

* [Mallfilstruktur: Villkor](conditional-resource-deployment.md).
* [Distribuera en resurs villkorligt i en ARM-mall](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Mallfunktion: Om](./template-functions-logical.md#if).
* [Jämförelsefunktioner för ARM-mallar](./template-functions-comparison.md)

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudiekurs: Integrera Azure Key Vault i ARM-malldistribution](./template-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure QuickStart-mallar är en databas för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Välj **Öppna**>**fil**i Visual Studio-kod .
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Det finns sex resurser som definieras av mallen:

   * [**Microsoft.Storage/storageKonton**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAdresser**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Det är bra att granska mallreferensen innan du anpassar en mall.

1. Välj **Spara fil**>**som** om du vill spara en kopia av filen på den lokala datorn med namnet **azuredeploy.json**.

## <a name="modify-the-template"></a>Ändra mallen

Gör två ändringar av den befintliga mallen:

* Lägg till namnparameter för lagringskonto. Användare kan ange antingen ett nytt lagringskontonamn eller ett befintligt lagringskontonamn.
* Lägg till en ny parameter med namnet **newOrExisting**. Distributionen använder den här parametern för att avgöra om du vill skapa ett nytt lagringskonto eller använda ett befintligt lagringskonto.

Här följer proceduren för att göra ändringarna:

1. Öppna **azuredeploy.json** i Visual Studio Code.
1. Ersätt de tre **variablerna ("storageAccountName")** med **parametrar ("storageAccountName")** i hela mallen.
1. Ta bort följande variabeldefinition:

    ![Villkorsdiagram för resurshanteraren använder villkor](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Lägg till följande två parametrar i början av parameteravsnittet:

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

    Tryck på **[ALT]+[SKIFT]+F** för att formatera mallen i Visual Studio-kod.

    Den uppdaterade parameterdefinitionen ser ut så här:

    ![Resource Manager-användningsvillkor](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Lägg till följande rad i början av lagringskontots definition.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Villkoret kontrollerar värdet för parametern med namnet **newOrExisting**. Om parametervärdet är **new** (nytt) skapar distributionen lagringskontot.

    Den uppdaterade lagringskontodefinitionen ser ut så här:

    ![Resource Manager-användningsvillkor](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Uppdatera **storageUri-egenskapen** för resursdefinitionen för virtuella datorer med följande värde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Den här ändringen är nödvändig när du använder ett befintligt lagringskonto under en annan resursgrupp.

1. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [Distribuera mallen](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) för att öppna Cloud Shell och ladda upp den reviderade mallen och kör sedan följande PowerShell-skript för att distribuera mallen.

> [!IMPORTANT]
> Namnet på lagringskontot måste vara unikt i Azure. Namnet får bara ha gemener eller siffror. Det kan inte vara längre än 24 tecken. Lagringskontonamnet är projektnamnet med "store" bifogat. Kontrollera att projektnamnet och det genererade lagringskontonamnet uppfyller kraven för lagringskontots namn.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

> [!NOTE]
> Distributionen misslyckas om **newOrExisting** är **new** (nytt) men lagringskontonamnet som anges redan finns.

Prova att göra en annan distribution med **newOrExisting** inställd på "befintlig" och ange ett befintligt lagringskonto. Information om hur du skapar ett lagringskonto i förväg finns i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om du vill ta bort resursgruppen väljer du Prova den **för** att öppna Cloud Shell. Om du vill klistra in PowerShell-skriptet högerklickar du på skalfönstret och väljer sedan **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklade du en mall som gör att användarna kan välja mellan att skapa ett nytt lagringskonto och att använda ett befintligt lagringskonto. Om du vill lära dig hur du hämtar hemligheter från Azure Key Vault och använder hemligheterna som lösenord i malldistributionen läser du:

> [!div class="nextstepaction"]
> [Integrera Key Vault i malldistribution](./template-tutorial-use-key-vault.md)

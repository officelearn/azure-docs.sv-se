---
title: Mall med beroende resurser
description: Lär dig hur du skapar en Azure Resource Manager-mall med flera resurser samt hur du distribuerar den med hjälp av Azure-portalen
author: mumian
ms.date: 04/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bbe973f5c701f55705fe197f56f5f8ab1d9e8c68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260801"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Självstudiekurs: Skapa ARM-mallar med beroende resurser

Lär dig hur du skapar en ARM-mall (Azure Resource Manager) för att distribuera flera resurser och konfigurera distributionsordern. När du har skapat mallen distribuerar du mallen med Cloud Shell från Azure-portalen.

I den här självstudien skapar du ett lagringskonto, en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. Vissa resurser kan inte distribueras förrän en annan resurs finns. Till exempel kan du inte skapa den virtuella datorn förrän dess lagringskonto och nätverksgränssnitt finns. Du kan definiera den här relationen genom att göra en resurs beroende av de andra resurserna. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Mer information finns i [Definiera ordningen för distribution av resurser i ARM-mallar](./define-resource-dependency.md).

![Resurshanterarens mallberoende resursdistributionsorderdiagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Utforska mallen
> * Distribuera mallen

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
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Välj **Spara fil**>**som** om du vill spara en kopia av filen på den lokala datorn med namnet **azuredeploy.json**.

## <a name="explore-the-template"></a>Utforska mallen

När du utforskar mallen i det här avsnittet kan du försöka besvara följande frågor:

* Hur många Azure-resurser är definierade i den här mallen?
* En av resurserna är ett Azure-lagringskonto.  Ser definitionen ut som den som används i den senaste självstudien?
* Kan du hitta mallreferenserna för de resurser som är definierade i mallen?
* Kan du hitta resursernas beroenden?

1. Från Visual Studio Code döljer du elementen tills du bara ser elementen på den första och andra nivån i **resurser**:

    ![Azure Resource Manager-mallar i Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Det finns sex resurser som definieras av mallen:

   * [**Microsoft.Storage/storageKonton**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAdresser**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Det är bra att granska mallreferensen innan du anpassar en mall.

1. Expandera den första resursen. Det är ett lagringskonto. Jämför resursdefinitionen med [mallreferensen](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Lagringsdefinition för Azure Resource Manager-mallar i Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Expandera den andra resursen. Resurstypen är `Microsoft.Network/publicIPAddresses`. Jämför resursdefinitionen med [mallreferensen](/azure/templates/microsoft.network/publicipaddresses).

    ![Definition av offentlig IP-adress för Azure Resource Manager-mallar i Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Expandera den tredje resursen. Resurstypen är `Microsoft.Network/networkSecurityGroups`. Jämför resursdefinitionen med [mallreferensen](/azure/templates/microsoft.network/networksecuritygroups).

    ![Visual Studio Code Azure Resource Manager-mallar nätverkssäkerhetsgruppdefinition](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Expandera den fjärde resursen. Resurstypen är `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code Azure Resource Manager-mallar beror påPå](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Med elementet dependsOn kan du definiera en resurs som beroende på en eller flera resurser. Med elementet dependsOn kan du definiera en resurs som beroende på en eller flera resurser.  Den här resursen är beroende av en annan resurs:

    * `Microsoft.Network/networkSecurityGroups`

1. Expandera den femtio resursen. Resurstypen är `Microsoft.Network/networkInterfaces`. Resursen beror på två resurser:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Expandera den sjätte resursen. Den här resursen är en virtuell dator. Den beror på två resurser:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Följande diagram illustrerar resurserna och beroendeinformation för den här mallen:

![Azure Resource Manager-mallar i Visual Studio Code – beroendediagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Genom att ange beroendena distribuerar Resource Manager effektivt lösningen. Den distribuerar lagringskontot, en offentlig IP-adress och ett virtuellt nätverk parallellt eftersom de inte har några beroenden. När den offentliga IP-adressen och det virtuella nätverket har distribuerats skapas ett nätverksgränssnitt. När alla andra resurser har distribuerats så distribuerar Resource Manager den virtuella datorn.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Följ instruktionerna i [Distribuera mallen](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) för att öppna Cloud Shell och ladda upp den reviderade mallen.

1. Kör följande PowerShell-skript för att distribuera mallen.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Kör följande PowerShell-kommando för att visa den nyligen skapade virtuella datorn:

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $resourceGroupName = "${projectName}rg"
    $vmName = "SimpleWinVM"

    Get-AzVM -Name $vmName -ResourceGroupName $resourceGroupName
    
    Write-Host "Press [ENTER] to continue ..."
    ```

    Namnet på den virtuella datorn är hårdkodat som **SimpleWinVM** i mallen.

1. RDP till den virtuella datorn för att verifiera att den virtuella datorn har skapats.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utvecklat och distribuerat en mall för att skapa en virtuell dator, ett virtuellt nätverk och de beroende resurserna. Mer information om hur du använder distributionsskript för att utföra distributionsåtgärder före/efter finns i:

> [!div class="nextstepaction"]
> [Använda distributionsskript](./template-tutorial-deployment-script.md)

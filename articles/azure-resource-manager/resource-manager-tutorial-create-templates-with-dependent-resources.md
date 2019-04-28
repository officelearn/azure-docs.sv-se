---
title: Skapa Azure Resource Manager-mallar med beroende resurser | Microsoft Docs
description: Lär dig hur du skapar en Azure Resource Manager-mall med flera resurser samt hur du distribuerar den med hjälp av Azure-portalen
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 486a13db9cf18cb44a063d37dde4a657f6dc625c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103679"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Självstudier: Skapa Azure Resource Manager-mallar med beroende resurser

Lär dig hur du skapar en Azure Resource Manager-mall för att distribuera flera resurser och konfigurera distributionsordning. När du har skapat mallen distribuerar du mallen med hjälp av Cloud Shell från Azure-portalen.

I den här självstudien skapar du ett lagringskonto, en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. Vissa resurser kan inte distribueras förrän en annan resurs finns. Till exempel kan du inte skapa den virtuella datorn förrän dess lagringskonto och nätverksgränssnitt finns. Du kan definiera den här relationen genom att göra en resurs beroende av de andra resurserna. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Mer information finns på sidan om att [definiera ordningen för distribution av resurser i Azure Resource Manager-mallar](./resource-group-define-dependencies.md).

![Resource manager-mall beroende resurser distribution ordning diagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Utforska mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med verktygstillägget för Resource Manager.  Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="explore-the-template"></a>Utforska mallen

När du utforskar mallen i det här avsnittet kan du försöka besvara följande frågor:

* Hur många Azure-resurser är definierade i den här mallen?
* En av resurserna är ett Azure-lagringskonto.  Ser definitionen ut som den som används i den senaste självstudien?
* Kan du hitta mallreferenserna för de resurser som är definierade i mallen?
* Kan du hitta resursernas beroenden?

1. Från Visual Studio Code döljer du elementen tills du bara ser elementen på den första och andra nivån i **resurser**:

    ![Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Det finns fem resurser som definieras av mallen:

   * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.

2. Expandera den första resursen. Det är ett lagringskonto. Jämför resursdefinitionen med [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Lagringsdefinition för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expandera den andra resursen. Resurstypen är `Microsoft.Network/publicIPAddresses`. Jämför resursdefinitionen med [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Definition av offentlig IP-adress för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Expandera den fjärde resursen. Resurstypen är `Microsoft.Network/networkInterfaces`:  

    ![Azure Resource Manager-mallar i Visual Studio Code – dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Med elementet dependsOn kan du definiera en resurs som beroende på en eller flera resurser. Resursen beror på två resurser:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Expandera den femte resursen. Den här resursen är en virtuell dator. Den beror på två resurser:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Följande diagram illustrerar resurserna och beroendeinformation för den här mallen:

![Azure Resource Manager-mallar i Visual Studio Code – beroendediagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Genom att ange beroendena distribuerar Resource Manager effektivt lösningen. Den distribuerar lagringskontot, en offentlig IP-adress och ett virtuellt nätverk parallellt eftersom de inte har några beroenden. När den offentliga IP-adressen och det virtuella nätverket har distribuerats skapas ett nätverksgränssnitt. När alla andra resurser har distribuerats så distribuerar Resource Manager den virtuella datorn.

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det finns många metoder för att distribuera mallar.  I den här självstudien använder du Cloud Shell från Azure-portalen.

1. Logga in på [Cloud Shell](https://shell.azure.com). 
2. Välj **PowerShell** högst upp till vänster i Cloud Shell och välj sedan **Bekräfta**.  Du använder PowerShell i den här självstudien.
3. Välj **Ladda upp fil** från Cloud Shell:

    ![Azure portal Cloud shell upload file](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Välj den mall som du sparade tidigare i självstudien. Standardnamnet är **azuredeploy.json**.  Om du har en fil med samma namn skrivs den gamla filen över utan något meddelande.

    Alternativt kan du använda kommandona **ls $HOME** och **cat $HOME/azuredeploy.json** för att kontrollera att filerna laddas upp. 

5. Från Cloud Shell kör du följande PowerShell-kommandon. För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Se [Förutsättningar](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Kör följande PowerShell-kommando för att visa den nyligen skapade virtuella datorn:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Namnet på den virtuella datorn är hårdkodat som **SimpleWinVM** i mallen.

9. RDP till den virtuella datorn för att verifiera att den virtuella datorn har skapats.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utvecklat och distribuerat en mall för att skapa en virtuell dator, ett virtuellt nätverk och de beroende resurserna. Information om hur du lär dig att distribuera Azure-resurser baserat på villkor finns i:

> [!div class="nextstepaction"]
> [Använda villkor](./resource-manager-tutorial-use-conditions.md)

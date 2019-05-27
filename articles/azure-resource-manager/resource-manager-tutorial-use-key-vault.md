---
title: Integrera Azure Key Vault vid malldistribution i Resource Manager | Microsoft Docs
description: Lär dig att använda Azure Key Vault för att skicka säkra parametrar under malldistributionen av Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239245"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager

Lär dig hur du hämtar hemligheter från Azure Key Vault och skickar hemligheterna som parametrar under Resource Manager-distributionen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Mer information finns i [Använd Azure Key Vault för att skicka säkra parametervärden under distributionen](./resource-manager-keyvault-parameter.md)

I den [ange resource distributionsordning](./resource-manager-tutorial-create-templates-with-dependent-resources.md) självstudien får du skapa en virtuell dator. Du måste ange VM administratörens användarnamn och lösenord. I stället för att ange lösenord, kan du förväg lagra lösenordet i ett Azure Key Vault och sedan anpassa mallen för att hämta lösenordet från nyckelvalvet under distributionen.

![Resource Manager mall Key Vault-integrering diagram](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett nyckelvalv
> * Öppna en snabbstartsmall
> * Redigera parameterfilen
> * Distribuera mallen
> * Verifiera distributionen
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med [verktygstillägget för Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Kontrollera att det genererade lösenordet uppfyller den virtuella datorns krav för lösenord. Varje Azure-tjänst har specifika lösenordskrav. Lösenordskraven för den virtuella datorn finns i [Vilka är lösenordskraven när du skapar en virtuell dator?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Förbereda ett nyckelvalv

I det här avsnittet ska du skapa ett nyckelvalv och lägga till en hemlighet i nyckelvalvet, så att du kan hämta hemligheten när du distribuerar mallen. Det finns många sätt att skapa ett nyckelvalv. I den här självstudien använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Mallen gör följande:

* Skapa ett nyckelvalv med egenskapen `enabledForTemplateDeployment` aktiverad. Den här egenskapen måste vara sann innan malldistributionsprocessen kan komma åt hemligheterna som definierats i det här nyckelvalvet.
* Lägg till en hemlighet i nyckelvalvet.  Hemligheten lagrar administratörslösenord för den virtuella datorn.

> [!NOTE]
> Om du (som användaren som distribuerar mallen för den virtuella datorn) inte är ägare av eller medarbetare i nyckelvalvet, måste ägaren eller medarbetaren för nyckelvalvet ge dig åtkomst till Microsoft.KeyVault/vaults/deploy/action för nyckelvalvet. Mer information finns i [Använd Azure Key Vault för att skicka säkra parametervärden under distributionen](./resource-manager-keyvault-parameter.md)

För att köra följande PowerShell-skript, Välj **prova** att öppna cloudshell. Om du vill klistra in skriptet, högerklicka på shell-fönstret och välj sedan **klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Få delar av viktig information:

* Resursgruppens namn är projektnamnet på med **rg** sist. Att göra det enklare att [Rensa resurserna som du skapade i den här självstudien](#clean-up-resources), Använd samma namn och resursgrupp projektgrupp namn när du [distribuera mallen nästa](#deploy-the-template).
* Standardnamnet för det hemliga namnet är **vmAdminPassword**. Det är hårdkodad i mallen.
* Om du för mallen för att hämta hemligheten måste du aktivera en åtkomstprincip som kallas **Aktivera åtkomst till Azure Resource Manager för malldistribution** för nyckelvalvet. Den här principen är aktiverad i mallen. Läs mer om den här åtkomstprincip [distribuera nyckelvalv och hemligheter](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Mallen har en utdatavärde kallas **keyVaultId**. Anteckna värdet. Du behöver detta ID när du distribuerar den virtuella datorn. Resurs-ID-formatet är:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

När du kopiera och klistra in ID, kan ID: T delas i flera rader. Du måste slå samman raderna och ta bort extra blanksteg.

Verifiera distributionen genom att köra följande PowerShell-kommando i fönstret samma shell för att hämta hemligheten i klartext. Kommandot fungerar bara i samma shell-sessionen eftersom den använder en variabel $keyVaultName definierats i föregående PowerShell-skript.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu har du förberett key vault och en hemlighet i följande avsnitt visar hur du anpassar en befintlig mall för att hämta hemligheten under distributionen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen. Det är samma scenario som används i [Självstudie: Skapa Azure Resource Manager-mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Det finns fem resurser som definieras av mallen:

   * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
5. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.
6. Upprepa steg 1–4 för att öppna följande URL och spara sedan filen som **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Redigera parameterfilen

Du behöver inte göra några ändringar i mallfilen.

1. Öppna **azuredeploy.parameters.json** i Visual Studio Code om den inte är öppen.
2. Uppdatera parametern **adminPassword** till:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Ersätt värdet för **id** med resurs-ID för ditt nyckelvalv som skapades i föregående procedur.

    ![integrera key vault och parameterfilen för Resource Manager-malldistribution av virtuell dator](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Ge värden till:

    * **adminUsername**: namnge administratörskontot för den virtuella datorn.
    * **dnsLabelPrefix**: namnge prefixet dnsLabelPrefix.

    Se ett exempel på föregående skärmbild.

4. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) för att distribuera mallen. Du måste överföra både **azuredeploy.json** och **azuredeploy.parameters.json** till cloudshell och använder sedan följande PowerShell-skript för att distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

När du distribuerar mallen använder du samma resursgrupp som nyckelvalvet. Det gör enklare när du rensar bland resurserna. Du behöver bara att ta bort en resursgrupp istället för två.

## <a name="validate-the-deployment"></a>Verifiera distributionen

När du har distribuerat den virtuella datorn kan du testa inloggningen med lösenordet som lagrats i nyckelvalvet.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **Resource grouips**/**YourResourceGroupName>** /**simpleWinVM**
3. Välj **anslut** högst upp.
4. Välj **Ladda ned RDP-fil** och följ sedan anvisningarna för att logga in på den virtuella datorn med lösenordet som lagrats i nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien hämtade du en hemlighet från Azure Key Vault och använde hemligheten i din malldistribution.  Om du vill lära dig mer om hur du skapar länkade mallar kan du se:

> [!div class="nextstepaction"]
> [Skapa länkade mallar](./resource-manager-tutorial-create-linked-templates.md)

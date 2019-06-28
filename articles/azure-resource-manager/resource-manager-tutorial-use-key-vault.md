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
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436579"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Självstudier: Integrera Azure Key Vault i Resource Manager för malldistributionen

Lär dig mer om att hämta hemligheter från ett Azure key vault och skicka hemligheterna som parametrar när du distribuerar Azure Resource Manager. Värdet för parametern exponeras aldrig, eftersom du referera till endast dess nyckelvalv-ID. Mer information finns i [använda Azure Key Vault för att skicka säkra parametervärdet under distributionen](./resource-manager-keyvault-parameter.md).

I den [ange resource distributionsordning](./resource-manager-tutorial-create-templates-with-dependent-resources.md) självstudien får du skapa en virtuell dator (VM). Du måste ange VM administratörens användarnamn och lösenord. I stället för att ange lösenord, kan du förväg lagra lösenordet i ett Azure key vault och sedan anpassa mallen för att hämta lösenordet från nyckelvalvet under distributionen.

![Diagram som visar integreringen av en Resource Manager-mall med ett nyckelvalv](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

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

* [Visual Studio Code](https://code.visualstudio.com/) med den [tillägget Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För ökad säkerhet kan du använda ett genererat lösenord för administratörskontot för virtuell dator. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Kontrollera att genererade lösenordet uppfyller kraven för virtuella datorer-lösenord. Varje Azure-tjänst har specifika lösenordskrav. Lösenordskrav VM finns [vilka är lösenordskraven för när du skapar en virtuell dator?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Förbereda ett nyckelvalv

I det här avsnittet ska du skapa ett nyckelvalv och lägga till en hemlighet, så att du kan hämta hemligheten när du distribuerar mallen. Det finns många sätt att skapa ett nyckelvalv. I den här självstudien använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Den här mallen gör följande:

* Skapar ett nyckelvalv med den `enabledForTemplateDeployment` egenskapen aktiverat. Den här egenskapen måste vara *SANT* innan malldistributionen processen kan komma åt hemligheter som definieras i nyckelvalvet.
* Lägger till en hemlighet i nyckelvalvet. Hemligheten lagrar administratörslösenord för virtuell dator.

> [!NOTE]
> Användaren som distribuerar mallen för virtuella datorer, om du inte är ägare eller deltagare till nyckelvalvet, ägare eller deltagare måste ge åtkomst till den *Microsoft.KeyVault/vaults/deploy/action* behörighet för nyckelvalvet. Mer information finns i [använda Azure Key Vault för att skicka en säker parameter-värdet under distributionen](./resource-manager-keyvault-parameter.md).

För att köra följande Azure PowerShell-skript, Välj **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet, högerklicka på shell-fönstret och välj sedan **klistra in**.

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

> [!IMPORTANT]
> * Resursgruppens namn är projektnamn, men med **rg** läggas till den. Att göra det enklare att [Rensa resurserna som du skapade i den här självstudien](#clean-up-resources), Använd samma namn och resursgrupp projektgrupp namn när du [distribuera mallen nästa](#deploy-the-template).
> * Standardnamnet för hemligheten är **vmAdminPassword**. Det är hårdkodad i mallen.
> * Om du vill aktivera mallen för att hämta hemligheten måste du aktivera en åtkomstprincip som kallas ”Aktivera åtkomst till Azure Resource Manager för malldistribution” för key vault. Den här principen är aktiverad i mallen. Läs mer om åtkomstprincipen [distribuera nyckelvalv och hemligheter](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Mallen har ett utdatavärde, kallas *keyVaultId*. Anteckna ID-värdet för senare användning när du distribuerar den virtuella datorn. Resursgruppens ID-format är:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

När du kopiera och klistra in ID: T, kan den delas i flera rader. Sammanfogar raderna och ta bort extra blanksteg.

Verifiera distributionen genom att köra följande PowerShell-kommando i fönstret samma shell för att hämta hemligheten i klartext. Kommandot fungerar bara i samma shell-session, eftersom den använder variabeln *$keyVaultName*, som definieras i föregående PowerShell-skriptet.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu har du skapat ett nyckelvalv och en hemlighet. I följande avsnitt visas hur du anpassar en befintlig mall för att hämta hemligheten under distributionen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är lagringsplatser för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien kallas [distribuera en enkel Windows-VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. I Visual Studio Code väljer du **Arkiv** > **Öppna fil**.

1. I den **filnamn** rutan, klistra in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen. Scenariot är samma som det som används i [självstudien: Skapa Azure Resource Manager-mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   Mallen definierar fem resurser:

   * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Det är bra att ha viss grundläggande förståelse för mallen innan du anpassa den.

1. Välj **filen** > **Spara som**, och spara en kopia av filen till den lokala datorn med namnet *azuredeploy.json*.

1. Upprepa steg 1 – 3 för att öppna följande URL och spara filen som *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Redigera parameterfilen

Du behöver inte göra några ändringar i mallfilen.

1. Öppna i Visual Studio Code *azuredeploy.parameters.json* om den inte redan är öppen.
1. Uppdatera den `adminPassword` parameter:

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
    > Ersätt värdet för **id** med resurs-ID för nyckelvalvet som du skapade i föregående procedur.

    ![integrera nyckelvalvet och Resource Manager-dator distributionsfilen med mallparametrar](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Uppdatera följande värden:

    * **adminUsername**: Namnet på administratörskontot för virtuell dator.
    * **dnsLabelPrefix**: Namnet dnsLabelPrefix-värdet.

    Exempel på namn, finns i föregående bild.

1. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Ladda upp både *azuredeploy.json* och *azuredeploy.parameters.json* till Cloud Shell och använder sedan följande PowerShell-skript för att distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

När du distribuerar mallen kan du använda samma resursgrupp som du använde i nyckelvalvet. Den här metoden gör det enklare för dig att rensa resurserna, eftersom du måste ta bort bara en resursgrupp istället för två.

## <a name="validate-the-deployment"></a>Verifiera distributionen

När den virtuella datorn har distribuerats, kan du testa autentiseringsuppgifter för inloggning med lösenord som lagras i nyckelvalvet.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Välj **resursgrupper** >  **\<*YourResourceGroupName*>**  > **simpleWinVM** .
1. Välj **ansluta** högst upp.
1. Välj **ladda ned RDP-filen**, och följ sedan anvisningarna för att logga in till den virtuella datorn med det lösenord som lagras i nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dina Azure-resurser kan du rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien får hämtade du en hemlighet från din Azure key vault. Du använde sedan hemligheten i din för malldistribution. Om du vill lära dig mer om hur du skapar länkade mallar kan du se:

> [!div class="nextstepaction"]
> [Skapa länkade mallar](./resource-manager-tutorial-create-linked-templates.md)

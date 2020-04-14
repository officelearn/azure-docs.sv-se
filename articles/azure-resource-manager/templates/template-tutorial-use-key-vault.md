---
title: Använda Azure Key Vault i mallar
description: Lär dig att använda Azure Key Vault för att skicka säkra parametrar under malldistributionen av Resource Manager
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 440835f50d2ef9c03dabc7a66e8f162e3fa15b2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260827"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Självstudiekurs: Integrera Azure Key Vault i distributionen av ARM-mall

Lär dig hur du hämtar hemligheter från ett Azure-nyckelvalv och skickar hemligheterna som parametrar när du distribuerar en ARM-mall (Azure Resource Manager). Parametervärdet visas aldrig, eftersom du bara refererar till dess nyckelvalvs-ID. Mer information finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](./key-vault-parameter.md).

I [självstudiekursen Ange resursdistributionsorder](./template-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator (VM). Du måste ange användarnamn och lösenord för vm-administratören. I stället för att ange lösenordet kan du förförvara lösenordet i ett Azure-nyckelvalv och sedan anpassa mallen för att hämta lösenordet från nyckelvalvet under distributionen.

![Diagram som visar integreringen av en Resource Manager-mall med ett nyckelvalv](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett nyckelvalv
> * Öppna en snabbstartsmall
> * Redigera parameterfilen
> * Distribuera mallen
> * Verifiera distributionen
> * Rensa resurser

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).
* Om du vill öka säkerheten använder du ett genererat lösenord för vm-administratörskontot. Här är ett exempel för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```
    Kontrollera att det genererade lösenordet uppfyller kraven på vm-lösenord. Varje Azure-tjänst har specifika lösenordskrav. För lösenordskraven för den virtuella datorn finns i [Vilka lösenordskrav finns när du skapar en virtuell dator?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="prepare-a-key-vault"></a>Förbereda ett nyckelvalv

I det här avsnittet skapar du ett nyckelvalv och lägger till en hemlighet i det, så att du kan hämta hemligheten när du distribuerar mallen. Det finns många sätt att skapa ett nyckelvalv. I den här självstudien använder du Azure PowerShell för att distribuera en [ARM-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Den här mallen gör följande:

* Skapar ett nyckelvalv `enabledForTemplateDeployment` med egenskapen aktiverad. Den här egenskapen måste vara *true* innan malldistributionsprocessen kan komma åt hemligheterna som har definierats i nyckelvalvet.
* Lägger till en hemlighet i nyckelvalvet. Hemligheten lagrar vm-administratörens lösenord.

> [!NOTE]
> Som den användare som distribuerar mallen för den virtuella datorn måste ägaren eller deltagaren som deltagare i nyckelvalvet, som den användare som distribuerar mallen för den virtuella datorn, om du inte är ägare till eller en deltagare till nyckelvalvet, ge dig åtkomst till *behörigheten Microsoft.KeyVault/vaults/deploy/action* för nyckelvalvet. Mer information finns i [Använda Azure Key Vault för att skicka ett säkert parametervärde under distributionen](./key-vault-parameter.md).

Om du vill köra följande Azure PowerShell-skript väljer du **Prova det** för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på skalfönstret och väljer sedan **Klistra in**.

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
> * Resursgruppsnamnet är projektnamnet, men med **rg** bifogat. Om du vill göra det enklare att [rensa de resurser som du skapade i den här självstudien](#clean-up-resources)använder du samma projektnamn och resursgruppsnamn när du [distribuerar nästa mall](#deploy-the-template).
> * Standardnamnet för hemligheten är **vmAdminPassword**. Det är hårdkodat i mallen.
> * Om du vill att mallen ska kunna hämta hemligheten måste du aktivera en åtkomstprincip som heter "Aktivera åtkomst till Azure Resource Manager för malldistribution" för nyckelvalvet. Den här principen är aktiverad i mallen. Mer information om åtkomstprincipen finns i [Distribuera nyckelvalv och hemligheter](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Mallen har ett utdatavärde, kallat *keyVaultId*. Skriv ned ID-värdet för senare användning när du distribuerar den virtuella datorn. Resurs-ID-formatet är:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

När du kopierar och klistrar in ID:et kan det vara uppdelat i flera rader. Sammanfoga linjerna och trimma de extra utrymmena.

Om du vill verifiera distributionen kör du följande PowerShell-kommando i samma skalfönster för att hämta hemligheten i klartext. Kommandot fungerar bara i samma skalsession, eftersom det använder variabeln *$keyVaultName*, som definieras i föregående PowerShell-skript.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu har du förberett ett nyckelvalv och en hemlighet. I följande avsnitt visas hur du anpassar en befintlig mall för att hämta hemligheten under distributionen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure Quickstart Templates är en lagringsplats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Mallen som används i den här självstudien kallas [Distribuera en enkel Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Välj **Öppna** > **fil**i Visual Studio-kod .

1. I rutan **Filnamn** klistrar du in följande webbadress: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen. Scenariot är detsamma som det som används i [Självstudiekurs: Skapa ARM-mallar med beroende resurser](./template-tutorial-create-templates-with-dependent-resources.md).
   Mallen definierar fem resurser:

   * `Microsoft.Storage/storageAccounts`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Det är bra att ha en grundläggande förståelse för mallen innan du anpassar den.

1. Välj **Spara fil** > **som**och spara sedan en kopia av filen på den lokala datorn med namnet *azuredeploy.json*.

1. Upprepa steg 1-3 för att öppna följande URL och spara sedan filen som *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Redigera parameterfilen

Du behöver inte göra några ändringar i mallfilen.

1. Öppna *azuredeploy.parameters.json* i Visual Studio Code om den inte redan är öppen.
1. Uppdatera `adminPassword` parametern till:

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
    > Ersätt värdet för **id** med resurs-ID:t för nyckelvalvet som du skapade i föregående procedur.

    ![Integrera parameterfilen för nyckelvalv och Resource Manager-mall för distribution av virtuella datorer](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Uppdatera följande värden:

    * **adminUsername**: Namnet på administratörskontot för den virtuella datorn.
    * **dnsLabelPrefix**: Namnge värdet dnsLabelPrefix.

    Exempel på namn finns i föregående bild.

1. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [Distribuera mallen](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Ladda upp både *azuredeploy.json* och *azuredeploy.parameters.json* till Cloud Shell och använd sedan följande PowerShell-skript för att distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

Write-Host "Press [ENTER] to continue ..."
```

När du distribuerar mallen använder du samma resursgrupp som du använde i nyckelvalvet. Den här metoden gör det enklare för dig att rensa resurserna, eftersom du bara behöver ta bort en resursgrupp i stället för två.

## <a name="validate-the-deployment"></a>Verifiera distributionen

När du har distribuerat den virtuella datorn testar du inloggningsuppgifterna med hjälp av lösenordet som lagras i nyckelvalvet.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** > **\<*YourResourceGroupName*>** > **simpleWinVM**.
1. Välj **anslut** högst upp.
1. Välj **Hämta RDP-fil**och följ sedan instruktionerna för att logga in på den virtuella datorn med hjälp av lösenordet som lagras i nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dina Azure-resurser rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien hämtade du en hemlighet från ditt Azure-nyckelvalv. Du använde sedan hemligheten i malldistributionen. Information om hur du använder tillägg för virtuell dator för att utföra distributionsuppgifter finns i:

> [!div class="nextstepaction"]
> [Distribuera tillägg för virtuella datorer](./template-tutorial-deploy-vm-extensions.md)

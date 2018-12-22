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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3a84f9ed35bac7f56d4a6aa2af94d1c28e335b74
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093207"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager

Lär dig hur du hämtar hemliga värden från Azure Key Vault och skickar de hemliga värdena som parametrar under Resource Manager-distributionen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Mer information finns i [Använd Azure Key Vault för att skicka säkra parametervärden under distributionen](./resource-manager-keyvault-parameter.md)

I självstudien [Ange resursdistributionsordning](./resource-manager-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. I den här självstudien anpassar du mallen för att hämta den virtuella datorns administratörslösenord från Azure Key Vault.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbered nyckelvalvet
> * Öppna en snabbstartsmall
> * Redigera parameterfilen
> * Distribuera mallen
> * Verifiera distributionen
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med [verktygstillägget för Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-the-key-vault"></a>Förbered nyckelvalvet

I det här avsnittet använder du en Resource Manager-mall för att skapa ett nyckelvalv och en hemlighet. Mallen gör följande:

* Skapa ett nyckelvalv med egenskapen `enabledForTemplateDeployment` aktiverad. Den här egenskapen måste vara sann innan malldistributionsprocessen kan komma åt hemligheterna som definierats i det här nyckelvalvet.
* Lägger till en hemlighet i nyckelvalvet.  Hemligheten lagrar administratörslösenord för den virtuella datorn.

Om du (som användaren som distribuerar mallen för den virtuella datorn) inte är ägare av eller medarbetare i nyckelvalvet, måste ägaren eller medarbetaren för nyckelvalvet ge dig åtkomst till Microsoft.KeyVault/vaults/deploy/action för nyckelvalvet. Mer information finns i [Använd Azure Key Vault för att skicka säkra parametervärden under distributionen](./resource-manager-keyvault-parameter.md)

Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID) och genererar även administratörslösenordet. Om du vill förhindra spray-attacker med lösenord rekommenderar vi att du använder genererade lösenord.

1. Kör följande kommando för Azure PowerShell eller Azure CLI.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Anteckna både objekt-ID:t och det genererade lösenordet. Du behöver dem senare.
3. Kontrollera att det genererade lösenordet uppfyller den virtuella datorns krav för lösenord. Varje Azure-tjänst har specifika lösenordskrav. Lösenordskraven för den virtuella datorn finns i [Vilka är lösenordskraven när du skapar en virtuell dator?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Så här skapar du ett nyckelvalv:

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.  Välj inte **Köp** när du har angett värdena.

    ![Resource Manager-mall för distributionsportal för Key Vault-integrering](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp**: tilldela ett unikt namn. Anteckna det här namnet, du kan använda samma resursgrupp för att distribuera den virtuella datorn i nästa session. Om du placerar både nyckelvalvet och den virtuella datorn i samma resursgrupp blir det enklare att rensa resursen i slutet av självstudien.
    * **Plats**: välj en plats.  Standardplatsen är **USA, centrala**.
    * **Nyckelvalvsnamn**: tilldela ett unikt namn. 
    * **Klient-ID**: mallfunktionen hämtar ditt klient-ID automatiskt.  Ändra inte standardvärdet
    * **Användar-ID för AD**: ange ditt användarobjekts-ID för Azure AD som du hämtade från föregående procedur.
    * **Hemligt namn**: Standardnamnet är **vmAdminPassword**. Om du ändrar det hemliga namnet måste du uppdatera det hemliga namnet när du distribuerar den virtuella datorn.
    * **Hemligt värde**: Ange din hemlighet.  Hemligheten är det lösenord som används för att logga in på den virtuella datorn. Vi rekommenderar att du använder det genererade lösenordet som du skapade i föregående procedur.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Redigera parametrar** högst upp för att ta en titt på mallen.
4. Bläddra till rad 28 i JSON-mallfilen. Det här är resursdefinitionen för Key Vault.
5. Bläddra till rad 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` är en Key Vault-egenskap. Den här egenskapen måste vara sann innan du kan hämta hemligheterna från detta nyckelvalv under distributionen.
6. Bläddra till rad 89. Det här är hemlighetsdefinitionen för Key Vault.
7. Välj **Ignorera** längst ned på sidan. Du inte har gjort några ändringar.
8. Kontrollera att du har angett alla värden som visas i föregående skärmbild och klicka sedan på **Köp** längst ned på sidan.
9. Välj klockikonen (meddelande) högst upp på sidan för att öppna fönstret **Meddelanden**. Vänta tills resursen har distribuerats.
10. Välj **Gå till resursgrupp** i fönstret **Meddelanden**. 
11. Välj nyckelvalvsnamnet för att öppna det.
12. Välj **Åtkomstprinciper** i det vänstra fönstret. Ditt namn (Active Directory) ska finnas i listan, annars har du inte behörighet att öppna nyckelvalvet.
13. Välj **Click to show advanced access policies** (Klicka för att visa avancerade åtkomstprinciper). Kontrollera att **Enable access to Azure Resource Manager for template deployment** (Aktivera åtkomst till Azure Resource Manager för malldistribution) har valts. Det här är ett annat villkor för att Key Vault-integreringen ska fungera.

    ![Åtkomstprinciper för Resource Manager-mall för Key Vault-integrering](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. Välj **Egenskaper** i det vänstra fönstret.
15. Skapa en kopia av **Resurs-ID**. Du behöver detta ID när du distribuerar den virtuella datorn.  Resurs-ID-formatet är:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

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
    Ersätt **ID:t** med resurs-ID:t för nyckelvalvet du skapade i föregående procedur.  

    ![integrera key vault och parameterfilen för Resource Manager-malldistribution av virtuell dator](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Ge värden till:

    * **adminUsername**: namnge administratörskontot för den virtuella datorn.
    * **dnsLabelPrefix**: namnge prefixet dnsLabelPrefix.
4. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Följ instruktionerna i [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) för att distribuera mallen. Du behöver ladda upp både **azuredeploy.json** och **azuredeploy.parameters.json** till Cloudshell och sedan använda följande PowerShell-skript för att distribuera mallen:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

När du distribuerar mallen använder du samma resursgrupp som Key Vault. Det gör enklare när du rensar bland resurserna. Du behöver bara att ta bort en resursgrupp istället för två.

## <a name="valid-the-deployment"></a>Validera distributionen

När du har distribuerat den virtuella datorn kan du testa inloggningen med lösenordet som lagrats i Key Vault.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **Resource grouips**/**YourResourceGroupName>**/**simpleWinVM**
3. Välj **anslut** högst upp.
4. Välj **Ladda ned RDP-fil** och följ sedan anvisningarna för att logga in på den virtuella datorn med lösenordet som lagrats i nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien hämtade du en hemlighet från Azure Key Vault och använde hemligheten i din malldistribution.  Om du vill lära dig mer om hur du skapar länkade mallar kan du se:

> [!div class="nextstepaction"]
> [Skapa länkade mallar](./resource-manager-tutorial-create-linked-templates.md)

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
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114320"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Självstudie: Skapa Azure Resource Manager-mallar med beroende resurser

Lär dig hur du skapar en Azure Resource Manager-mall för att distribuera flera resurser.  När du har skapat mallen distribuerar du mallen med hjälp av Cloud Shell från Azure-portalen.

I den här självstudien skapar du ett lagringskonto, en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. Vissa resurser kan inte distribueras förrän en annan resurs finns. Till exempel kan du inte skapa den virtuella datorn förrän dess lagringskonto och nätverksgränssnitt finns. Du kan definiera den här relationen genom att göra en resurs beroende av de andra resurserna. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Mer information finns på sidan om att [definiera ordningen för distribution av resurser i Azure Resource Manager-mallar](./resource-group-define-dependencies.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbered nyckelvalvet
> * Öppna en snabbstartsmall
> * Utforska mallen
> * Redigera parameterfilen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med verktygstillägget för Resource Manager.  Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>Förbereda nyckelvalv

För att förhindra spray-attacker med lösenord rekommenderar vi att använda ett automatiskt genererat lösenord för administratörskontot för den virtuella datorn och använda nyckelvalv för att lagra lösenordet. Följande procedur skapar ett nyckelvalv och en hemlighet för att lagra lösenordet. Även de behörigheter som behövs för malldistribution konfigureras för åtkomst till hemligheten som lagras i nyckelvalvet. Ytterligare åtkomstprinciper behövs om nyckelvalvet finns under en annan Azure-prenumeration. Mer information finns i [Använd Azure Key Vault för att skicka säkra parametervärden under distributionen](./resource-manager-keyvault-parameter.md).

1. Logga in på [Azure Cloud Shell](https://shell.azure.com).
2. Byt till den miljö du föredrar, antingen **PowerShell** eller **Bash** högst upp till vänster.
3. Kör följande kommando för Azure PowerShell eller Azure CLI.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Anteckna utdatavärdena. Du behöver dem senare under kursen

> [!NOTE]
> Varje Azure-tjänst har specifika lösenordskrav. Du kan till exempel hitta kraven för den virtuella Azure-datorn i Vad är lösenordskraven när du skapar en virtuell dator?

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.
5. Upprepa steg 1–4 för att öppna **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** följande URL och spara sedan filen som **azuredeploy.parameters.json**.

## <a name="explore-the-template"></a>Utforska mallen

När du utforskar mallen i det här avsnittet kan du försöka besvara följande frågor:

- Hur många Azure-resurser är definierade i den här mallen?
- En av resurserna är ett Azure-lagringskonto.  Ser definitionen ut som den som används i den senaste självstudien?
- Kan du hitta mallreferenserna för de resurser som är definierade i mallen?
- Kan du hitta resursernas beroenden?

1. Från Visual Studio Code döljer du elementen tills du bara ser elementen på den första och andra nivån i **resurser**:

    ![Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Det finns fem resurser som definieras i mallen.
2. Expandera den första resursen. Det är ett lagringskonto. Definitionen ska vara identisk med den som används i början av den senaste självstudien.

    ![Lagringsdefinition för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expandera den andra resursen. Resurstypen är **Microsoft.Network/publicIPAddresses**. Du hittar mallreferensen genom att bläddra till [mallreferens](https://docs.microsoft.com/azure/templates/) och ange **offentlig ip-adress** eller **offentliga ip-adresser** i fältet **Filtrera efter rubrik**. Jämför resursdefinitionen med mallreferensen.

    ![Definition av offentlig IP-adress för Azure Resource Manager-mallar i Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Upprepa det senaste steget för att leta rätt på mallreferenserna för de övriga resurser som är definierade i mallen.  Jämför resursdefinitionerna med mallreferenserna.
5. Expandera den fjärde resursen:

    ![Azure Resource Manager-mallar i Visual Studio Code – dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Med elementet dependsOn kan du definiera en resurs som beroende på en eller flera resurser. I det här exemplet är den här resursen ett networkInterface.  Den beror på två resurser:

    * publicIPAddress
    * virtualNetwork

6. Expandera den femte resursen. Den här resursen är en virtuell dator. Den beror på två resurser:

    * storageAccount
    * networkInterface

Följande diagram illustrerar resurserna och beroendeinformation för den här mallen:

![Azure Resource Manager-mallar i Visual Studio Code – beroendediagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Genom att ange beroendena distribuerar Resource Manager effektivt lösningen. Den distribuerar lagringskontot, en offentlig IP-adress och ett virtuellt nätverk parallellt eftersom de inte har några beroenden. När den offentliga IP-adressen och det virtuella nätverket har distribuerats skapas ett nätverksgränssnitt. När alla andra resurser har distribuerats så distribuerar Resource Manager den virtuella datorn.

## <a name="edit-the-parameters-file"></a>Redigera parameterfilen

Du behöver inte göra några ändringar i mallfilen. Men du måste ändra parameterfilen för att hämta administratörslösenordet från nyckelvalvet.

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
    Ersätt **ID:t** med resurs-ID:t för nyckelvalvet du skapade i föregående procedur. Det är en av utdata. 

    ![integrera key vault och parameterfilen för Resource Manager-malldistribution av virtuell dator](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Ge värden till:

    - **adminUsername**: namnge administratörskontot för den virtuella datorn.
    - **dnsLabelPrefix**: namnge prefixet dnsLablePrefix.
4. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

Det finns många metoder för att distribuera mallar.  I den här självstudien använder du Cloud Shell från Azure-portalen.

1. Logga in på [Cloud Shell](https://shell.azure.com). Du kan även logga in på [Azure-portalen](https://portal.azure.com) och välja **Cloud Shell** högst upp till höger enligt följande bild:

    ![Azure portal Cloud shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Välj **PowerShell** högst upp till vänster i Cloud Shell och välj sedan **Bekräfta**.  Du använder PowerShell i den här självstudien.
3. Välj **Ladda upp fil** från Cloud Shell:

    ![Azure portal Cloud shell upload file](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Välj de filer som du sparade tidigare i självstudien. Standardnamnet är **azuredeploy.json** och **azuredeploy.paraemters.json**.  Om du har filer med samma namn kommer de gamla filerna att skrivas över utan något meddelande.
5. Från Cloud Shell kör du följande kommando för att kontrollera att filen har laddats upp. 

    ```bash
    ls
    ```

    ![Azure portal Cloud shell list file](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Det filnamn som visas på skärmbilden är azuredeploy.json.

6. Från Cloud Shell kör du följande kommando för att verifiera innehållet i JSON-filen:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Från Cloud Shell kör du följande PowerShell-kommandon. Exempelskriptet använder samma resursgrupp som skapats för nyckelvalvet. Om du använder samma resursgrupp blir det enklare att rensa resurserna.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Kör följande PowerShell-kommando för att visa den nyligen skapade virtuella datorn:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Namnet på den virtuella datorn är hårdkodat som **SimpleWinVM** i mallen.

9. Logga in på den virtuella datorn för att testa administratörens autentiseringsuppgifter. 

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklar och distribuerar du en mall för att skapa en virtuell dator, ett virtuellt nätverk och de beroende resurserna. Information om hur du lär dig att distribuera Azure-resurser baserat på villkor finns i:


> [!div class="nextstepaction"]
> [Använda villkor](./resource-manager-tutorial-use-conditions.md)


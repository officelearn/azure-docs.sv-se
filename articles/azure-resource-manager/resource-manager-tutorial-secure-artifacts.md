---
title: Säkra artefakter i mallar
description: Lär dig hur du skyddar artefakter som används i Azure Resource Manager-mallar.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a9d209e843d8e9a1735a3c6907b00d85be6580b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971747"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Självstudie: säkra artefakter i Azure Resource Manager mallar distributioner

Lär dig hur du skyddar artefakter som används i Azure Resource Manager-mallar med hjälp av Azure Storage-konto med signaturer för delad åtkomst (SAS). Distributionsartefakter är vilka filer som helst, utöver den huvudsakliga mallfilen, som behövs för att slutföra en distribution. I [själv studie kursen: importera SQL BACPAC-filer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)skapar huvudmallen en Azure SQL Database. den anropar också en BACPAC-fil för att skapa tabeller och infoga data. BACPAC-filen är en artefakt som lagras i ett Azure Storage-konto. Lagrings konto nyckeln användes för att få åtkomst till artefakten. I den här självstudien använder du SAS för att bevilja begränsad åtkomst till BACPAC-filen i ditt eget Azure Storage-konto. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Information om hur du skyddar länkade mallar finns i [Självstudier: Skapa länkade Azure Resource Manager mallar](./resource-manager-tutorial-create-linked-templates.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en BACPAC-fil
> * Öppna en befintlig mall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).
* Granska [självstudie: importera SQL BACPAC-filer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Den mall som används i den här självstudien är den som utvecklas i självstudien. En nedladdningslänk för den färdiga mallen finns i den här artikeln.
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

I det här avsnittet förbereder du BACPAC-filen så att filen är tillgänglig på ett säkert sätt när du distribuerar Resource Manager-mallen. Det finns fem procedurer i det här avsnittet:

* Ladda ned BACPAC-filen.
* skapa ett Azure Storage-konto
* Skapa en blobcontainer för ett lagringskonto.
* Ladda upp BACPAC-filen till containern.
* Hämta SAS-token för BACPAC-filen.

1. Välj **prova** att öppna Cloud Shell och klistra sedan in följande PowerShell-skript i Shell-fönstret.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Skriv ner BACPAC-filens URL och SAS-token. Du behöver dessa värden när du distribuerar mallen.

## <a name="open-an-existing-template"></a>Öppna en befintlig mall

I den här sessionen ändrar du mallen som du skapade i [själv studie kursen: importera SQL BACPAC-filer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) för att anropa BACPAC-filen med en SAS-token.  Mallen som utvecklas i själv studie kursen för SQL-tillägg delas i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

3. Välj **Öppna** för att öppna filen.

    Det finns fyra resurser definierade i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Det är bra att få lite grundläggande förståelse av mallen innan den anpassas.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

1. Ersätt storageAccountKey-parameter definitionen med följande parameter definition:

    ```json
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the SAS token required to access the artifact location."
      }
    },
    ```

    ![Självstudie om Resource Manager, skydda artefaktparametrar](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

2. Uppdatera värdet för följande tre element i SQL-tilläggs resursen:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Den färdiga mallen ser ut så här:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).
För värdena för _artifactsLocation, _artifactsLocationSasToken och bacpacFileName, se [förbereda en BACPAC-fil](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verifiera distributionen

Välj SQL-databasen från den nyligen distribuerade resursgruppen i portalen. Välj **Frågeredigerare (förhandsversion)** och ange administratörsautentiseringsuppgifterna. Du bör se två tabeller som har importerats till databasen:

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en SQL Server och en SQL-databas samt importerade en BACPAC-fil med hjälp av SAS-token. Information om hur du skapar en Azure-pipeline för att kontinuerligt utveckla och distribuera Resource Manager-mallar finns i

> [!div class="nextstepaction"]
> [Kontinuerlig integrering med Azure pipeline](./resource-manager-tutorial-use-azure-pipelines.md)

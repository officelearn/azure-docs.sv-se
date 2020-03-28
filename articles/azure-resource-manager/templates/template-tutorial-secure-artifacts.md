---
title: Säkra artefakter i mallar
description: Lär dig hur du skyddar artefakter som används i Azure Resource Manager-mallar.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239219"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Självstudiekurs: Säkra artefakter i ARM-malldistributioner

Lär dig hur du skyddar artefakterna som används i dina ARM-mallar (Azure Resource Manager) med hjälp av ett Azure Storage-konto med SAS (Shared Access Signatures). Distributionsartefakter är vilka filer som helst, utöver den huvudsakliga mallfilen, som behövs för att slutföra en distribution. I [självstudiekurs: Importera SQL BACPAC-filer med ARM-mallar](./template-tutorial-deploy-sql-extensions-bacpac.md)skapar huvudmallen en Azure SQL Database-instans. Det anropar också en BACPAC-fil för att skapa tabeller och infoga data. BACPAC-filen är en artefakt och lagras i ett Azure Storage-konto. En lagringskontonyckel användes för att komma åt artefakten.

I den här självstudien använder du SAS för att bevilja begränsad åtkomst till BACPAC-filen i ditt eget Azure Storage-konto. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Mer information om hur du skyddar en länkad mall finns i [Självstudiekurs: Skapa länkade ARM-mallar](./template-tutorial-create-linked-templates.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbered en BACPAC-fil.
> * Öppna en befintlig mall.
> * Redigera mallen.
> * Distribuera mallen.
> * Verifiera distributionen.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med ett resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](./use-vs-code-to-create-template.md).
* Granska [självstudiekurs: Importera SQL BACPAC-filer med ARM-mallar](./template-tutorial-deploy-sql-extensions-bacpac.md). Den mall som används i den här självstudien är den som utvecklas i självstudien. En nedladdningslänk för den färdiga mallen finns i den här artikeln.
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel som du kan använda för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudiekurs: Integrera Azure Key Vault i ARM-malldistribution](./template-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

I det här avsnittet förbereder du BACPAC-filen så att filen är tillgänglig på ett säkert sätt när du distribuerar ARM-mallen. Det finns fem procedurer i det här avsnittet:

* Ladda ned BACPAC-filen.
* skapa ett Azure Storage-konto
* Skapa en lagringskontoblolobbehållare.
* Ladda upp BACPAC-filen till containern.
* Hämta SAS-token för BACPAC-filen.

1. Välj **Prova det** för att öppna molnskalet. Klistra sedan in följande PowerShell-skript i skalfönstret.

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

1. Skriv ner BACPAC-fil-URL:en och SAS-token. Du behöver dessa värden när du distribuerar mallen.

## <a name="open-an-existing-template"></a>Öppna en befintlig mall

I den här sessionen ändrar du mallen som du skapade i [Självstudiekurs: Importera SQL BACPAC-filer med ARM-mallar](./template-tutorial-deploy-sql-extensions-bacpac.md) för att anropa BACPAC-filen med en SAS-token. Mallen som utvecklats i självstudien för SQL-tillägg delas i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Välj **Öppna** > **fil**i Visual Studio-kod .
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Välj **Öppna** för att öppna filen.

    Det finns fyra resurser definierade i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Det är bra om du har grundläggande förståelse av mallen innan du anpassar den.
1. Välj **Spara fil** > **som** om du vill spara en kopia av filen på den lokala datorn med namnet *azuredeploy.json*.

## <a name="edit-the-template"></a>Redigera mallen

1. Ersätt parameterdefinitionen storageAccountKey med följande parameterdefinition:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Självstudie om Resource Manager, skydda artefaktparametrar](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Uppdatera värdet för följande tre element i SQL-tilläggsresursen:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Den färdiga mallen ser ut så här:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se avsnittet [Distribuera mallavsnittet](./template-tutorial-create-multiple-instances.md#deploy-the-template) för distributionsproceduren. Använd i stället följande PowerShell-distributionsskript.

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
För värdena för _artifactsLocation, _artifactsLocationSasToken och bacpacFileName finns i [Förbereda en BACPAC-fil](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verifiera distributionen

Välj SQL-databasen från den nyligen distribuerade resursgruppen i portalen. Välj **Frågeredigerare (förhandsversion)** och ange administratörsautentiseringsuppgifterna. Två tabeller visas som importeras till databasen.

![Frågeredigeraren (förhandsgranskning)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. I Azure-portalen väljer du **Resursgrupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn. Totalt visas sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en SQL-server och en SQL-databas och importerade en BACPAC-fil med hjälp av en SAS-token. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i

> [!div class="nextstepaction"]
> [Använda säkra distributionsmetoder](./deployment-manager-tutorial.md)

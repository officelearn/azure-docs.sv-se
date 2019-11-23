---
title: Import SQL BACPAC files with templates
description: Lär dig hur du använder SQL Database-tillägget för att importera SQL BACPAC-filer med Azure Resource Manager-mallar.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422154"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar

Lär dig hur du använder Azure SQL Database-tillägget för att importera en BACPAC-fil med Azure Resource Manager-mallar. Deployment artifacts are any files, in addition to the main template files that are needed to complete a deployment. BACPAC-filen är en artefakt. I den här självstudien skapar du en mall för att distribuera en Azure SQL-server och en SQL-databas och importera en BACPAC-fil. Information om hur du distribuerar tillägg för virtuell Azure-dator med hjälp av Azure Resource Manager-mallar finns i [# Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-vm-extensions.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en BACPAC-fil
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

A BACPAC file is shared in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Om du vill skapa en egen läser du [Exportera en Azure SQL-databas till en BACPAC-fil](../sql-database/sql-database-export.md). Om du väljer att publicera filen till din egen plats måste du uppdatera mallen senare under självstudien.

The BACPAC file must be stored in an Azure Storage account before it can be imported using Resource Manager template.

1. Open the [Cloud shell](https://shell.azure.com).
1. Select **Upload/Download files**, and then select **Upload**.
1. Specify the following URL and then select **Open**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Copy and paste the following PowerShell script into the shell window.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Write down storage account key and the BACPAC file URL. You need these values when you deploy the template.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

The template used in this tutorial is stored in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.

    Det finns tre resurser som definieras i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Det är bra att få lite grundläggande förståelse av mallen innan den anpassas.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

1. Add two more parameters at the end of the **parameters** section to set the storage account key and the BACPAC URL:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Add a comma after **adminPassword**. To format the JSON file from VS Code, press **[SHIFT]+[ALT]+F**.

    See [Prepare a BACPAC file](#prepare-a-bacpac-file) about getting these two values.

1. Lägg till två ytterligare resurser i mallen.

    * To allow the SQL database extension to import BACPAC files, you need to allow traffic from Azure services. Add the following firewall rule definition under the SQL server definition:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        Mallen bör se ut så här:

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Lägg till en resurs för SQL Database-tillägget till databasdefinitionen med följande JSON:

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Mallen bör se ut så här:

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Information om resursdefinitionen finns i [tilläggsreferensen för SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Här följer några viktiga element:

        * **dependsOn**: tilläggsresursen måste skapas efter att SQL-databasen har skapats.
        * **storageKeyType**: Specify the type of the storage key to use. Värdet kan vara antingen `StorageAccessKey` eller `SharedAccessKey`. Use `StorageAccessKey` in this tutorial.
        * **storageKey**: Specify the key for the storage account where the BACPAC file is stored. If storage key type is SharedAccessKey, it must be preceded with a "?"
        * **storageUri**: Specify the URL of the BACPAC file stored in a storage account.
        * **administratorLoginPassword**: Lösenordet för SQL-administratören. Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Consider using the same project name as you used when you prepared the bacpac file, so that all the resources are stored within the same resource group.  It is easier for managing resource, such as cleaning up the resources. If you use the same project name, you can either remove the **New-AzResourceGroup** command from the script, or answer y or n when you are asked whether you want to update the existing resource group.

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

## <a name="verify-the-deployment"></a>Verifiera distributionen

To access the SQL server from your client computer, you need to add an additional firewall rule. For more information, see [Create and manage IP firewall rules](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Välj SQL-databasen från den nyligen distribuerade resursgruppen i portalen. Välj **Frågeredigerare (förhandsversion)** och ange administratörsautentiseringsuppgifterna. Du bör se två tabeller som har importerats till databasen:

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en SQL Server och en SQL-databas samt importerat en BACPAC-fil. BACPAC-filen lagras i ett Azure Storage-konto. Vem som helst med URL:en kan komma åt filen. Läs hur du skyddar BACPAC-filen (artefakten) i

> [!div class="nextstepaction"]
> [Skydda artefakterna](./resource-manager-tutorial-secure-artifacts.md)

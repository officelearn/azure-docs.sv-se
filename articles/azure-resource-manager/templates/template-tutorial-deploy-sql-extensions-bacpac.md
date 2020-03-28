---
title: Importera SQL BACPAC-filer med mallar
description: Lär dig hur du använder Azure SQL Database-tillägg för att importera SQL BACPAC-filer med Azure Resource Manager-mallar.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8e65ebbfa0971bf2156165b55ca18eee3cc74bc9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239281"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Självstudiekurs: Importera SQL BACPAC-filer med ARM-mallar

Lär dig hur du använder Azure SQL Database-tillägg för att importera en BACPAC-fil med Azure Resource Manager -mallar (ARM). Distributionsartefakter är alla filer, utöver de viktigaste mallfilerna, som behövs för att slutföra en distribution. BACPAC-filen är en artefakt. 

I den här självstudien skapar du en mall för att distribuera en Azure SQL-server och en SQL-databas och importera en BACPAC-fil. Information om hur du distribuerar Azure-tillägg för virtuella datorer med hjälp av ARM-mallar finns i [Självstudiekurs: Distribuera tillägg till virtuella datorer med ARM-mallar](./template-tutorial-deploy-vm-extensions.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbered en BACPAC-fil.
> * Öppna en snabbstartsmall.
> * Redigera mallen.
> * Distribuera mallen.
> * Verifiera distributionen.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med verktygstillägget för Resource Manager. Se [Använda Visual Studio-kod för att skapa ARM-mallar](./use-vs-code-to-create-template.md).
* Om du vill öka säkerheten använder du ett genererat lösenord för Azure SQL Server-administratörskontot. Här är ett exempel som du kan använda för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudiekurs: Integrera Azure Key Vault i ARM-malldistribution](./template-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

En BACPAC-fil delas i [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Om du vill skapa en egen läser du [Exportera en Azure SQL-databas till en BACPAC-fil](../../sql-database/sql-database-export.md). Om du väljer att publicera filen till din egen plats måste du uppdatera mallen senare under självstudien.

BACPAC-filen måste lagras i ett Azure Storage-konto innan den kan importeras med hjälp av en ARM-mall. Följande PowerShell-skript förbereder BACPAC-filen med följande steg:

* Ladda ned BACPAC-filen.
* skapa ett Azure Storage-konto
* Skapa en lagringskontoblolobbehållare.
* Ladda upp BACPAC-filen till containern.
* Visa lagringskontonyckeln och blob-URL:en.

1. Välj **Prova det** för att öppna molnskalet. Klistra sedan in följande PowerShell-skript i skalfönstret.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Skriv ned lagringskontonyckeln och BACPAC-fil-URL:en. Du behöver dessa värden när du distribuerar mallen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Mallen som används i den här självstudien lagras i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Välj **Öppna** > **fil**i Visual Studio-kod .
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.

    Det finns två resurser som definierats i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        Det är bra om du har grundläggande förståelse av mallen innan du anpassar den.
1. Välj **Spara fil** > **som** om du vill spara en kopia av filen på den lokala datorn med namnet *azuredeploy.json*.

## <a name="edit-the-template"></a>Redigera mallen

1. Lägg till ytterligare två parametrar i slutet av **parameteravsnittet** för att ange lagringskontonyckeln och BACPAC-URL:en.

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

    Lägg till ett kommatecken efter **adminPassword**. Om du vill formatera JSON-filen från Visual Studio-kod väljer du Skift+Alt+F.

    Om du vill hämta dessa två värden finns i [Förbereda en BACPAC-fil](#prepare-a-bacpac-file).

1. Lägg till två ytterligare resurser i mallen.

    * Om du vill att SQL Database-tillägget ska kunna importera BACPAC-filer måste du tillåta trafik från Azure-tjänster. Lägg till följande brandväggsregeldefinition under SQL-serverdefinitionen:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Mallen ser ut som:

        ![Mall med definition av brandväggsregel](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Lägg till en resurs för SQL Database-tillägget till databasdefinitionen med följande JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Mallen ser ut som:

        ![Mall med tillägget SQL Database](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Information om resursdefinitionen finns i [tilläggsreferensen för SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Här följer några viktiga element:

        * **dependsOn**: tilläggsresursen måste skapas efter att SQL-databasen har skapats.
        * **storageKeyType**: Ange vilken typ av lagringsnyckel som ska användas. Värdet kan vara antingen `StorageAccessKey` eller `SharedAccessKey`. Använd `StorageAccessKey` i den här självstudien.
        * **storageKey**: Ange nyckeln för lagringskontot där BACPAC-filen lagras. Om lagringsnyckeltypen är, `SharedAccessKey`måste den föregås med en "?".
        * **storageUri**: Ange URL:en för BACPAC-filen som lagras i ett lagringskonto.
        * **administratorLoginPassword**: Lösenordet för SQL-administratören. Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

Den färdiga mallen ser ut så här:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Överväg att använda samma projektnamn som du använde när du förberedde BACPAC-filen så att alla resurser lagras inom samma resursgrupp. På så sätt är det enklare att hantera resursaktiviteter, till exempel rensa resurser. Om du använder samma projektnamn kan `New-AzResourceGroup` du antingen ta bort kommandot från skriptet eller svara ja (y) eller nej (n) när du tillfrågas om du vill uppdatera den befintliga resursgruppen.

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

## <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill komma åt SQL-servern från klientdatorn måste du lägga till ytterligare en brandväggsregel. Mer information finns i [Skapa och hantera IP-brandväggsregler](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

I Azure-portalen väljer du SQL-databasen från den nyligen distribuerade resursgruppen. Välj **Frågeredigerare (förhandsversion)** och ange administratörsautentiseringsuppgifterna. Två tabeller visas som importeras till databasen.

![Frågeredigeraren (förhandsgranskning)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. I Azure-portalen väljer du **Resursgrupp** på den vänstra menyn.
1. Ange resursgruppens namn i fältet **Filtrera efter namn**.
1. Välj resursgruppens namn. Totalt visas sex resurser i resursgruppen.
1. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en SQL-server och en SQL-databas och importerade en BACPAC-fil. BACPAC-filen lagras i ett Azure Storage-konto. Vem som helst med URL:en kan komma åt filen. Mer information om hur du skyddar BACPAC-filen (artefakten) finns i:

> [!div class="nextstepaction"]
> [Skydda artefakterna](./template-tutorial-secure-artifacts.md)

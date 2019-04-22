---
title: Importera SQL BACPAC-filer med Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du använder SQL Database-tillägget för att importera SQL BACPAC-filer med Azure Resource Manager-mallar.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7339c975bf979907bd1f9bbb46546b58ef7ae6e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282363"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar

Lär dig hur du använder Azure SQL Database-tillägget för att importera en BACPAC-fil med Azure Resource Manager-mallar. Distributionsartefakter är vilka filer som helst, förutom den huvudsakliga mallfilen som behövs för att slutföra en distribution. BACPAC-filen är en artefakt. I den här självstudien skapar du en mall för att distribuera en Azure SQL-server och en SQL-databas och importera en BACPAC-fil. Information om hur du distribuerar tillägg för virtuell Azure-dator med hjälp av Azure Resource Manager-mallar finns i [# Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-vm-extensions.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en BACPAC-fil
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med verktygstillägget för Resource Manager. Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

En BACPAC-fil delas på ett [Azure Storage-konto med offentlig åtkomst](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac). Om du vill skapa en egen läser du [Exportera en Azure SQL-databas till en BACPAC-fil](../sql-database/sql-database-export.md). Om du väljer att publicera filen till din egen plats måste du uppdatera mallen senare under självstudien.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Den mall som användes i den här självstudien är lagrad i en [Azure Storage-konto](https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json). 

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.

    Det finns tre resurser som definieras i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

Lägg till två ytterligare resurser i mallen.

* Om du vill tillåta att SQL databastillägget importerar BACPAC-filer måste du tillåta åtkomst till Azure-tjänster. Lägg till följande JSON till SQL server-definitionen:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
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
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    Mallen bör se ut så här:

    ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Information om resursdefinitionen finns i [tilläggsreferensen för SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Här följer några viktiga element:

    * **dependsOn**: Tilläggsresursen måste skapas efter att SQL-databasen har skapats.
    * **storageKeyType**: Typ av lagringsnyckel som ska användas. Värdet kan vara antingen `StorageAccessKey` eller `SharedAccessKey`. Eftersom den angivna BACPAC-filen delas på ett Azure Storage-konto med offentlig åtkomst används ”SharedAccessKey” här.
    * **storageKey**: Lagringsnyckeln som ska användas. Om lagringsnyckeltypen är SharedAccessKey måste den föregås av ett ”?”.
    * **storageUri**: Lagrings-URI:n som ska användas. Om du väljer att inte använda BACPAC-filen som angavs måste du uppdatera värdena.
    * **administratorLoginPassword**: Lösenordet för SQL-administratören. Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

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

I den här självstudien har du distribuerat en SQL Server och en SQL-databas samt importerat en BACPAC-fil. BACPAC-filen lagras i ett Azure Storage-konto. Vem som helst med URL:en kan komma åt filen. Läs hur du skyddar BACPAC-filen (artefakten) i

> [!div class="nextstepaction"]
> [Skydda artefakterna](./resource-manager-tutorial-secure-artifacts.md)

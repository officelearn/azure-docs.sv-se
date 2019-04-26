---
title: Skydda artefakter i Azure Resource Manager-malldistributioner | Microsoft Docs
description: Lär dig hur du skyddar artefakter som används i Azure Resource Manager-mallar.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fd68edcc727ab08ed9d3ba765bbe795e88de5fc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391349"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Självstudie: Skydda artefakter i Azure Resource Manager-malldistributioner

Lär dig hur du skyddar artefakter som används i Azure Resource Manager-mallar med hjälp av Azure Storage-konto med signaturer för delad åtkomst (SAS). Distributionsartefakter är vilka filer som helst, utöver den huvudsakliga mallfilen, som behövs för att slutföra en distribution. Som exempel: I [Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) skapar huvudmallen en Azure SQL-databas. Den anropar även en BACPAC-fil för att skapa tabeller och infoga data. BACPAC-filen är en artefakt. Artefakten lagras i ett Azure Storage-konto med offentlig åtkomst. I den här självstudien använder du SAS för att bevilja begränsad åtkomst till BACPAC-filen i ditt eget Azure Storage-konto. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Information om hur du skyddar en länkad mall finns i [Självstudie: Skapa länkade Azure Resource Manager-mallar](./resource-manager-tutorial-create-linked-templates.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en BACPAC-fil
> * Öppna en befintlig mall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med verktygstillägget för Resource Manager. Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Granska [Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Den mall som används i den här självstudien är den som utvecklas i självstudien. En nedladdningslänk för den färdiga mallen finns i den här artikeln.
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

I det här avsnittet förbereder du BACPAC-filen så att filen är tillgänglig på ett säkert sätt när du distribuerar Resource Manager-mallen. Det finns fem procedurer i det här avsnittet:

* Ladda ned BACPAC-filen.
* skapa ett Azure Storage-konto
* Skapa en blobcontainer för ett lagringskonto.
* Ladda upp BACPAC-filen till containern.
* Hämta SAS-token för BACPAC-filen.

Om du vill automatisera de här stegen med hjälp av ett PowerShell-skript kan du använda skriptet från [Ladda upp den länkade mallen](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Ladda ned BACPAC-filen

Ladda ned [BACPAC-filen](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) och spara den på din lokala dator med samma namn, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

1. Välj följande bild för att öppna en Resource Manager-mall på Azure-portalen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Ange följande egenskaper:

    * **Prenumeration**: Välj din Azure-prenumeration.
    * **Resursgrupp**: Välj **Skapa ny** och ge den ett namn. En resursgrupp är en container för Azure-resurser med hanteringssyfte. I den här självstudien kan du använda samma resursgrupp för lagringskontot och Azure SQL-databasen. Anteckna den här resursgruppens namn. Du behöver det när du skapar Azure SQL-databasen senare i självstudierna.
    * **Plats**: Välj en region. Välj till exempel **USA, centrala**. 
    * **Typ av lagringskonto**: använd standardvärdet, vilket är **Standard_LRS**.
    * **Plats**: Använd standardvärdet, vilket är **[resursGrupp().plats]**. Det innebär att du använder resursgrupplatsen för lagringskontot.
    * **Jag godkänner villkoren ovan**: (valt)
3. Välj **Köp**.
4. Välj meddelandeikonen (klockikonen) i det övre högra hörnet av portalen för att se distributionsstatusen.

    ![Självstudie om Resource Manager, fönsterruta med portalmeddelanden](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. När lagringskontot har distribuerats väljer du **Gå till resursgrupp** från meddelandefönstret för att öppna resursgruppen.

### <a name="create-a-blob-container"></a>Skapa en blobcontainer

En blobcontainer krävs innan du kan ladda upp filer. 

1. Välj lagringskontot för att öppna det. Du bör endast se ett lagringskonto som visas i resursgruppen. Namnet på ditt lagringskonto skiljer sig från den som visas på följande skärmbild.

    ![Självstudie om Resource Manager, lagringskonto](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Välj panelen **Blobar**.

    ![Självstudie om Resource Manager, blobar](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Välj **+ Container** längst upp för att skapa en ny container.
4. Ange följande värden:

    * **Namn**: Ange **sqlbacpac**. 
    * **Offentlig åtkomstnivå**: Använd standardvärdet, **Privat (ingen anonym åtkomst)**.
5. Välj **OK**.
6. Välj **sqlbacpac** för att öppna den nyligen skapade containern.

### <a name="upload-the-bacpac-file-to-the-container"></a>Ladda upp BACPAC-filen till containern

1. Välj **Överför**.
2. Ange följande värden:

    * **Filer**: Följ instruktionerna för att välja den BACPAC-fil som du laddade ned tidigare. Standardnamnet är **SQLDatabaseExtension.bacpac**.
    * **Autentiseringstyp**: Välj **SAS**.  *SAS* är standardvärdet.
3. Välj **Överför**.  När filen har laddats upp bör filnamnet anges i containern.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Generera en SAS-token

1. Högerklicka på **SQLDatabaseExtension.bacpac** från containern och välj sedan **Generera SAS**.
2. Ange följande värden:

    * **Behörighet**: Använd standardvärdet, **Läs**.
    * **Datum/tid för start och utgång**: Med standardvärdet har du åtta timmar på dig att använda SAS-token. Om du behöver mer tid för att slutföra den här självstudien uppdaterar du **Förfallodatum**.
    * **Tillåtna IP-adresser**: Lämna det här fältet tomt.
    * **Tillåtna protokoll**: Använd standardvärdet: **HTTPS**.
    * **Signeringsnyckel**: Använd standardvärdet: **Nyckel 1**.
3. Välj **Generera blob-SAS-token och URL**.
4. Skapa en kopia av **Blob-SAS-URL**. I mitten av URL:en finns filnamnet **SQLDatabaseExtension.bacpac**.  Filnamnet delar in URL:en i tre delar:

   - **Plats för artefakt**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Se till att platsen slutar med ett ”/”.
   - **BACPAC-filnamn**: SQLDatabaseExtension.bacpac.
   - **SAS-token för artefaktplats**: Se till att token föregås av ett ”?”.

     Du behöver dessa tre värden i [Distribuera mallen](#deploy-the-template).

## <a name="open-an-existing-template"></a>Öppna en befintlig mall

I den här sessionen ändrar du den mall som du skapade i [Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) för att anropa BACPAC-filen med en SAS-token.  Den mall som utvecklades i självstudien om SQL-tillägget delas på [https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.

    Det finns fem resurser som definieras i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

Lägg till följande ytterligare parametrar:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Självstudie om Resource Manager, skydda artefaktparametrar](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Uppdatera värdet för följande två element:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).
Information om värdena för _artifactsLocation, _artifactsLocationSasToken och bacpacFileName finns i [Generera en SAS-token](#generate-a-sas-token).

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

I den här självstudien distribuerade du en SQL Server och en SQL-databas samt importerade en BACPAC-fil med hjälp av SAS-token. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i

> [!div class="nextstepaction"]
> [Använda Distributionshanteraren i Azure](./resource-manager-tutorial-deploy-vm-extensions.md)

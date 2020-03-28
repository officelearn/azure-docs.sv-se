---
title: Använda malldistributionsskript | Microsoft-dokument
description: Lär dig hur du använder distributionsskript i Azure Resource Manager-mallar.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 94b351ddb18ca596f47e8ef40cff8229c838d7bd
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239210"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Självstudiekurs: Använda distributionsskript för att skapa ett självsignerat certifikat (förhandsversion)

Lär dig hur du använder distributionsskript i ARM-mallar (Azure Resource Manage). Distributionsskript kan användas för att utföra anpassade steg som inte kan göras av ARM-mallar. Skapa till exempel ett självsignerat certifikat.  I den här självstudien skapar du en mall för `Microsoft.Resources/deploymentScripts` att distribuera ett Azure-nyckelvalv och använder sedan en resurs i samma mall för att skapa ett certifikat och sedan lägga till certifikatet i nyckelvalvet. Mer information om distributionsskript finns i [Använda distributionsskript i ARM-mallar](./deployment-script-template.md).

> [!IMPORTANT]
> Två distributionsskriptresurser, ett lagringskonto och en behållarinstans, skapas i samma resursgrupp för skriptkörning och felsökning. Dessa resurser tas vanligtvis bort av skripttjänsten när skriptkörningen hamnar i ett terminaltillstånd. Du debiteras för resurserna tills resurserna tas bort. Mer information finns i [Rensa distributionsskriptresurser](./deployment-script-template.md#clean-up-deployment-script-resources).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Felsöka det misslyckade skriptet
> * Rensa resurser

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* ** [Visual Studio-kod](https://code.visualstudio.com/) med tillägget Resource Manager Tools**. Se [Använda Visual Studio-kod för att skapa ARM-mallar](./use-vs-code-to-create-template.md).

* **En användartilldelad hanterad identitet med deltagarens roll på prenumerationsnivå**. Den här identiteten används för att köra distributionsskript. Information om hur du skapar en, se [Användartilldelade hanterade identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Du behöver identitets-ID:n när du distribuerar mallen. Identitetens format är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande PowerShell-skript för att hämta ID:n genom att ange resursgruppsnamnet och identitetsnamnet.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates är en lagringsplats för ARM-mallar.

Mallen som används i den här snabbstarten kallas [Skapa ett Azure Key Vault och ett hemligt](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mallen skapar ett nyckelvalv och lägger sedan till en hemlighet i nyckelvalvet.

1. Välj **Öppna**>**fil**i Visual Studio-kod .
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Välj **Spara fil**>**som** om du vill spara filen som **azuredeploy.json** på den lokala datorn.

## <a name="edit-the-template"></a>Redigera mallen

Gör följande ändringar i mallen:

### <a name="clean-up-the-template-optional"></a>Rensa mallen (valfritt)

Den ursprungliga mallen lägger till en hemlighet i nyckelvalvet.  Om du vill förenkla självstudien tar du bort följande resurs:

* **Microsoft.KeyVault/valv/hemligheter**

Ta bort följande två parameterdefinitioner:

* **secretName (hemligtnamn)**
* **hemlighetVärdera**

Om du väljer att inte ta bort dessa definitioner måste du ange parametervärdena under distributionen.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurera principer för nyckelvalvsåtkomst

Distributionsskriptet lägger till ett certifikat i nyckelvalvet. Konfigurera principer för nyckelvalvsåtkomst för att ge behörighet till den hanterade identiteten:

1. Lägg till en parameter för att hämta det hanterade identitets-ID:a:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Malltillägget Resource Manager för Visual Studio-kod kan inte formatera distributionsskript ännu. Använd inte [SKIFT]+[ALT]+F för att formatera distributionenScripts-resurser, till exempel följande.

1. Lägg till en parameter för att konfigurera nyckelvalvets åtkomstprinciper så att den hanterade identiteten kan lägga till certifikat i nyckelvalvet.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Uppdatera de befintliga principer för nyckelvalvsåtkomst till:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Det finns två principer definierade, en för den inloggade användaren och den andra är för den hanterade identiteten.  Den inloggade användaren behöver *list* bara listbehörighet för att verifiera distributionen.  För att förenkla självstudien tilldelas samma certifikat både den hanterade identiteten och de inloggade användarna.

### <a name="add-the-deployment-script"></a>Lägga till distributionsskriptet

1. Lägg till tre parametrar som används av distributionsskriptet.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Lägga till en deploymentScripts-resurs:

    > [!NOTE]
    > Eftersom infogade distributionsskript omges av dubbla citattecken måste strängarna i distributionsskripten omges av enkla citattecken i stället. Escape-tecknet för PowerShell är **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Resursen `deploymentScripts` är beroende av nyckelvalvresursen och rolltilldelningsresursen.  Den har dessa egenskaper:

    * **identitet:** Distributionsskript använder en användartilldelad hanterad identitet för att köra skripten.
    * **typ**: Ange typ av skript. För närvarande är endast PowerShell-skript stöd.
    * **forceUpdateTag**: Bestäm om distributionsskriptet ska köras även om skriptkällan inte har ändrats. Kan vara aktuell tidsstämpel eller ett GUID. Mer information finns i [Kör skript mer än en gång](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Anger den Azure PowerShell-modulversion som ska användas. Distributionsskript stöder för närvarande version 2.7.0, 2.8.0 och 3.0.0.
    * **timeout:** Ange den maximala tillåtna skriptkörningstiden som anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
    * **argument**: Ange parametervärdena. Värdena avgränsas med blanksteg.
    * **scriptContent**: Ange skriptinnehåll. Om du vill köra ett externt skript använder du **primaryScriptURI** i stället. Mer information finns i [Använda externt skript](./deployment-script-template.md#use-external-scripts).
        Att deklarera **$DeploymentScriptOutputs** krävs endast när skriptet testas på en lokal dator. Om du deklarerar variabeln kan skriptet köras på en lokal dator och i en deploymentScript-resurs utan att behöva göra ändringar. Värdet som tilldelats $DeploymentScriptOutputs är tillgängligt som utdata i distributionerna. Mer information finns i [Arbeta med utdata från PowerShell-distributionsskript](./deployment-script-template.md#work-with-outputs-from-powershell-script) eller [Arbeta med utdata från CLI-distributionsskript](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**: Ange inställningen för när distributionsskriptresurserna ska tas bort.  Standardvärdet är **Alltid**, vilket innebär att distributionsskriptresurserna tas bort trots terminaltillståndet (Lyckades, Misslyckades, avbröts). I den här självstudien används **OnSuccess** så att du får en chans att visa skriptkörningsresultaten.
    * **retentionInterval**: Ange det intervall för vilket tjänsten behåller skriptresurserna när den når ett terminaltillstånd. Resurser tas bort när den här varaktigheten upphör att gälla. Varaktigheten baseras på ISO 8601-mönstret. Den här självstudien använder P1D, vilket innebär en dag.  Den här egenskapen används när **cleanupPreference** är inställt **på OnExpiration**. Den här egenskapen är inte aktiverad för tillfället.

    Distributionsskriptet har tre parametrar: nyckelvalvsnamn, certifikatnamn och ämnesnamn.  Det skapar ett certifikat och lägger sedan till certifikatet i nyckelvalvet.

    **$DeploymentScriptOutputs** används för att lagra utdatavärde.  Mer information finns i [Arbeta med utdata från PowerShell-distributionsskript](./deployment-script-template.md#work-with-outputs-from-powershell-script) eller [Arbeta med utdata från CLI-distributionsskript](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Den ifyllda mallen hittar du [här](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Om du vill se felsökningsprocessen placerar du ett fel i koden genom att lägga till följande rad i distributionsskriptet:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Det korrekta kommandot är **Write-Output** i stället för **Write-Output1**.

1. Välj**Spara** **fil**>om du vill spara filen.

## <a name="deploy-the-template"></a>Distribuera mallen

Se avsnittet [Distribuera mallavsnittet](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) i snabbstarten för Visual Studio-kod för att öppna cloud-skalet och ladda upp mallfilen till skalet. Och kör sedan följande PowerShell-skript:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

Distributionsskripttjänsten måste skapa ytterligare distributionsskriptresurser för skriptkörning. Förberedelsen och rensningsprocessen kan ta upp till en minut att slutföra utöver den faktiska körningstiden för skriptet.

Distributionen misslyckades på grund av det ogiltiga kommandot, **Write-Output1** används i skriptet. Du skall få ett felmeddelande som säger:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Resultatet för körning av distributionsskript lagras i distributionsskriptresurserna för felsökningssyfte.

## <a name="debug-the-failed-script"></a>Felsöka det misslyckade skriptet

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen. Det är projektnamnet med **rg** bifogat. Du kommer att se ytterligare två resurser i resursgruppen. Dessa resurser kallas *distributionsskriptresurser*.

    ![Skriptresurser för resurshanterarens malldistribution](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Båda filerna har **azscripts** suffixet. Det ena är ett lagringskonto och det andra är en behållarinstans.

    Välj **Visa dolda typer** om du vill visa distributionScripts-resursen.

1. Välj lagringskontot med suffixet **azscripts.**
1. Markera panelen **Filresurser.** Du ska se en **azscripts** mapp.  Mappen innehåller körningsfilerna för distributionsskriptet.
1. Välj **azscripts**. Du ska se två mapparL **azscriptinput** och **azscriptoutput**.  Indatamappen innehåller en system-PowerShell-skriptfil och skriptfiler för användardistribution. Utdatamappen innehåller en **executionresult.json** och skriptutdatafilen. Felmeddelandet visas i **executionresult.json**. Utdatafilen är inte där eftersom körningen misslyckades.

Ta bort **raden Write-Output1** och distribuera om mallen.

När den andra distributionen har körts ska distributionsskriptresurserna tas bort av skripttjänsten, eftersom egenskapen **cleanupPreference** är inställd **på OnSuccess**.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder distributionsskript i ARM-mallar. Information om hur du lär dig att distribuera Azure-resurser baserat på villkor finns i:

> [!div class="nextstepaction"]
> [Använda villkor](./template-tutorial-use-conditions.md)

---
title: Distribuera resurser med PowerShell och mall
description: Använd Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153275"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Distribuera resurser med ARM-mallar och Azure PowerShell

Lär dig hur du använder Azure PowerShell med ARM-mallar (Azure Resource Manager) för att distribuera dina resurser till Azure. Mer information om begreppen att distribuera och hantera dina Azure-lösningar finns i [översikt över malldistribution](overview.md).

## <a name="deployment-scope"></a>Distributionsomfattning

Du kan rikta distributionen till en resursgrupp, prenumeration, hanteringsgrupp eller klient. I de flesta fall riktar du distributionen till en resursgrupp. Om du vill använda principer och rolltilldelningar i ett större omfång använder du prenumerations-, hanteringsgrupp- eller klientdistributioner. När du distribuerar till en prenumeration kan du skapa en resursgrupp och distribuera resurser till den.

Beroende på distributionens omfattning använder du olika kommandon.

Om du vill distribuera till en **resursgrupp**använder du [New-AzResourceGroupDeployment:](/powershell/module/az.resources/new-azresourcegroupdeployment)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Om du vill distribuera till en **prenumeration**använder du New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Mer information om distributioner på prenumerationsnivå finns i [Skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

Om du vill distribuera till en **hanteringsgrupp**använder du [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Mer information om distributioner på hanteringsgruppsnivå finns i [Skapa resurser på hanteringsgruppsnivå](deploy-to-management-group.md).

Om du vill distribuera till en **klient**använder du [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Mer information om distributioner på klientnivå finns i [Skapa resurser på klientnivå](deploy-to-tenant.md).

Exemplen i den här artikeln använder resursgruppsdistributioner.

## <a name="prerequisites"></a>Krav

Du behöver en mall för att distribuera. Om du inte redan har en, ladda ner och spara ett [exempel mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) från Azure Quickstart mallar repo. Det lokala filnamnet som används i den här artikeln är **c:\MyTemplates\azuredeploy.json**.

Om du inte använder Azure Cloud Shell för att distribuera mallar måste du installera Azure PowerShell och ansluta till Azure:

- **Installera Azure PowerShell-cmdlets på din lokala dator.** Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).
- **Anslut till Azure med [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Om du har flera Azure-prenumerationer kan du också behöva köra [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Mer information finns i [Använda flera Azure-prenumerationer](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Distribuera lokal mall

I följande exempel skapas en resursgrupp och en mall distribueras från den lokala datorn. Namnet på resursgruppen kan bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parentes. Det kan vara upp till 90 tecken. Det kan inte sluta i en period.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Det kan ta några minuter att slutföra distributionen.

## <a name="deploy-remote-template"></a>Distribuera fjärrmall

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i en källkontrolldatabas (till exempel GitHub). Du kan också lagra dem i ett Azure-lagringskonto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du parametern **TemplateUri.** Använd URI i exemplet för att distribuera exempelmallen från GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

I föregående exempel krävs en allmänt tillgänglig URI för mallen, som fungerar för de flesta scenarier eftersom mallen inte ska innehålla känsliga data. Om du behöver ange känsliga data (till exempel ett administratörslösenord) skickar du det värdet som en säker parameter. Men om du inte vill att mallen ska vara allmänt tillgänglig kan du skydda den genom att lagra den i en privat lagringsbehållare. Information om hur du distribuerar en mall som kräver en SAS-token (Shared Access Signature) finns [i Distribuera en privat mall med SAS-token](secure-template-with-sas-token.md). Information om hur du går igenom en självstudiekurs finns [i Självstudiekurs: Integrera Azure Key Vault i ARM-malldistributionen](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Distribuera från Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com) för att distribuera din mall. Om du vill distribuera en extern mall anger du URI-listan för mallen. Om du vill distribuera en lokal mall måste du först läsa in mallen i lagringskontot för ditt Cloud Shell. Om du vill ladda upp filer till skalet väljer du menyikonen **Ladda upp/ladda ned filer** i skalfönstret.

Om du vill öppna [https://shell.azure.com](https://shell.azure.com)molnskalet bläddrar du till eller väljer **Prova på** följande kodavsnitt:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Om du vill klistra in koden i skalet högerklickar du i skalet och väljer sedan **Klistra in**.

## <a name="pass-parameter-values"></a>Skicka parametervärden

Om du vill skicka parametervärden kan du använda antingen infogade parametrar eller en parameterfil.

### <a name="inline-parameters"></a>Infogade parametrar

Om du vill skicka infogade parametrar anger `New-AzResourceGroupDeployment` du namnen på parametern med kommandot. Om du till exempel vill skicka en sträng och matris till en mall använder du:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Det är praktiskt att hämta ett parametervärde från en fil när du behöver ange konfigurationsvärden. Du kan till exempel ange [molninitvärden för en virtuell Linux-dator](../../virtual-machines/linux/using-cloud-init.md).

Om du behöver skicka in en matris med objekt skapar du hash-tabeller i PowerShell och lägger till dem i en matris. Skicka matrisen som en parameter under distributionen.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen kan vara en lokal fil eller en extern fil med en tillgänglig URI.

Mer information om parameterfilen finns i [Skapa Resource Manager-parameterfil](parameter-files.md).

Om du vill skicka en lokal parameterfil använder du parametern **TemplateParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Om du vill skicka en extern parameterfil använder du parametern **TemplateParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testa malldistributioner

Om du vill testa mall- och parametervärden utan att distribuera några resurser använder du [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Om inga fel upptäcks avslutas kommandot utan svar. Om ett fel upptäcks returneras ett felmeddelande i kommandot. Om du till exempel skickar ett felaktigt värde för lagringskontot SKU returneras följande fel:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Om mallen har ett syntaxfel returnerar kommandot ett fel som anger att det inte kunde tolka mallen. Meddelandet anger radnumret och placeringen av tolkningsfelet.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel finns [i Återställning vid fel till en lyckad distribution](rollback-on-error.md).
- Information om hur resurser som finns i resursgruppen men som inte har definierats i mallen ska [hanteras](deployment-modes.md).
- Information om hur du definierar parametrar i mallen finns i [Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [Distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

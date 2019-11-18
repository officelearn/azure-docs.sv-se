---
title: Distribuera resurser med PowerShell och mall
description: Använd Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e726d1154fca1dbcce244783bf987bea6610cf98
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150717"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuera resurser med Resource Manager-mallar och Azure PowerShell

Lär dig hur du använder Azure PowerShell med Resource Manager-mallar för att distribuera dina resurser till Azure. Mer information om begreppen för att distribuera och hantera dina Azure-lösningar finns i [Azure Resource Manager översikt](resource-group-overview.md).

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta din distribution till antingen en Azure-prenumeration eller en resurs grupp i en prenumeration. I de flesta fall riktar du distribution till en resurs grupp. Använd prenumerations distributioner för att tillämpa principer och roll tilldelningar i prenumerationen. Du kan också använda prenumerations distributioner för att skapa en resurs grupp och distribuera resurser till den. Beroende på distributionens omfattning använder du olika kommandon.

Använd [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)för att distribuera till en **resurs grupp**:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Om du vill distribuera till en **prenumeration**använder du [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

För närvarande stöds endast distributioner av hanterings grupper via REST API. Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

I exemplen i den här artikeln används resurs grupps distributioner.

## <a name="prerequisites"></a>Krav

Du behöver en mall för att distribuera. Om du inte redan har ett kan du hämta och spara en exempel-mall från lagrings platsen för Azure snabb starts [mal len](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) . Det lokala fil namnet som används i den här artikeln är **c:\MyTemplates\azuredeploy.JSON**.

Om du inte använder Azure Cloud Shell för att distribuera mallar måste du installera Azure PowerShell och ansluta till Azure:

- **Installera Azure PowerShell-cmdlets på den lokala datorn.** Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).
- **Anslut till Azure med hjälp av [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Om du har flera Azure-prenumerationer kan du också behöva köra [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Mer information finns i [använda flera Azure-prenumerationer](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Distribuera lokal mall

I följande exempel skapas en resurs grupp och en mall distribueras från den lokala datorn. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte sluta med en punkt.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Det kan ta några minuter att slutföra distributionen.

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

I stället för att lagra Resource Manager-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i ett lager för käll kontroll (till exempel GitHub). Du kan också lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du parametern **TemplateUri** . Använd URI i exemplet för att distribuera exempel mal len från GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

I föregående exempel krävs en offentligt tillgänglig URI för mallen som fungerar i de flesta fall eftersom din mall inte ska innehålla känsliga data. Om du behöver ange känsliga data (som ett administratörs lösen ord) skickar du det värdet som en säker parameter. Men om du inte vill att din mall ska vara offentligt tillgänglig kan du skydda den genom att lagra den i en privat lagrings behållare. Information om hur du distribuerar en mall som kräver en SAS-token (signatur för delad åtkomst) finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md). Information om hur du går igenom självstudierna finns i [Självstudier: integrera Azure Key Vault i Resource Manager malldistribution](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Distribuera från Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com) för att distribuera mallen. Om du vill distribuera en extern mall anger du mallens URI. Om du vill distribuera en lokal mall måste du först läsa in din mall till lagrings kontot för Cloud Shell. Om du vill ladda upp filer till gränssnittet väljer du Meny ikonen **Ladda upp/ladda ned filer** från fönstret Shell.

Om du vill öppna Cloud Shell bläddrar du till [https://shell.azure.com](https://shell.azure.com)eller väljer **prova-den** från följande kod avsnitt:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Om du vill klistra in koden i gränssnittet högerklickar du inuti gränssnittet och väljer sedan **Klistra in**.

## <a name="pass-parameter-values"></a>Pass parameter värden

Om du vill skicka parameter värden kan du använda antingen infogade parametrar eller en parameter fil.

### <a name="inline-parameters"></a>Infogade parametrar

Om du vill skicka infogade parametrar anger du namnet på parametern med kommandot `New-AzResourceGroupDeployment`. Om du till exempel vill skicka en sträng och matris till en mall använder du:

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

Att hämta ett parameter värde från en fil är användbart när du behöver ange konfigurations värden. Du kan till exempel ange [värden för Cloud-Init för en virtuell Linux-dator](../virtual-machines/linux/using-cloud-init.md).

Om du behöver skicka en matris med objekt skapar du hash-tabeller i PowerShell och lägger till dem i en matris. Skicka matrisen som en parameter under distributionen.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameter-filer

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parameter värden. Parameter filen kan vara en lokal fil eller en extern fil med en tillgänglig URI.

Mer information om parameter filen finns i [create Resource Manager parameter File](resource-manager-parameter-files.md).

Om du vill skicka en lokal parameter fil använder du parametern **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Om du vill skicka en extern parameter fil använder du parametern **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testa mall distributioner

Använd [test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)om du vill testa din mall och parameter värden utan att behöva distribuera några resurser. 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Om inga fel upptäcks slutförs kommandot utan svar. Om ett fel upptäcks returnerar kommandot ett fel meddelande. Om du till exempel skickar ett felaktigt värde för lagrings kontots SKU returneras följande fel:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Om din mall har ett syntaxfel returnerar kommandot ett fel som anger att mallen inte kunde parsas. Meddelandet anger rad numret och positionen för tolknings felet.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md).

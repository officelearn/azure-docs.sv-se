---
title: Distribuera resurser med PowerShell och en mall | Microsoft Docs
description: Använda Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 2ef5cc702bd5035c958a8feb9b6f5051781cd3cc
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649802"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuera resurser med Resource Manager-mallar och Azure PowerShell

Lär dig hur du använder Azure PowerShell med Resource Manager-mallar för att distribuera dina resurser till Azure. Mer information om begrepp för distribution och hantering av dina Azure-lösningar finns i [översikt över Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Distributionsomfattningen

Du kan begränsa distributionen till en Azure-prenumeration eller resursgrupp inom en prenumeration. I de flesta fall kommer du rikta distribution till en resursgrupp. Använda prenumerationsdistributioner för att tillämpa principer och rolltilldelningar i prenumerationen. Du kan också använda prenumerationsdistributioner för att skapa en resursgrupp och distribuera resurser till den. Beroende på omfattningen av distributionen, kan du använda olika kommandon.

Distribuera till en **resursgrupp**, använda [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Distribuera till en **prenumeration**, använda [New AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

I exemplen i den här artikeln används distribution av resursgrupper. Mer information om prenumerationsdistributioner finns i [skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en mall för distribution. Om du inte redan har en, hämta och spara en [exempelmall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) från lagringsplatsen för Azure Quickstart-mallar. Det lokala filnamnet som används i den här artikeln är **c:\MyTemplates\azuredeploy.json**.

Om du använder Azure Cloud shell för att distribuera mallar, måste du installera Azure PowerShell och Anslut till Azure:

- **Installera Azure PowerShell-cmdlets på den lokala datorn.** Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).
- **Anslut till Azure med hjälp av [Connect AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Om du har flera Azure-prenumerationer kan du också behöva köra [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Mer information finns i [använder flera Azure-prenumerationer](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Distribuera lokala mall

I följande exempel skapas en resursgrupp och distribuerar en mall från den lokala datorn. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte avslutas med en punkt.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Det kan ta några minuter att slutföra distributionen.

## <a name="deploy-remote-template"></a>Distribuera fjärråtkomst mall

Istället för att lagra Resource Manager-mallar på den lokala datorn, kanske du föredrar att lagra dem i en extern plats. Du kan lagra mallar i ett källkontrollscentrallager (till exempel GitHub). Eller du kan lagra dem i ett Azure storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en mall för externa, använda den **TemplateUri** parametern. Använd URI: N i det här exemplet för att distribuera exempelmallen från GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

I föregående exempel kräver en offentligt tillgänglig URI för den mall som passar de flesta fall eftersom mallen inte ska innehålla känsliga data. Om du vill ange känsliga data (till exempel ett administratörslösenord) kan du skicka det värdet som en säker parameter. Om du inte vill att mallen för att vara allmänt tillgänglig, kan du dock skydda den genom att lagra det i en privat lagringsbehållare. Information om hur du distribuerar en mall som kräver en signatur (SAS) token för delad åtkomst finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md). Om du vill gå igenom en självstudiekurs, se [självstudien: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Distribuera från Azure Cloud shell

Du kan använda den [Azure Cloud Shell](https://shell.azure.com) att distribuera mallen. Ange URI för mallen för att distribuera en mall för externa. Om du vill distribuera en lokal mall måste du först läsa in mallen till lagringskontot för Cloud Shell. Om du vill ladda upp filer till shell, Välj den **uppladdning/nedladdning filer** menyikonen från fönstret shell.

Öppna cloudshell genom att bläddra till [ https://shell.azure.com ](https://shell.azure.com), eller välj **försök It** från kodavsnittet med följande:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Klistra in koden i gränssnittet genom att högerklicka i gränssnittet och välj sedan **klistra in**.

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *återställning vid fel*. När en distribution misslyckas, kan du automatiskt distribuera om en tidigare lyckad distribution från distributionshistoriken. Om du vill ange omdistribution, kan du använda antingen den `-RollbackToLastDeployment` eller `-RollBackDeploymentName` parameter i distributionskommandot. Den här funktionen är användbar om du har har ett fungerande tillstånd för din distribution av infrastruktur och vill ha detta återställas till. Det finns ett antal varningar och begränsningar:

- Omdistributionen körs exakt som den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Föregående distributionen körs med hjälp av den [fullständig läge](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den föregående distributionen tas bort och alla resurskonfigurationer ställs till sina tidigare tillstånd. Kontrollera att du förstår de [distributionslägen](./deployment-modes.md).
- Omdistributionen påverkar endast resurserna, dataändringar påverkas inte.
- Den här funktionen stöds bara på resursgrupp distributioner, inte prenumerationen på distributioner. Mer information om prenumerationen på distribution finns i [skapa resursgrupper och resurser på prenumerationsnivå](./deploy-to-subscription.md).

Om du vill använda det här alternativet för måste dina distributioner ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn, kan den aktuella misslyckad distributionen skriva över tidigare distributionen i historiken. Du kan bara använda det här alternativet med rot på distributioner. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill distribuera om den senaste distributionen, lägger du till den `-RollbackToLastDeployment` parametern som en flagga.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Om du vill distribuera om en specifik distribution, använda den `-RollBackDeploymentName` parametern och ange namnet på distributionen.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Den angivna distributionen måste ha lyckats.

## <a name="pass-parameter-values"></a>Ange parametervärden

Du kan använda infogade parametrar eller en parameterfil för att ange parametervärden. I föregående exempel i den här artikeln visas infogade parametrar.

### <a name="inline-parameters"></a>Infogad parametrar

Om du vill skicka infogade parametrar, ange namnen på parametern med det `New-AzResourceGroupDeployment` kommando. Till exempel om du vill skicka en sträng och en matris till en mall, använder du:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Du kan också hämta innehållet i filen och ge innehållet som en infogad-parameter.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Hämta ett parametervärde från en fil är användbart när du behöver ange konfigurationsvärden. Du kan till exempel ange [cloud-init värden för en Linux-dator](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen kan vara en lokal fil eller en extern fil med en tillgänglig URI.

Parameterfilen måste vara i följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observera att parameters-avsnittet innehåller ett parameternamn som matchar den parameter som definierats i mallen (storageAccountType). Parameterfilen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa fler än en parameterfil och sedan skicka in lämpliga parameterfilen för scenariot.

Kopiera i föregående exempel och spara det som en fil med namnet `storage.parameters.json`.

Om du vill skicka en lokal parameterfil, använda den **TemplateParameterFile** parameter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Om du vill skicka en extern parameterfilen, använda den **TemplateParameterUri** parameter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Parametern prioritet

Du kan använda infogade parametrar och en lokal parameterfil på samma gång för distribution. Du kan till exempel ange vissa värden i den lokala parameterfilen och lägga till andra värden infogad under distributionen. Om du anger värden för en parameter i både lokala parameterfilen och infogade måste företräde det infogade värdet.

Men när du använder en extern parameterfilen, du kan inte skicka andra värden antingen direkt eller från en lokal fil. När du anger en parameterfil i den **TemplateParameterUri** parametern, alla infogade parametrar kommer att ignoreras. Ange alla parametervärden i den externa filen. Om mallen innehåller något känsligt värde som du inte kan ta i parameterfilen, lägga till detta värde i key vault eller dynamiskt ger alla infogade för parametern-värden.

### <a name="parameter-name-conflicts"></a>Parametern namnet står i konflikt

Om mallen innehåller en parameter med samma namn som en av parametrarna i PowerShell-kommandot, PowerShell visar parametern från din mall med postfixen **från mall**. Till exempel en parameter med namnet **ResourceGroupName** i din mall är i konflikt med den **ResourceGroupName** parametern i den [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet. Du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**. I allmänhet bör du undvika den här förvirring genom att namnge inte parametrar med samma namn som parametrar som används för distributionsåtgärder.

## <a name="test-template-deployments"></a>Testa malldistributioner

Testa din mall- och parameterfilerna värden utan att faktiskt distribuera resurser med [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Om inga fel identifieras måste kommandot har slutförts utan ett svar. Om ett fel upptäcks kan returnerar kommandot ett felmeddelande. Skicka ett felaktigt värde för lagringskontots SKU, returnerar exempelvis följande fel:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Om mallen innehåller ett syntaxfel, returnerar kommandot ett felmeddelande om att det gick inte att parsa mallen. Meddelandet Anger radnumret och positionen för parsningsfel.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Nästa steg

- Om du vill distribuera på ett säkert sätt din tjänst till flera regioner, se [Azure Deployment Manager](deployment-manager-overview.md).
- Om du vill ange hur du hanterar resurser som finns i resursgruppen men inte har definierats i mallen, se [distributionslägen i Azure Resource Manager](deployment-modes.md).
- Information om hur du definierar parametrar i mallen finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md).

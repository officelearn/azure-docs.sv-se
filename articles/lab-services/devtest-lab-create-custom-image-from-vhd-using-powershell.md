---
title: Skapa en Azure DevTest Labs anpassad avbildning från en VHD-fil med hjälp av PowerShell | Microsoft Docs
description: Automatisera skapandet av en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: b43dc668af74f532838dad3baf1d6e11d51ac69d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964087"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Skapa en anpassad avbildning från en VHD-fil med hjälp av PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Steg för steg-anvisningar

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av PowerShell:

1. I en PowerShell-prompt loggar du in på ditt Azure-konto med följande anrop till cmdleten **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Välj önskad Azure-prenumeration genom att anropa cmdleten **Select-AzSubscription** . Ersätt följande plats hållare för variabeln **$subscriptionId** med ett giltigt ID för Azure-prenumeration.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Hämta Lab-objektet genom att anropa cmdleten **Get-AzResource** . Ersätt följande plats hållare för **$labRg** och **$labName** variabler med lämpliga värden för din miljö.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Ersätt följande plats hållare för variabeln **$vhdUri** med URI: n till den ÖVERFÖRda VHD-filen. Du kan hämta VHD-filens URI från lagrings kontots BLOB-blad i Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Skapa den anpassade avbildningen med cmdleten **New-AzResourceGroupDeployment** . Ersätt följande plats hållare för **$customImageName** och **$customImageDescription** variabler till meningsfulla namn för din miljö.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-skript för att skapa en anpassad avbildning från en VHD-fil

Du kan använda följande PowerShell-skript för att skapa en anpassad avbildning från en VHD-fil. Ersätt plats hållarna (som börjar och slutar med vinkelparenteser) med lämpliga värden för dina behov.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Relaterade blogg inlägg

- [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade bilder mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)

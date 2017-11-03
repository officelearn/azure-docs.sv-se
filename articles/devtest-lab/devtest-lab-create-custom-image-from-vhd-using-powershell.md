---
title: "Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell | Microsoft Docs"
description: "Automatisera genereringen av en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a4729f70aae80a13233fbe96a5d8a56c0c9d01d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Skapa en anpassad avbildning från en VHD-fil med hjälp av PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du skapar en anpassad avbildning från en VHD-fil med hjälp av PowerShell:

1. I PowerShell-Kommandotolken logga in på ditt Azure-konto med följande anrop till den **Login-AzureRmAccount** cmdlet.  
    
    ```PowerShell
    Login-AzureRmAccount
    ```

1.  Välj önskad Azure-prenumerationen genom att anropa den **Select-AzureRmSubscription** cmdlet. Ersätt följande platshållare för de **$subscriptionId** variabeln med ett giltigt Azure-prenumerations-ID. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    ```

1.  Hämta lab objektet genom att anropa den **Get-AzureRmResource** cmdlet. Ersätt följande platshållare för de **$labRg** och **$labName** variabler med lämpliga värden för din miljö. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Hämta labbet storage-konto och lab lagring konto nyckelvärden från labb-objektet. 

    ```PowerShell
    $labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Ersätt följande platshållare för de **$vhdUri** variabeln med URI i överförda VHD-filen. Du kan hämta VHD-filen URI från bladet med blob storage-konto i Azure-portalen.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Skapa en anpassad avbildning med hjälp av den **ny AzureRmResourceGroupDeployment** cmdlet. Ersätt följande platshållare för de **$customImageName** och **$customImageDescription** variabler till beskrivande namn för din miljö.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-skript för att skapa en anpassad avbildning från en VHD-fil

Följande PowerShell-skript kan användas för att skapa en anpassad avbildning från en VHD-fil. Ersätt platshållarna (börjar och slutar med hakparenteser) med lämpliga värden för dina behov. 

```PowerShell
# Log in to your Azure account.  
Login-AzureRmAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Relaterade blogginlägg

- [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiera anpassade avbildningar mellan Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Nästa steg

- [Lägga till en virtuell dator i labbet](./devtest-lab-add-vm-with-artifacts.md)

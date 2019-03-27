---
title: Lägg till en artefakt i en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en artefakt i en virtuell dator i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 5e6a7cbc070d81de33fac07a89dabf2b469bd355
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450169"
---
# <a name="add-an-artifact-to-a-vm"></a>Lägg till en artefakt i en virtuell dator
När du skapar en virtuell dator måste du lägga till befintliga artefakter till den. Dessa artefakter kan vara från antingen den [offentliga DevTest Labs Git-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) eller från din egen Git-lagringsplats. Den här artikeln visar hur du lägger till artefakter i Azure-portalen och med hjälp av Azure PowerShell. 

Azure DevTest Labs *artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn etableras, t.ex köra Windows PowerShell-skript, köra Bash-kommandon och installera programvara. Artefakten *parametrar* kan du anpassa artefakten för ditt specifika scenario.

Mer information om hur du skapar anpassade artefakter finns i artikeln: [Skapa anpassade artefakter](devtest-lab-artifact-author.md).

## <a name="use-azure-portal"></a>Använda Azure-portalen 
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Välj labbet som innehåller den virtuella datorn som du vill arbeta i listan över labbar.  
1. Välj **Mina virtuella datorer**.
1. Välj den virtuella datorn.
1. Välj **hantera artefakter**. 
1. Välj **tillämpa artefakter**.
1. På den **tillämpa artefakter** fönstret, Välj den artefakt som du vill lägga till till den virtuella datorn.
1. På den **Lägg till artefakt** fönstret anger du de obligatoriska parametervärdena och parametrar som du behöver.  
1. Välj **Lägg till** att lägga till artefakten och återgå till den **tillämpa artefakter** fönstret.
1. Fortsätt lägga till artefakter som behövs för den virtuella datorn.
1. När du har lagt till artefakterna för, kan du [ändra ordning där artefakter körs](#change-the-order-in-which-artifacts-are-run). Du kan också gå tillbaka till [visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar att lägga till artefakter, Välj **tillämpa**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra ordning där artefakter körs
Som standard utförs åtgärderna för artefakter i den ordning som de läggs till den virtuella datorn. Följande steg illustrerar hur du ändrar den ordning i vilken artefakterna körs.

1. Överst på den **tillämpa artefakter** fönstret, klicka på länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till i virtuell dator](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** fönstret dra och släpp artefakter i önskad ordning. Om du har problem med att dra artefakten kan du se till att du drar från vänster sida av artefakten. 
1. Välj **OK** när du är klar.  

### <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg illustrerar hur du vill visa eller ändra parametrarna för en artefakt:

1. Överst på den **tillämpa artefakter** fönstret, klicka på länken som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som lagts till i virtuell dator](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. På den **valt artefakter** fönstret, Välj den artefakt som du vill visa eller redigera.  
1. På den **Lägg till artefakt** fönstret, se något behov ändringar och välj **OK** att Stäng den **Lägg till artefakt** fönstret.
1. Välj **OK** att Stäng den **valt artefakter** fönstret.

## <a name="use-powershell"></a>Använd PowerShell
Följande skript gäller angivna artefakten till den angivna virtuella datorn. Den [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) kommandot är det som utför åtgärden.  

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzureRmResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzureRmResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar på artefakter:

- [Ange obligatoriska artefakter för ditt labb](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter](devtest-lab-artifact-author.md)
- [Lägg till en artefaktcentrallagret till ett labb](devtest-lab-artifact-author.md)
- [Diagnostisera fel i artefakter](devtest-lab-troubleshoot-artifact-failure.md)
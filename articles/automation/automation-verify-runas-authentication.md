---
title: Validera konfigurationen för Azure Automation-kontot
description: Den här artikeln beskriver hur du bekräftar att konfigurationen av ditt Automation-konto har gjorts på korrekt sätt.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c1ac698a1774a7a6242506ec65193434bb81cc25
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598777"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Testa Kör som-kontoautentisering för Azure Automation
När ett Automation-konto har skapats kan du utföra ett enkelt test för att bekräfta att du kan autentisera i Azure Resource Manager eller den klassiska Azure-distributionen med ditt nyskapade eller uppdaterade Automation Kör som-konto.    

## <a name="automation-run-as-authentication"></a>Automation Kör som-autentisering
Använd exempelkoden nedan för att [skapa en PowerShell-runbook](automation-creating-importing-runbook.md) och verifiera autentisering med hjälp av Kör som-kontot och även i dina anpassade runbookflöden för att autentisera och hantera Resource Manager-resurser med Automation-kontot.

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Lägg märke till den cmdlet som används för att autentisera i en runbook - **Connect-AzureRmAccount**, använder den *ServicePrincipalCertificate* parameteruppsättning.  Den autentiserar med hjälp av tjänstobjektets certifikat, inte autentiseringsuppgifter.  

> [!IMPORTANT]
> **Lägg till AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. Om du inte ser **Connect-AzureRMAccount**, kan du använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

När du [köra runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) verifiera ditt kör som-konto ett [runbook-jobbet](automation-runbook-execution.md) skapas jobbet visas och jobbstatus visas i den **jobbsammanfattning** panelen. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  När runbook-jobbet har slutförts bör du se statusen **Slutfört**.

Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.  På sidan **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla resurser i alla resursgrupper i prenumerationen.  

Kom bara ihåg att ta bort blockeringen av kod med början med kommentaren `#Get all ARM resources from all resource groups` när du återanvänder koden för dina runbookflöden.

## <a name="classic-run-as-authentication"></a>Klassisk Kör som-autentisering
Använd exempelkoden nedan för att [skapa en PowerShell-runbook](automation-creating-importing-runbook.md) och verifiera autentisering med hjälp av det klassiska Kör som-kontot och även i dina anpassade runbookflöden för att autentisera och hantera resurser i den klassiska distributionsmodellen.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

När du [kör en runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) för att verifiera ditt Kör som-konto skapas ett [runbook-jobb](automation-runbook-execution.md), jobbsidan öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  När runbook-jobbet har slutförts bör du se statusen **Slutfört**.

Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.  På sidan **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla resurser i alla virtuella Azure-datorer efter virtuellt datornamn som har distribuerats i prenumerationen.  

Kom bara ihåg att ta bort cmdlet **Get-AzureVM** när du återanvänder koden för dina runbooflöden.

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

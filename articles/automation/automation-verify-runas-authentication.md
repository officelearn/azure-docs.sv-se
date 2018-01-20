---
title: Verifiera konfiguration av Azure Automation-konto | Microsoft Docs
description: "Den här artikeln beskriver hur du bekräftar att konfigurationen av ditt Automation-konto har gjorts på korrekt sätt."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 1413b5d9625ebc2e3b2419f50e7e78be994d8d68
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
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
        Add-AzureRmAccount `
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

Observera cmdleten som används för autentisering i runbooken – **Add-AzureRmAccount** använder *ServicePrincipalCertificate*-parameteruppsättningen.  Den autentiserar med hjälp av tjänstobjektets certifikat, inte autentiseringsuppgifter.  

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

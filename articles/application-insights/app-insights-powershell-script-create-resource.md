---
title: "PowerShell-skript för att skapa en resurs för Application Insights | Microsoft Docs"
description: Automatisera skapandet av Application Insights-resurser.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 376bcb542e4e83c2464d9f3f53ea71965ce79c33
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-skript för att skapa en Application Insights-resurs


När du vill övervaka ett nytt program- eller en ny version av ett program - med [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), du ställer in en ny resurs i Microsoft Azure. Den här resursen är där telemetridata från din app analyseras och visas. 

Du kan automatisera skapandet av en ny resurs med hjälp av PowerShell.

Till exempel om du utvecklar en app för mobila enheter, är förmodligen, när som helst, kommer att flera publicerade versioner av appen används av dina kunder. Du vill inte hämta telemetri resultaten från olika versioner upp. Om du vill skapa din build-processen för att skapa en ny resurs för varje version.

> [!NOTE]
> Om du vill skapa en uppsättning resurser på samma gång bör [att skapa resurser med hjälp av en Azure-mall](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript för att skapa en Application Insights-resurs
Visa relevanta cmdlet-specifikationer:

* [Ny AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-skript*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount / Login-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Vad du gör med iKey
Varje resurs identifieras av dess instrumentation nyckel (iKey). IKey är en utdata från skriptet för att skapa resursen. Skapa skriptet bör ge iKey till Application Insights SDK inbäddade i appen.

Det finns två sätt att tillhandahålla iKey till SDK:

* I [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Eller i [initieringskod](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Se även
* [Skapa Application Insights och testa webbresurser från mallar](app-insights-powershell.md)
* [Konfigurera övervakning av Azure diagnostics med PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Ange aviseringar med hjälp av PowerShell](app-insights-powershell-alerts.md)


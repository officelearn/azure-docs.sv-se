---
title: PowerShell-skript för att skapa en Application Insights-resurs | Microsoft Docs
description: Automatisera genereringen av Application Insights-resurser.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 91790f372dce4322d316b42c4bfa7ad36625c91d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315579"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-skript för att skapa en Application Insights-resurs

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du vill övervaka ett nytt program- eller en ny version av ett program – med [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), du har konfigurerat en ny resurs i Microsoft Azure. Den här resursen är där telemetridata från din app analyseras och visas. 

Du kan automatisera skapandet av en ny resurs med hjälp av PowerShell.

Till exempel om du utvecklar en app för mobila enheter, är det troligt att, när som helst, det är flera publicerade versioner av din app används av dina kunder. Du vill inte få telemetri-resultat från olika versioner upp. Du får den utvecklingsprocessen för att skapa en ny resurs för varje version.

> [!NOTE]
> Om du vill skapa en uppsättning resurser på samma gång kan du överväga att [skapa resurser med hjälp av en Azure-mall](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript för att skapa en Application Insights-resurs
Se relevanta cmdlet-specifikationer:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell Script*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

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


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Vad du gör med iKey
Varje resurs identifieras av dess instrumentationsnyckeln (iKey). Nyckeln är en resurs skapas skriptets utdata. Build-skriptet bör ge iKey till Application Insights SDK inbäddade i appen.

Det finns två sätt att tillgängliggöra iKey till SDK:

* I [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Eller i [initieringskod](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Se också
* [Skapa Application Insights och testa webbresurser från mallar](powershell.md)
* [Konfigurera övervakning av Azure-diagnostik med PowerShell](powershell-azure-diagnostics.md) 
* [Ställ in aviseringar med hjälp av PowerShell](powershell-alerts.md)


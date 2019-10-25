---
title: PowerShell-skript för att skapa en Application Insights resurs | Microsoft Docs
description: Automatisera skapandet av Application Insights-resurser.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820681"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-skript för att skapa en Application Insights-resurs

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du vill övervaka ett nytt program eller en ny version av ett program – med [Azure Application insikter](https://azure.microsoft.com/services/application-insights/), ställer du in en ny resurs i Microsoft Azure. Den här resursen är där telemetri-data från din app analyseras och visas. 

Du kan automatisera skapandet av en ny resurs med hjälp av PowerShell.

Om du t. ex. utvecklar en app för mobila enheter, är det troligt att du när som helst får flera publicerade versioner av din app som används av dina kunder. Du vill inte hämta telemetri-resultatet från olika versioner som är blandade. Så du får skapa en ny resurs för varje version så att du kan skapa en ny resurs.

> [!NOTE]
> Om du vill skapa en uppsättning resurser samtidigt kan du överväga att [skapa resurserna med hjälp av en Azure-mall](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript för att skapa en Application Insights resurs
Se relevanta cmdlet-specifikationer:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-skript*  

```powershell


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

## <a name="what-to-do-with-the-ikey"></a>Vad du kan göra med iKey
Varje resurs identifieras av sin Instrumentation-nyckel (iKey). IKey är utdata från skriptet för att skapa en resurs. Ditt build-skript ska tillhandahålla iKey till den Application Insights SDK Embedded i din app.

Det finns två sätt att göra iKey tillgängligt för SDK:

* I [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Eller i [initierings kod](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Se också
* [Skapa Application Insights-och webb test resurser från mallar](powershell.md)
* [Konfigurera övervakning av Azure Diagnostics med PowerShell](powershell-azure-diagnostics.md) 
* [Ange aviseringar med hjälp av PowerShell](powershell-alerts.md)


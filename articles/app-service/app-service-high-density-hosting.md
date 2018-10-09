---
title: Högdensitetsvärd på Azure App Service med per app skalning | Microsoft Docs
description: Högdensitetsvärd på Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: f2cf472ef3c2c9950dd9f9382009e21fbf62771b
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856793"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Högdensitetsvärd på Azure App Service med per app skalning
Som standard skalas App Service-appar genom att skala den [App Service-plan](azure-web-sites-web-hosting-plans-in-depth-overview.md) de körs på. När flera appar körs i samma App Service-planen, körs varje instans som skalats ut alla appar i planen.

Du kan aktivera *per app skalning* på App Service plan nivå. Skalningen av en app oberoende av App Service-planen som är värd för den. På så sätt kan en App Service plan kan skalas till 10 instanser, men en app kan ställas in att använda endast fem.

> [!NOTE]
> Per app-skalning är endast tillgänglig för **Standard**, **Premium**, **Premium V2** och **isolerad** prisnivåer.
>

## <a name="per-app-scaling-using-powershell"></a>Per app skalning med hjälp av PowerShell

Skapa en plan med per app-skalning genom att skicka in den ```-PerSiteScaling $true``` parametern till den ```New-AzureRmAppServicePlan``` cmdlet.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivera per app-skala med en befintlig App Service Plan genom att skicka in den `-PerSiteScaling $true` parametern till den ```Set-AzureRmAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Konfigurera antalet instanser som appen kan använda i App Service-plan på app-nivå.

Appen är begränsad till två instanser, oavsett hur många instanser som den underliggande app service-planen kan skalas ut till i exemplet nedan.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` skiljer sig från `$newapp.MaxNumberOfWorkers`. Per app skalning använder `$newapp.SiteConfig.NumberOfWorkers` fastställa skala egenskaper för appen.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Per app skalning med Azure Resource Manager

Följande Azure Resource Manager-mall skapas:

- En App Service-plan som är skalas upp till 10 instanser
- en app som är konfigurerad att skala upp till högst fem instanser.

App Service-planen kan du ställa den **PerSiteScaling** egenskap till true `"perSiteScaling": true`. Appen kan du ställa den **antal arbetare** du använder för att 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderade konfigurationen för högdensitetsvärd
Per app-skalning är en funktion som är aktiverad i både Azure-regioner globalt och [App Service-miljöer](environment/app-service-app-service-environment-intro.md). Den rekommenderade strategin är dock att använda App Service-miljöer för att dra nytta av de avancerade funktionerna och större pooler med kapacitet.  

Följ dessa steg om du vill konfigurera hög densitet som är värd för dina appar:

1. Konfigurera App Service Environment och välj en arbetarpool som är dedikerad till hög densitet värdscenario.
1. Skapa en enkel App Service-plan och skala den att använda all tillgänglig kapacitet på arbetarpoolen.
1. Ange den `PerSiteScaling` flaggan TRUE på App Service-planen.
1. Nya appar skapas och tilldelas till den App Service-planen med den **numberOfWorkers** egenskapen **1**. Med den här konfigurationen ger det högsta densitet som är möjliga i den här arbetarpool.
1. Antal arbetare kan konfigureras separat per app för att ge ytterligare resurser efter behov. Exempel:
    - En app med hög användning kan ställa in **numberOfWorkers** till **3** ha flera bearbetningskapacitet för appen. 
    - Låg användning appar skulle ange **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Azure App Service-planer djupgående översikt](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)

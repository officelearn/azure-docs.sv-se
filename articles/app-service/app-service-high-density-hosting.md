---
title: Hög densitet värd i Azure App Service med skalning av per app | Microsoft Docs
description: Hög densitet värd i Azure App Service
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
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961950"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hög densitet värd i Azure App Service med skalning av per app
Som standard skala Apptjänst-appar genom att skala den [programtjänstplanen](azure-web-sites-web-hosting-plans-in-depth-overview.md) de körs på. När flera appar körs i samma App Service-plan, körs varje instans skalats ut alla appar i planen.

Du kan aktivera *per app skalning* planera nivå på App Service. Skalningen av en app oberoende av App Service-plan som är värd för den. På så sätt kan en App Service-plan kan skalas till 10 instanser, men en app kan ställas in att använda bara fem.

> [!NOTE]
> Per app skalning är bara tillgängligt för **Standard**, **Premium**, **Premium V2** och **isolerad** prisnivåer.
>

## <a name="per-app-scaling-using-powershell"></a>Per app skalning med PowerShell

Skapa en plan med per app-skalning genom att passera i den ```-perSiteScaling $true``` attribut till den ```New-AzureRmAppServicePlan``` cmdleten igen

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Uppdatera en befintlig programtjänstplan med skalning av per app: 

- Hämta mål-plan ```Get-AzureRmAppServicePlan```
- ändra egenskapen lokalt ```$newASP.PerSiteScaling = $true```
- skicka ändringarna till azure ```Set-AzureRmAppServicePlan``` 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Konfigurera antalet instanser som appen kan använda i App Service-plan på app-nivå.

Appen är begränsad till två instanser oavsett hur många instanser underliggande apptjänstplan skalas ut till i exemplet nedan.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` skiljer sig från `$newapp.MaxNumberOfWorkers`. Per app skalning använder `$newapp.SiteConfig.NumberOfWorkers` till bestämmer skala egenskaper för appen.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Per app skalning med Azure Resource Manager

Följande Azure Resource Manager-mall skapas:

- En apptjänstplan skalas ut till 10 instanser
- en app som är konfigurerad att skala upp till högst fem instanser.

Programtjänstplanen inställningen i **PerSiteScaling** egenskap till true `"perSiteScaling": true`. Appen inställningen i **antal arbetare** att använda till 5 `"properties": { "numberOfWorkers": "5" }`.

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderad konfiguration för hög densitet värd
Per app skalning är en funktion som är aktiverad i båda globala Azure-regioner och [Apptjänstmiljöer](environment/app-service-app-service-environment-intro.md). Den rekommenderade strategin är dock att använda Apptjänstmiljöer för att dra nytta av de avancerade funktionerna och större pooler med kapacitet.  

Följ dessa steg om du vill konfigurera hög densitet som värd för dina appar:

1. Konfigurera Apptjänst-miljön och välj en arbetspool som är dedikerad till hög densitet värd scenariot.
1. Skapa en enkel App Service-plan och skala och använda den tillgängliga kapaciteten i poolen worker.
1. Ange den `PerSiteScaling` flaggan till true i App Service-plan.
1. Nya appar skapas och tilldelas till den App Service-planen med de **numberOfWorkers** egenskapen **1**. Med den här konfigurationen ger högsta densiteten möjliga i den här arbetspool.
1. Antalet arbetare kan konfigureras oberoende per app för att bevilja ytterligare resurser som behövs. Exempel:
    - En hög användning app kan ange **numberOfWorkers** till **3** har flera bearbetningskapacitet för appen. 
    - Använd låg appar skulle ange **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Azure App Service-planer djupgående översikt](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)

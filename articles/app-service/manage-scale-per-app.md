---
title: Högdensitetsvärd med hjälp av per app skala - Azure App Service | Microsoft Docs
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
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602323"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Högdensitetsvärd på Azure App Service med per app skalning

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du använder App Service kan du skala dina appar genom att skala den [App Service-plan](overview-hosting-plans.md) de körs på. När flera appar körs i samma App Service-planen, körs varje instans som skalats ut alla appar i planen.

*Per app skalning* kan aktiveras på nivån för App Service-planen så att för att skala en app oberoende av App Service-planen som är värd för den. På så sätt kan en App Service plan kan skalas till 10 instanser, men en app kan ställas in att använda endast fem.

> [!NOTE]
> Per app-skalning är endast tillgänglig för **Standard**, **Premium**, **Premium V2** och **isolerad** prisnivåer.
>

Appar tilldelas tillgänglig App Service-plan med en metod för bästa prestanda för en jämn fördelning mellan instanser. När en jämn fördelning inte garanteras, kontrollerar plattformen att två instanser av samma app inte ska finnas på samma App Service-plan-instans.

Plattformen beroende är inte mått för att fatta beslut om worker allokering. Program är ombalanseras bara instanser läggs till eller tas bort från App Service-planen.

## <a name="per-app-scaling-using-powershell"></a>Per app skalning med hjälp av PowerShell

Skapa en plan med per app-skalning genom att skicka in den ```-PerSiteScaling $true``` parametern till den ```New-AzAppServicePlan``` cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivera per app-skala med en befintlig App Service Plan genom att skicka in den `-PerSiteScaling $true` parametern till den ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Konfigurera antalet instanser som appen kan använda i App Service-plan på app-nivå.

Appen är begränsad till två instanser, oavsett hur många instanser som den underliggande app service-planen kan skalas ut till i exemplet nedan.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Per app-skalning är en funktion som är aktiverad i både Azure-regioner globalt och [App Service-miljöer](environment/app-service-app-service-environment-intro.md). Den rekommenderade strategin är dock att använda App Service-miljöer för att dra nytta av de avancerade funktionerna och kapacitet för större App Service-plan.  

Följ dessa steg om du vill konfigurera högdensitetsvärd för dina appar:

1. Ange en App Service plan som Högdensitet plan och skala ut till önskad kapacitet.
1. Ange den `PerSiteScaling` flaggan TRUE på App Service-planen.
1. Nya appar skapas och tilldelas till den App Service-planen med den **numberOfWorkers** egenskapen **1**.
   - Med den här konfigurationen ger högsta möjliga densiteten.
1. Antal arbetare kan konfigureras separat per app för att ge ytterligare resurser efter behov. Exempel:
   - En app med hög användning kan ställa in **numberOfWorkers** till **3** ha flera bearbetningskapacitet för appen.
   - Låg användning appar skulle ange **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Azure App Service-planer djupgående översikt](overview-hosting-plans.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)
